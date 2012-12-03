# Conférence dotJS à Paris le 30 novembre 2012

La conférence [DotJS](http://www.dotjs.eu/) traitait intégralement de Javascript.
Elle se déroulait sur une journée complète suivie d'une autre journée d'ateliers.

De nombreux retour sont consultables sur [Twitter](https://twitter.com/search?q=%23dotjs)
et les slides disponibles par leur auteur.

Certains des sujets traités : [des outils pour les développeurs](https://speakerdeck.com/addyosmani/tooling-for-the-modern-webapp-developer), Javascript et ses WTF, être un "patron" chez Nodejitsu, développer sous NodeJS, [le polymorphisme en JS](http://lanyrd.com/2012/dotjs/scbgcm/), …


# Atelier NodeJS + Arduino (dotJS) le 1er décembre

Cet atelier était organisé par SendGrid et animé par [Swift](https://twitter.com/swiftalphaone).
Le but étant de piloter un Arduino en Nodejs.

Les exercices effectués sont disponibles sur le [dépôt dédié de Swift](https://github.com/theycallmeswift/arduino-101-workshop).


## Prérequis

- Un système Unix (Linux ou Mac OS par exemple) ;
- Un Arduino avec un kit de base ;
- NodeJS installé.


## Déroulement

1. Installation de l'IDE Arduino pour [Mac OS ou Windows](http://arduino.cc/fr/Main/TelechargerArduinoFrancais), ou pour [Linux Ubuntu](http://www.arduino.cc/playground/Linux/Ubuntu)
2. Injection de l'exemple Firmata > SimpleFirmata dans l'Arduino
3. Installation de Firmata et [Johnny-Five](https://github.com/rwldrn/johnny-five): npm install firmata johnny-five
4. Montage d'une [led sur l'Arduino](http://arduino.cc/en/uploads/Tutorial/ExampleCircuit_bb.png) et lancement d'un [script de test dans Johnny-Five](https://github.com/rwldrn/johnny-five/blob/master/eg/board.js)

D'autres exemples ont été testés jusqu'au scripting d'une blague aléatoire envoyée par mail
lorsque l'on appuie sur un bouton de l'Arduino. [Voir le code](https://github.com/vinyll/arduino-playground/blob/master/nodejs/mail-joke.js).
