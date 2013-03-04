## Localiser les points sensibles pour sécuriser les clients

### Points sensibles

* stratégie : mails, documents confidentiels
* données : base de données existante, contenus soumis par des utilisateurs
* code : dépôt centralisé distant, serveur de staging interne

### Cas concrets

* Vincent se fait voler son ordinateur lors d'une conférence.
* David se fait voler son téléphone dans la rue.
* Stéphane se fait cambrioler son NAS.
* Nicolas perd son sac contenant le disque dur de sauvegarde d'un client.
* Vincent se fait hacker le mot de passe de ses emails.
* David se fait hacker le mot de passe de son compte google.
* Stéphane se fait hacker le mot de passe de la banque.
* Nicolas se fait hacker le mot de passe Bitbucket.
* Dropbox est sujet à une faille de sécurité.
* Un stagiaire de l'hébergeur de staging est trop curieux.
* Un serveur Github se fait voler dans leur datacenter.
* Un disque du NAS grille.

### Pistes de résolutions

* chiffrement des disques durs et emails
* accès restreint aux périphériques par mot de passe (portables, macbooks)
* accès restreint aux serveurs par clé SSH (NAS, staging)
* sauvegardes distantes chiffrées
* se limiter à des données sur le sol français pour avoir un recours juridique

**Communiquer au client les mesures prises et leurs limites en début de projet.**
