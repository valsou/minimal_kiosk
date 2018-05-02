# Minimal Kiosk

**Sommaire**

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Minimal Kiosk](#minimal-kiosk)
	- [Présentation](#prsentation)
		- [Minimal kiosk, c'est quoi ?](#minimal-kiosk-cest-quoi-)
		- [Comment ça marche ?](#comment-a-marche-)
		- [Investissement](#investissement)
	- [Tutoriel](#tutoriel)
		- [Pré-requis](#pr-requis)
			- [Raspbian Lite](#raspbian-lite)
			- [Etcher](#etcher)
		- [Première installation](#premire-installation)
		- [Maintenance de l'affichage](#maintenance-de-laffichage)

<!-- /TOC -->

## Présentation
### Minimal kiosk, c'est quoi ?
*Minimal kiosk* est une solution d'affichage dynamique basée sur des Raspberry Pi. La solution a été développée pour le lycée Emmanuel Mounier de Grenoble dans le but de remplacer un système vieillissant.

> Un Raspberry Pi est un micro-ordinateur destiné en premier lieu à l'apprentissage de l'informatique. Ce dernier dispose de tout le nécessaire tels que des ports USB, un port HDMI, un port ethernet et sur les dernières versions d'un récepteur/émetteur Wi-fi.

L'affichage dynamique est défini par un dispositif d'écrans TV affichant des informations diverses. Dans notre cas nous affichons la date, l'heure, la météo et un diaporama d'informations interne.

Les défauts constatés de l'ancien système étaient :
* les écrans TV étaient reliés à de vieux PC de bureau (lents et energivores).
* il fallait allumer et éteindre les ordinateurs tous les jours.
* il fallait manuellement ouvrir le navigateur pour mettre en plein écran un Google Slide.

La solution envisagée a réglé tous ces points :
* un Raspberry Pi est un micro-ordinateur peu consommateur et suffisant pour l'affichage d'un navigateur
* le système d'exploitation nous permet de mettre en place des actions automatisées (par le biais de scripts), aussi bien l'extinction du navigateur que l'extinction du signal vidéo.

*Minimal kiosk* a été développé dans l'optique de dégager les problèmes techniques de l'équation pour se concentrer sur les informations et le moyen de communiquer avec les visiteurs et les élèves du lycée. De même, ayant bien conscience des moyens aujourd'hui restreints en matière de référents informatiques dans les établissements, j'espère que le tutoriel qui suit sera à la portée de tous.

L'autre point important à ne pas négliger réside dans le coût de la solution ! **Le code est libre** et le micro-ordinateur choisi (Raspberry  Pi) coûte à l'unité 35€. C'est une solution peu coûteuse qui peut permettre à votre établissement une certaine indépendance. Vous évitant des frais parfois démesurés quand il s'agit d'affichage dynamique.

### Comment ça marche ?
Votre téléviseur est branché en HDMI (ou avec un adaptateur) à un Raspberry Pi.
Ce dernier est configuré de sorte à afficher automatiquement au démarrage un navigateur web (Chromium) et une page par défaut.

Cette page est disponible en téléchargement sur Github.com ici-même. Cette page est une page web .html.

Elle a également été écrite en Javascript afin d'avoir une mise à jour asynchrone des différents éléments affichés. Pour rappel, l'écran affiche la date et l'heure (mis à jour toutes les secondes), la météo (mise à jour toutes les 30 minutes) et un Google Slide (mis à jour toutes les 5 minutes).

___

**En tant qu'utilisateur vous n'avez besoin de configurer qu'une première fois votre Raspberry Pi. En préciseant l'URL de votre Google Slide et les coordonées GPS de votre établissement pour la météo.**

Ensuite le Raspberry Pi vivra sa vie. Il s'éteindra à l'heure décidée et il se rallumera à l'heure décidée.
Avant chaque vacances vous pouvez débrancher tout simplement le Raspberry Pi; en le rebranchant à la rentrée il lancera automatiquement Chromium.

La solution peut-être considérée comme "stand-alone". C'est-à-dire qu'elle fonctionne d'elle-même et n'est pas reliée à d'autres de telle sorte qu'un eco-système pourrait se créer. La configuration est unique à chaque Raspberry Pi. *Une solution envisagée pour recenser/contrôler les Raspberry Pi serait de créer un serveur NodeJS sur le même réseau.*

### Investissement
Un affichage dynamique vous en coûtera :
* un écran TV
* un Raspberry Pi (35€)
* une carte microSD >= 4Go
* un lecteur de carte microSD
* un câble HDMI
* un chargeur d'alimentation
* un câble ethernet
* un clavier USB
* et un peu de temps !

En imaginant que le Raspberry Pi tournera 24/7, il vous en coûtera 10 fois moins cher en électricité que si vous y aviez mis un ordinateur de bureau (tournant lui 5/7jours et 10 heures par jour...).

---
## Tutoriel
### Pré-requis
Nous allons installer le système d'exploitation (Raspbian) sur notre carte microSD avant de lancer le Raspberry Pi.

#### Raspbian Lite
Raspbian Lite est le système d'exploitation que nous allons utilisé. Dérivé de Debian et optimisé pour Raspberry Pi, la version Lite est dépourvue d'environnement graphique et nous permettra de partir sur une base légère.

1. Téléchargez [Raspbian Lite](https://www.raspberrypi.org/downloads/raspbian/) sur le site officiel.
`Version testée : Stretch du 18 avril 2018.`

2. Décompressez l'archive zip. Vous allez obtenir une image (.img).
`Exemple : 2018-04-18-raspbian-stretch-lite.img`


#### Etcher
1. Téléchargez [Etcher.io](https://etcher.io/) pour votre OS (macOS / Windows / Linux). Attention à l'architecture CPU (32bits ou 64bits) !
2. Installez Etcher ou lancez-le si vous avec choisi la version portable.
3. Image pour expliquer ! choisir image, choisir disque, flasher.
4. Retirez la carte microSD et insérez-là dans le Raspberry Pi.
5. Branchez le câble ethernet, le clavier USB et l'écran au Raspberry Pi puis branchez l'alimentation.

---
### Première installation

Recopier tutoriel.
---
### Maintenance de l'affichage
Backup d'une carte microSD.
Flasher une carte microSD avec le backup.
Sortir du kiosk mode, éteindre à la main.
Ou éteindre en débrancheant.

Modifier le slide sur Google Slide.
