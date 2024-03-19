# Voron-2.4-CBT
Correction logiciel Voron 2.4 CBT de cher Magix Phoenix + améliorations

### Scripts utiles :
  - [Shake&Tune de Klippain par frix-x](https://github.com/Frix-x/klippain-shaketune)
  - [Octoeverywhere](https://octoeverywhere.com/)
  - [Klipper TMC Autotune de andrewmcgr](https://github.com/andrewmcgr/klipper_tmc_autotune)
    
### Mods avec achats :
  - [BTT HDMI 5"](https://fr.aliexpress.com/item/1005005889257573.html?spm=a2g0o.order_list.order_list_main.27.5cdb5e5bHcPF79&gatewayAdapt=glo2fra) pour le mod [HDMI5 case for Voron 2.4 
 de Deady](https://www.printables.com/fr/model/586475-hdmi5-case-for-voron-24-300)
  - [OV5648-Caméra USB 120° Auto Focus](https://fr.aliexpress.com/item/1005003833993617.html?spm=a2g0o.order_list.order_list_main.21.5cdb5e5bHcPF79&gatewayAdapt=glo2fra) pour le mod [Angry CAM USB de chri.kai.in](https://mods.vorondesign.com/details/RYpQW53mtem8Nj1JKqiSQ)
  - [BTT Relay v1.2](https://fr.aliexpress.com/item/32972603577.html?spm=a2g0o.order_list.order_list_main.10.5cdb5e5bHcPF79&gatewayAdapt=glo2fra) à installer avec les fixations [Bigtreetech Relay V1.2 Din Mount de Kenneth Walters](https://www.printables.com/fr/model/445328-bigtreetech-relay-v12-din-mount) et [DIN Rail Bracket - Heat Insert de mmuellerphoto - version standard](https://www.printables.com/fr/model/420009-din-rail-bracket-heat-insert-version)
  - [Bouton Poussoir 12x12x7](https://fr.aliexpress.com/item/1005006384754591.html?spm=a2g0o.order_list.order_list_main.5.5cdb5e5bHcPF79&gatewayAdapt=glo2fra) pour le mod [V2.4 Skirt Buttons de xbst_](https://mods.vorondesign.com/details/7TfCWsFfVYrayrgWemf2Qg), il y a une erreur dans son descriptif. les boutons de 12x12x5 sont trop courts, il faut des boutons de 12x12x7 ou modifier le trou dans ses boutons et les passer de 1,7 mm à 0,5 mm (le fichier étape est fourni)

### Mods sans achats nécessaires :
  - [Voron V2.4 Side Panel Lock de core3d studio](https://www.printables.com/fr/model/253623-voron-v24-side-panel-lock-more-fit-to-frame)
  - [Voron 2.4 CanBus Umbilical de Locki](https://www.printables.com/fr/model/384279-voron-24-canbus-umbilical-mod/files)
  - [Bowden Tube Guide de Galvanic](https://mods.vorondesign.com/details/8CxQeqS1lXhlGphwkyqh7g)
  - [Hidden cable routing z belt cover de Akio](https://mods.vorondesign.com/details/LzEFU0RDHXUarF7y69x2Q)
  - [6mm slot cover de filotto78](https://mods.vorondesign.com/details/ZKJgu7OwMNB1xAKRrYSYeg)
  - [RockNRoll de RockNLol](https://mods.vorondesign.com/details/tiIhFDTh9tHJY0JNJK9A)
  - [Removable Doors de ElPoPo](https://mods.vorondesign.com/details/WqhhKrXksAZ4omhHS1RY4Q)

### Installation du BTT relay :
Cette ajout nécéssite l'utilisation d'un bouton poussoir pour la mise en marche de l'imprimante ainsi que de reflasher le M8P

  - Dans un premier temps il nous faut flasher l'imprimante en lui demandant de démarrer avec un pin activé (celui de l'automatien du relay, non documenté cher BTT...)

    [Voir la procedure MAJ de Magic Phoenix](https://mpx.wiki/Firmware-Flash/Updating_Manta_klipper_firmware_remotely) avec l'activation de la pin

    ![menuconfig screen 1](https://github.com/Didier-T/Voron-2.4-CBT/blob/main/Images/Capture%20d'%C3%A9cran%202024-03-17%20165709-2.png)

    ![menuconfig screen 2](https://github.com/Didier-T/Voron-2.4-CBT/blob/main/Images/Capture%20d'%C3%A9cran%202024-03-17%20165626.png)

  - [Puis activer votre RPI en tant que MCU secondaire](https://github.com/Klipper3d/klipper/blob/master/docs/RPi_microcontroller.md)
  - Il ne reste plus qu'a brancher

    pour le bouton poussoir et l'alimentation se référer a la cature suivante
    
    ![brochage BTT relay](https://github.com/Didier-T/Voron-2.4-CBT/blob/main/Images/s-l1600-7-1_3.jpg)
    
    pour l'extinction auto et l'autoalimentation nous allons utiliser les broches 4 et 6 (alimentation) ainsi que les broches 7 et 9 (signal d'arrêt)
    
    ![broche GPIO CB1](https://github.com/Didier-T/Voron-2.4-CBT/blob/main/Images/bigtreetech-40-pin-gpio-cm4-cb1-mapping-2.png)
    
    Les broches 4 et 6 sont a connecter aux broches 5v et GND. Les broches 7 et 9 sont à connecter aux broche S et G de "Automatique shutdown after printing signal line"

  ### Mise en place de l'extinction via l'écran (bouton en bas a droite)

    - il va falloir se connecter au terminal via ssh (pour rappel id:biqu mdp:biqu)
    - ensuite nous allons créer un fichier shutdown.service
    
``` shell
sudo nano /etc/systemd/system/shutdown.service
```

    - puis copier le text suivant (clic droit pour coller dans nano)
    
```cfg
[Unit]
Description=Arret machine

[Service]
Type=oneshot
User=biqu
RemainAfterExit=yes
ExecStop=gpioset 0 71=0

[Install]
WantedBy=multi-user.target
```

    - Ctrl+x pour fermer, pensez à valider l'enregistrement (y puis touche entrée)
    - Ensuite nous activons notre service

```shell
sudo systemctl enable shutdown.service
sudo systemctl start shutdown
```
