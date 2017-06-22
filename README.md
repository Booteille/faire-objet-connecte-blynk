Faire de l'animation avec les objets connectés
============================================

## Objectifs

Cette animation a pour objectif de faire découvrir les objets connectés à un public d'enfants ayant de 10 à 16 ans.
Au cours de l'animation, les enfants devront comprendre ce qu'est un objet connecté et programmer leur premier objet à l'aide de [Blynk](https://www.blynk.cc/).
Si vous découvrez Blynk, vous pouvez retrouver une [documentation en français](https://booteille.github.io/blynk-docs-fr/) aisni qu'un [guide de démarrage](https://booteille.github.io/blynk-docs-fr/#guide-de-demarrage) vous permettant de rapidement vous faire la main avec ce nouvel outil.
Ici, nous présenterons une animation type pour huit enfants.

## Matériel nécessaire

Le matériel nécessaire variera en fonction du type d'objets connectés et du type de public.
Pour l'animation type, vous aurez besoin de :

* 1 **Raspberry Pi 3** (Ou n'importe quel équivalent ayant la possibilité d'agir comme routeur WiFi) avec un [**serveur Blynk**](https://booteille.github.io/blynk-docs-fr/#serveur-blynk) installé et configuré
* 4 **tablettes tactiles** avec l'application Blynk installée
* 1 **tablette tactile** avec un port HDMI (Pour la connecter à une télé ou un projecteur) ou ayant une application permettant de diffuser l'affichage sur un écran externe
* 5 [**ESP8266 Witty Cloud**](https://www.aliexpress.com/item/ESP8266-witty-cloud-development-board-ESP12F-module/32806516490.html)
* 4 **robots type [Petits Bots](https://github.com/julienrat/petitbot/)**
* 1 **écran ou vidéo-projecteur** afin d'y afficher l'écran de la tablette tactile. Vous pouvez utiliser des solutions comme [TeamViewer](http://teamviewer.org/) couplé à un ordinateur si vous n'avez pas la possibilité de brancher votre tablette sur un afficheur.
* 16 **piles AAA** (de préférence rechargeables) pour les Petits Bots
* 1 **Lampe**
* 1 **Wemos D1** ou équivalent, afin de contrôler la lampe avec un relais
* 1 [**Base Shield V2**](https://www.seeedstudio.com/Base-Shield-V2-p-1378.html) ainsi qu'un [**relais Arduino**](https://www.seeedstudio.com/Grove-Relay-p-769.html) ou équivalent.
* 1 **balle** que les robots devront pousser

## Déroulement de l'animation

Durant toute l'animation, vous montrerez les étapes à suivre sur votre tablette reliée à l'écran.
Ce permettra à tous de bien comprendre ce que vous dites et d'avoir ainsi un repère visuel sur les marches à suivre. Le groupe de huit enfants sera séparé en quatre groupes de deux enfants.

### Préparation de l'animation

* Configurez votre Raspberry Pi 3 pour qu'il [agisse comme routeur WiFi](https://frillip.com/using-your-raspberry-pi-3-as-a-wifi-access-point-with-hostapd/)

* Pour ce qui est du serveur de Blynk, assurez-vous d'avoir correctement [installé le serveur Blynk](https://booteille.github.io/blynk-docs-fr/#serveur-blynk) et que la version soit compatible avec la version des applications Blynk utilisées sur les tablettes (mettez à jour l'application sur les tablettes ainsi le serveur Blynk si vous avez un doute.).

* Créez un projet vide par Petit Bot et Witty Cloud

* Chargez les programmes adaptés dans chaque dispositif (n'oubliez pas de changer le jeton d'authentification (Auth Token) pour chaque appareil) :
  **Witty Cloud** :
  ```c++
  /**
   * Ce script est issu des fichiers d'exemple de la bibliothèque Blynk pour Arduino.
   * Assurez-vous d'avoir installé la bibliothèque Blynk au préalable.
   */

  /* Comment this out to disable prints and save space */
  #define BLYNK_PRINT Serial


  #include <ESP8266WiFi.h>
  #include <BlynkSimpleEsp8266.h>

  // You should get Auth Token in the Blynk App.
  // Go to the Project Settings (nut icon).
  char auth[] = "YourAuthToken";

  // Your WiFi credentials.
  // Set password to "" for open networks.
  char ssid[] = "YourNetworkName";
  char pass[] = "YourPassword";
  char host[] = "addresse_ip_de_votre_raspberry_pi"

  void setup() {
   // Debug console
   Serial.begin(9600);

   Blynk.begin(auth, ssid, pass);
  }

  void loop() {
   Blynk.run();
  }
  ```

  **Petit Bot** - Assurez-vous d'avoir [installé les bibliothèques Arduino](https://www.arduino.cc/en/Guide/Libraries) Blynk et Servo :
  ```c++
  /**************************************************************

   **************************************************************/
  #define BLYNK_PRINT Serial    // Comment this out to disable prints and save space
  #include <ESP8266WiFi.h>
  #include <BlynkSimpleEsp8266.h>
  #include <Servo.h>

  // You should get Auth Token in the Blynk App.
  // Go to the Project Settings (nut icon).
  char auth[] = "38c7ad2576324912b76e65c05d62857a";//rouge


  // Your WiFi credentials.
  // Set password to "" for open networks.
  char ssid[] = "apdblynk";
  char pass[] = "dcliclab";
  char host[] = "192.168.3.2";

  /**
   * Servos initial positions.
   * Positions initiales des servos.
   */
  const uint8_t INIT_SERVO_LEFT = 90;
  const uint8_t INIT_SERVO_RIGHT = 90;

  const uint8_t SERVO_LEFT_FORWARD = 180;
  const uint8_t SERVO_LEFT_BACKWARD = 0;
  const uint8_t SERVO_RIGHT_FORWARD = 0;
  const uint8_t SERVO_RIGHT_BACKWARD = 180;

  Servo servo_left;
  Servo servo_right;
  bool is_accelerometer_active = false;

  // Attach virtual serial terminal to Virtual Pin V10
  WidgetTerminal terminal(V10);

  void halt() {
    servo_left.write(INIT_SERVO_LEFT);
    servo_right.write(INIT_SERVO_RIGHT);
  }

  void forward() {
    servo_left.write(SERVO_LEFT_FORWARD);
    servo_right.write(SERVO_RIGHT_FORWARD);
  }

  void reverse() {
    servo_left.write(SERVO_LEFT_BACKWARD);
    servo_right.write(SERVO_RIGHT_BACKWARD);
  }

  void right() {
    servo_left.write(SERVO_LEFT_FORWARD);
    servo_right.write(INIT_SERVO_RIGHT);
  }

  void left() {
    servo_left.write(INIT_SERVO_LEFT);
    servo_right.write(SERVO_RIGHT_FORWARD);
  }

  // Gauche
  BLYNK_WRITE(V0){
    if (param[0]) {
      left();
    } else {
      halt();
    }
  }

  // Droite
  BLYNK_WRITE(V1) {
    if (param[0]) {
      right();
    } else {
      halt();
    }
  }

  // Bas
  BLYNK_WRITE(V2) {
    if (param[0]) {
      reverse();
    } else {
      halt();
    }
  }

  // Haut
  BLYNK_WRITE(V3) {
    if (param[0]) {
      forward();
    } else {
      halt();
    }
  }

  // Toggle Accelerometer
  BLYNK_WRITE(V4) {
    is_accelerometer_active = param.asInt();
    halt();
  }

  BLYNK_WRITE(V5) {
    //acceleration force applied to axis x
    float x = param[0].asFloat();
    //acceleration force applied to axis y
    float y = param[1].asFloat();
    if (is_accelerometer_active){
      if (x > 2) {
        left();
      } else if (x < -2) {
        right();
      } else if (y > 2) {
        reverse();
      } else if (y < -2) {
        forward();
      } else {
        halt();
      }
    }
  }

  void setup() {
    Serial.begin(9600);

    Blynk.begin(auth, ssid, pass, host);

    servo_left.attach(D5);
    servo_right.attach(D6);
    servo_left.write(INIT_SERVO_LEFT);
    servo_right.write(INIT_SERVO_RIGHT);
  }

  void loop() {
    Blynk.run();
  }
  ```

* Si vous utilisez l'utilitaire [Blynk CLI](https://github.com/Booteille/blynk-cli), vous pouvez faire une sauvegarde des projets vides créés au préalable avec la commande `blynk-cli server backup create vide`. Ce vous permettra, si vous enchaînez plusieurs animations, de pouvoir vider rapidement tous les projets créés pendant une animation en restaurant les projets à partir de la dernière sauvegarde. Pour ce faire, il faudra utiliser la commande `blynk-cli server backup restore vide`.

* Mettez en place les dispositifs de manière à ce que chaque groupe puisse avoir un Witty Cloud et un Petit Bot sur lesquels travailler.

* Préparez la lampe pour pouvoir l'allumer et l'éteindre avec Blynk à l'aide du Wemos D1 et du relais.


### Présentation des objets connectés

Dans un premier temps, vous poserez des questions sur ce qu'est un objet connecté et les différentes applications possible. La lampe, que vous aurez connecté au préalable au relais et au Wemos D1, vous permettra de présenter un exemple simple et concret de ce qu'est un objet connecté.

### Création du premier objet connecté

Une fois la notion de ce qu'est un objet connecté assimilée, vous inviterez les enfants à créer leur premier objet connecté.
Les Witty Cloud posés devant chaque groupe (le cinquième vous étant attribué pour les démonstrations), ils devront créer, sur Blynk, l'application permettant de les contrôler.
Pour commencer, indiquez-leur comment contrôler la LED rouge intégrée (Broche D8) avec un [Slider](https://booteille.github.io/blynk-docs-fr/#widgets-controleurs-glissiere-slider).
![Slider + Witty Cloud](http://dcliclab.fr/wp-content/uploads/2017/06/one_slider-1.png)
Ensuite, faites-les créer deux autres Sliders pour les LEDs vertes (D7) et bleues (D6).
![3 Sliders](http://dcliclab.fr/wp-content/uploads/2017/06/full_slider-1.png)
Une fois que les enfants se sont assez amusés avec les trois Sliders, indiquez-leur qu'il existe un moyen plus simple de contrôler une LED et faites-les supprimer et remplacer leurs Sliders par le [ZeRGBa](https://booteille.github.io/blynk-docs-fr/#widgets-controleurs-zergba) où vous leur ferez configurer le rouge sur D8, le vert sur D7, le bleu sur D6.
![Zèbre](http://dcliclab.fr/wp-content/uploads/2017/06/full_slider-1.png)
Souvent, les enfants ne se rendent pas compte qu'il faut, par défaut, relâcher le doigt pour que la couleur se mette à jour. Vous pouvez donc leur faire changer la valeur de [`SEND ON RELEASE`](https://booteille.github.io/blynk-docs-fr/#widgets-parametres-communs-des-widgets-envoyer-au-relachement-send-on-release) de `OFF` à `ON`. Assurez-vous que la valeur de `WRITE INTERVAL` soit sur `100 ms` pour que la couleur change le plus rapidement possible.
Une fois le zèbre ajouté, indiquez-leur qu'il est aussi possible de récupérer des informations à partir d'un objet connecté et faites-leur ajouter un [Graph](https://booteille.github.io/blynk-docs-fr/#widgets-afficheurs-graphique-graph) ou un [Gauge](https://booteille.github.io/blynk-docs-fr/#widgets-afficheurs-jauge-gauge) que vous leur direz de connecter à la broche A0.
![Witty Cloud avec Sélecteur de Couleur et Graphique](http://dcliclab.fr/wp-content/uploads/2017/06/full_witty-1.png)

### Création d'un « Rocket League »

Assignez à chaque enfant un Petit Bot différent puis montrez-leur comment passer d'un projet à un autre sur Blynk en leur faisant sélectionner le projet du Petit Bot qui leur est assigné.
Une fois qu'ils sont sur leur projet, faites-les ajouter quatre [Buttons](https://booteille.github.io/blynk-docs-fr/#widgets-controleurs-bouton-button) pour les quatre directions différentes. Le Button du haut sera sur V3, de droite sur V1, du bas sur V2 et de gauche sur V0.
Une fois les Buttons correctement configurés, faites-les faire un premier essai.
[Petit Bot avec Boutons](http://dcliclab.fr/wp-content/uploads/2017/06/kart_buttons-1.png)

Rapidement, ensuite, vous leur ferez ajouter un cinquième Button, relié à V4, avec un [Accelerometer](https://booteille.github.io/blynk-docs-fr/#widgets-capteurs-accelerometre-accelerometer) configuré sur V5.
Lorsqu'ils appuieront sur le bouton, la valeur de l'accéléromètre sera lue et permettra de contrôler le Petit Bot en fonction de l'inclinaison de la tablette.
![Petit Bot avec Boutons et Accéléromètre](http://dcliclab.fr/wp-content/uploads/2017/06/final_kart-1.png)

Mettez les quatre Bots dans une arène avec la balle au centre, faites deux équipes, indiquez à chacune dans quel camp marquer, puis profitez.
