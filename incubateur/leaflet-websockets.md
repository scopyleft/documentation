# Des cartes dynamiques avec Leaflet

L'application développée lors de notre retraite montagnarde avec [scopyleft](http://scopyleft.fr) utilisait la cartographie et nous sommes partis sur [Leaflet](http://leafletjs.com/) qui semble être la référence actuelle lorsque l'on souhaite utiliser autre chose que des Google Maps. Très vite, on s'est rendu compte que la bibliothèque était très puissante et allait nous faire gagner pas mal de temps.

On commence par initialiser la carte :

    var map = L.map('map');
    map.locate().on('locationfound', function (evt) {
        map.setView([evt.latlng.lat, evt.latlng.lng], 14);
    });
    map.on('locationerror', function (evt) {
        map.setView([43.654388, 3.266995], 10);
    });

    L.tileLayer('http://serveur_de_tuiles/{z}/{x}/{y}.png', {
        maxZoom: 18
    }).addTo(map);

Rien de compliqué ici, on se sert de la géolocalisation via l'API HTML 5 pour centrer la carte sur la localisation du visiteur avec un niveau de zoom permettant de reconnaître le lieu ou l'on affiche la moitié sud de la France lorsque cette fonctionnalité n'est pas disponible.

L'application consistant à afficher les marqueurs des nouveaux arrivants via des `websockets`, il fallait redimensionner la cartes à chaque nouvelle position ou mouvement grâce à la fonction `fitBounds` :

    var markersCoordinates = [];
    markers.forEach(function(marker) {
        markersCoordinates.push([marker.data.lat, marker.data.lon]);
    });
    map.fitBounds(markersCoordinates);

Les marqueurs étaient stockés afin de les mettre à jour en fonction du déplacement de l'utilisateur. L'API `localstorage` nous a permis de conserver les informations personnelles de l'utilisateur afin qu'il n'ait pas à ressaisir inutilement ses coordonnées en situation de mobilité :

    localStorage.setItem('userId', id);
    $('#user_id').val(localStorage.getItem('userId'));

La communication en temps-réel entre les différents participants était assurée par les `websockets` de manière native (en pratique il faudrait utiliser des surcouches comme [socket.io](http://socket.io/) pour pallier aux obsolescences de certains navigateurs mais nous souhaitions rester en mode "plaisir") :

    var ws = new WebSocket(wsurl);
    ws.onmessage = function(evt) {
        var data = JSON.parse(evt.data);
        updateMarker(data);
    };
    ws.send(JSON.stringify({
        lat: user.lat,
        lon: user.lon,
        id: user.id,
    }));

Encore une fois l'usage restait basique et donc l'implémentation relativement simple. Au final en quelques lignes de JavaScript on arrive à avoir un affichage de carte dynamique qui répond bien à l'idée que l'on se faisait de notre prototype d'application, le plus compliqué pour moi aura été de générer un code propre mais heureusement Nicolas et Vincent veillaient au grain ce qui m'a permis d'apprendre beaucoup de concepts jusque là ignorés grâce à nos itérations en binômes. Vive l'apprentissage coopératif :-).