# Du code acentré

Lors de notre escapade avec [scopyleft](http://scopyleft.fr) dans les Pyrénées pour 3 jours, nous avons décidé de tenter une approche non centralisée de notre travail. L’utilisation qui est faite aujourd’hui des *[Distributed Concurrent Versions System](https://en.wikipedia.org/wiki/Distributed_Concurrent_Versions_System) (DCVS)* est clairement contraire à l’objectif initial de ces outils avec des dépôts centralisés (souvent sur [GitHub](https://github.com/) ou [Bitbucket](https://bitbucket.org)).

Or, il est tout à fait possible d’avoir des échanges de code locaux sans qu’il n’y ait de centre, chaque machine pouvant avoir une version différente du projet à différents stades de développement et incluant des fonctionnalités différentes. Cela peut poser problème dans un contexte de *release* mais ce n’était pas notre objectif principal (et ça reste possible à partir du moment où une machine récupère l’intégralité des fonctionnalités développées sur les autres). Nous avons choisi [Git](http://git-scm.com/) (mais nous aurions pu tout aussi bien utiliser [Mercurial](http://mercurial.selenic.com/)).

Notre premier essai a été de tenter de passer par les dossiers partagés d’OS X mais Git ne permet malheureusement pas d’utiliser le protocole `afp://`, je me suis alors souvenu d’avoir déjà eu à servir mon dépôt Mercurial localement avec la commande `serve` et je me suis mis en quête de trouver [la même commande en Git](http://stackoverflow.com/questions/377213/git-serve-i-would-like-it-that-simple) qui est la suivante :

    $ git daemon --reuseaddr --base-path=. --export-all

qu’il est possible de mettre en alias dans son fichier de configuration global `~/.gitconfig` :

    [alias]
        serve = !git daemon --reuseaddr --base-path=. --export-all ./.git

Notez qu’il est possible d’ajouter l’option `--verbose` pour voir qui récupère quoi sur sa machine.

Une fois le dépôt servi par une machine source, il est possible de s’y connecter directement grâce au nom de la machine, ce qui s’avère plus pérenne si vous êtes en `DHCP` (il faut être sur le même réseau pour la résolution `DNS`, attention aux noms d’hôtes aussi ! La commande `ping` est votre amie pour tester ça) :

    $ git clone git://nom_machine_source/

Ne surtout pas oublier le `/` final sinon la récupération ne pourra pas être effectuée. À partir de là, il est possible d’effectuer l’intégralité des commandes Git. Il peut être utile d’ajouter les machines concernées en `remote` pour éviter d’avoir à re-taper l’URL à chaque fois :

    $ git remote add nom_source git://nom_machine_source/

Une fois cela mis en place, le *workflow* de travail est un peu différent puisqu’il n’y a jamais de `push`, chaque développeur récupérant ce dont il a besoin chez les autres pour avancer sans rien leur imposer.

L’expérience s’est avérée relativement concluante une fois les différentes commandes ci-dessus acquises, et permettra de mettre en pratique un tel *workflow* lors d’un futur `[/dev/fort](http://devfort.com/)` où la connexion est par définition limitée.

*Qu’il est bon d’enfin utiliser un* **D***CVS à bon escient :-)*
