---
layout: posts
title:  Gérer les Notifications d'Alerte Météo dans Home Assistant
categories:
  - Blog
tags:   [HomeAssistant, HA, AlerteMeteo, MeteoFrance]
---

## Introduction

Home Assistant est une plateforme puissante pour automatiser votre maison connectée. Si vous utilisez le plugin Météo-France, il est possible de configurer une automatisation qui vous envoie des notifications lorsque des alertes météo importantes surviennent. Dans ce billet, nous allons créer une automatisation qui regroupe les alertes actives par niveau (Jaune, Orange, Rouge) et envoie une notification appropriée.

## Prérequis

- Home Assistant installé et configuré.
- Plugin Météo-France configuré, par exemple sensor.weather_alert.
- Savoir configurer le service de notification (par exemple, l'application mobile Home Assistant, Telegram, ou autre).

## Le Code de l'Automatisation

Voici le code YAML complet de l'automatisation. Copiez-le dans votre interface Home Assistant (section Automatisations).

code

## Explications

1. **Déclencheur** :
   - L'automatisation se déclenche à chaque changement d'état du capteur `sensor.weather_alert`.

2. **Variables** :
   - Les niveaux d'alerte pour chaque phénomène météorologique sont extraits via `state_attr`.
   - Les alertes actives sont triées par niveau (Rouge, Orange, Jaune).

3. **Actions** :
   - Une notification de débogage (`Debug Alerte Météo`) permet de vérifier que tout fonctionne.
   - Si une alerte est active (Rouge, Orange ou Jaune), une notification contenant les alertes en cours est envoyée.
   - Sinon, une notification informe que les alertes météo sont terminées.

4. **Format des Messages** :
   - Les alertes sont regroupées par couleur avec une liste claire pour chaque niveau et organisé par gravité (Rouge -> Orange -> Jaune)

## Personnalisation

- Modifiez le titre ou le contenu des notifications selon vos préférences.
- Remplacez `notify.notify` par le service de notification que vous utilisez (par exemple, `notify.mobile_app_mon_telephone`).
