title: Websockets & Python3
author: Nicolas
date: 2012-12-03
published: false

We recently decided to play around with [Websockets](http://websocket.org/) and [Python 3](http://wiki.python.org/moin/Python2orPython3). Choosing Python3 suggested it wouldn't be obvious, because while the situation improves month after month, a big part of the Python ecosystem is [still Python2-compatible only](https://python3wos.appspot.com/). We were wrong!

## Choosing a Python3 compatible Websocket server

Nowadays lots of developers seem doing WebSockets using [Gevent](http://www.gevent.org/), which is unfortunately not officialy ported to Python3 yet — though [some](https://bitbucket.org/jjonte/gevent) [attempts](https://bitbucket.org/Edmund_Ogban/gevent-py3k) [exist](https://bitbucket.org/damoxc/gevent-py3) but haven’t been updated recently, which is a bit scary to us.

[Django](http://djangoproject.com/) has Python3 support [starting with 1.5a1](https://www.djangoproject.com/weblog/2012/oct/25/15-alpha-1/), but has no builtin support for WebSockets, and we didn't found any third-party WebSocket-enabler app compatible with Python3 at the time we searched for one.

So we found [Tornado](http://www.tornadoweb.org/) and [WebSocket-for-Python (ws4py)](https://github.com/Lawouach/WebSocket-for-Python) combined with [CherryPy](http://www.cherrypy.org/), which are both compatible with Python3 out of the box and provide native support for Websockets.

## Our test application: the ScopyChat™

At first we wanted an app as simple as possible to highlight its capabilities and the underlying concepts:

- realtime message sending through websocket, of course
- message broadcasting
- light, simple and illustrative code
- as little dependencies as possible

So we went for a realtime chat Web server. Yeah we know, it's not exactly revolutionary and a bit helloworldish, but the purpose of this blog post is mostly educational.

## [Tornado](http://www.tornadoweb.org/)

Tornado has a [`websocket` module](http://www.tornadoweb.org/documentation/websocket.html) which provides a `WebSocketHandler` class to react on websocket-related events. The cool thing with Tornado — as it is with CherryPy — is that it can serve both HTTP and WebSockets from a single script.

### The backend

Here's the code we started with:

    import json

    from tornado import httpserver, ioloop, web, websocket

    connections = []
    messages = []

    class MainHandler(web.RequestHandler):
        def get(self):
            self.render("test.html")

    class WSHandler(websocket.WebSocketHandler):
        def write_json(self, **kwargs):
            return self.write_message(json.dumps(**kwargs))

        def open(self):
            if not (self in connections):
                connections.append(self)
            self.write_json(type='message', data=messages)

        def on_message(self, data):
            data = json.loads(data)
            for con in connections:
                messages.append(data)
                con.write_json(data)

        def on_close(self):
            if self in connections:
                connections.remove(self)

    application = web.Application([
        (r'/', MainHandler),    # HTML chat homepage
        (r'/chat', WSHandler),  # WebSocket server
    ])

    if __name__ == "__main__":
        http_server = httpserver.HTTPServer(application)
        http_server.listen(8888)
        ioloop.IOLoop.instance().start()

**Note:** The `WebSocketHandler` class is only capable of passing strings or binary data by default; so we naturally used [JSON](http://json.org/) serialization to structure a bit our messages by adding a convenient `write_json()` method.

### The frontend

For our chat frontend, the chat homepage template is very basic, rather ugly but functional:

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8"/>
        <title>ScopyChat</title>
        <style>
        ul#chat {
            width: 100%;
            min-height: 200px;
            background: #eee;
        }
        #post {
            display: none;
        }
        </style>
    </head>
    <body>
        <h1>Welcome to ScopyChat</h1>
        <form id="login">
            <label for="nick">Nick</label>
            <input type="text" name="nick" id="nick">
            <input type="submit" value="Join">
        </form>
        <ul id="chat" data-ws="ws://localhost:8888/ws"></ul>
        <form id="post">
            <label for="message">Post</label>
            <input type="text" name="message" id="message">
            <input type="submit" value="Post">
        </form>
        <script src="http://code.jquery.com/jquery.min.js"></script>
        <script>
        jQuery(function($) {
            var ws, $chat = $('#chat'), nick;
            function log(data) {
                $chat.append('<li>' + (data.nick ? data.nick + ': ' : '') + data.message + '</li>');
            }
            $('#login').on('submit', function(evt) {
                evt.preventDefault();
                ws = window.ws = new WebSocket('ws://localhost:8888/chat');
                ws.onopen = function(evt) {
                    log({message: 'chat server connected'});
                };
                ws.onclose = function(evt) {
                    log({message: 'chat server disconnected'});
                };
                ws.onmessage = function(evt) {
                    var data = JSON.parse(evt.data);
                    if (data.type === 'list') {
                        data.data.forEach(function(entry) {
                            log(entry);
                        });
                    } else {
                        log(data);
                    }
                };
                $('#post').find('label').text('Post as ' + nick);
                $('#post').show();
            });
            $('#post').on('submit', function(evt) {
                evt.preventDefault();
                if (!ws) {
                    return log({message: 'You are not connected.'});
                }
                ws.send(JSON.stringify({
                    type: 'new_message',
                    nick: $('#nick').val(),
                    message: $('#message').val()
                }));
                $('#message').val('');
            });
        });
        </script>
    </body>
    </html>

To run the chat webserver:

    $ python server.py

Connect to `http://localhost:8888/`, you should have a brand new functional relatime websocket-powered chat python3 webserver live, and much more probably a cool mouthful buzzword-bingo sentence to impress your friends with.

![tornado-powered scopychat capture](/static/images/blog/2012/scopychat-demo.png)

## [ws4py](https://github.com/Lawouach/WebSocket-for-Python) with [CherryPy](http://www.cherrypy.org/)

Ws4py is a Python3 compatible *library providing an implementation of the WebSocket protocol defined in RFC 6455*. It offers support for Gevent and CherryPy — the latter being the only one supporting Python3 out of the box, so we went for it.

While the combination works perfectly, we were a bit less impressed by the CherryPy API — essentially because ws4py is then used as a CherryPy plugin — though we may have missed something obvious to make it less complicated to implement.

Our simple CherryPy chat server:

    import random
    import cherrypy

    from ws4py.server.cherrypyserver import WebSocketPlugin, WebSocketTool
    from ws4py.websocket import WebSocket

    cherrypy.config.update({'server.socket_host': '127.0.0.1',
                            'server.socket_port': 8889})
    plugin = WebSocketPlugin(cherrypy.engine)
    plugin.subscribe()

    cherrypy.tools.websocket = WebSocketTool()

    class EchoWebSocketHandler(WebSocket):
        def received_message(self, message):
            cherrypy.log('message: %s' % message.data)
            self.plugin.broadcast(message.data, message.is_binary)

    class Root(object):
        def __init__(self, plugin):
            self.plugin = plugin

        @cherrypy.expose
        @cherrypy.tools.websocket(on=False)
        def ws(self):
            with open("index.phtml") as html_template:
                return html_template.read() % {
                    'username': "User%d" % random.randint(0, 100),
                }

        @cherrypy.expose
        def index(self):
            cherrypy.request.ws_handler.plugin = self.plugin
            cherrypy.log("Handler created: %s" % repr(cherrypy.request.ws_handler))

    cherrypy.quickstart(Root(plugin), '/', config={
        '/': {'tools.websocket.on': True,
              'tools.websocket.handler_cls': EchoWebSocketHandler,
    }})

The HTML template we used, while a bit simpler than the one we used with Tornado, doesn't do much but is sufficient to make the app works:

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8"/>
        <title>ScopyChat</title>
    </head>
    <body>
        <h1>Welcome to ScopyChat</h1>
        <form action="/echo" id="chatform" method="get">
            <textarea id="chat" cols="35" rows="10"></textarea>
            <br />
            <label for="message">%(username)s:</label>
            <input type="text" id="message" />
            <input type="submit" value="Send" />
        </form>
        <script type="application/javascript" src="http://code.jquery.com/jquery.min.js"></script>
        <script type="application/javascript">
        jQuery(function($) {
            var ws = new WebSocket('ws://localhost:8889/');
            ws.onmessage = function (evt) {
                $('#chat').val($('#chat').val() + evt.data + '\n');
                console.log("message recu : ", evt.data);
            };
            ws.onopen = function(evt) {
                ws.send("Hello there");
            };
            ws.onclose = function(evt) {
                $('#chat').val($('#chat').val() + 'Connection closed by server: ' + evt.code + ' \"' + evt.reason + '\"\n');
            };
            $('#chatform').submit(function(evt) {
                evt.preventDefault();
                ws.send('%(username)s: ' + $('#message').val());
                $('#message').val("");
            });
        });
        </script>
    </body>
    </html>

The server is to be launched with `$ python server.py` and the chat server address is `http://localhost:8889/` by default.

## Conclusion

Working with Python3 and WebSockets is definitely feasible, and was for my part easier than initially expected. It was cool to note that the modern version of our favorite language can deal with them without pain already.

Last, WebSockets are fun, easy to play with, nicely supported by modern browsers and may deliver value to your users. So as we did ourselves, keep learning outside from your comfort zone and don't hesitate to give all of this a try!
