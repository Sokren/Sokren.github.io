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

```yaml
alias: Notification - Alerte Météo
description: Envoie une notification des alertes actives et informe de la fin des événements météo
triggers:
  - entity_id: sensor.weather_alert
    trigger: state
conditions: []
actions:
  - variables:
      alertes:
        Vent_violent: "{{ state_attr('sensor.weather_alert', 'Vent violent') }}"
        Inondation: "{{ state_attr('sensor.weather_alert', 'Inondation') }}"
        Orages: "{{ state_attr('sensor.weather_alert', 'Orages') }}"
        Pluie_inondation: "{{ state_attr('sensor.weather_alert', 'Pluie-inondation') }}"
        Neige_Verglas: "{{ state_attr('sensor.weather_alert', 'Neige-verglas') }}"
        Grand_froid: "{{ state_attr('sensor.weather_alert', 'Grand-froid') }}"
      alertes_rouges: >
        {% for name, level in alertes.items() -%}{%- if level == 'Rouge'
        %}{{name.replace('_', ' ')}} {% endif -%}{%- endfor %} 
      alertes_oranges: >
        {% for name, level in alertes.items() -%}{%- if level == 'Orange'
        %}{{name.replace('_', ' ')}} {% endif -%}{%- endfor %} 
      alertes_jaunes: >
        {% for name, level in alertes.items() -%}{%- if level == 'Jaune'
        %}{{name.replace('_', ' ')}} {% endif -%}{%- endfor %} 
  - data:
      title: Debug Alerte Météo
      message: >
        **Alertes regroupées par couleur :** - Jaune : {{ alertes_jaunes if
        alertes_jaunes | length > 0 else 'Aucune' }} - Orange : {{
        alertes_oranges if alertes_oranges | length > 0 else 'Aucune' }} - Rouge
        : {{ alertes_rouges if alertes_rouges | length > 0 else 'Aucune' }}.
    action: notify.notify
    enabled: false
  - if:
      - condition: template
        value_template: >-
          {{ alertes_rouges | length > 0 or alertes_oranges | length > 0 or
          alertes_jaunes | length > 0 }}
    then:
      - data:
          title: Alertes Météo Actives
          message: >
            {%- if alertes_rouges | length > 0 -%}Rouge : {{ alertes_rouges +
            "\n" }}{%- endif -%} {%- if alertes_oranges | length > 0 -%}Orange :
            {{ alertes_oranges + "\n" }}{%- endif -%} {%- if alertes_jaunes |
            length > 0 -%}Jaune : {{ alertes_jaunes }}{%- endif -%}
        action: notify.notify
    else:
      - data:
          title: Fin des Alertes Météo
          message: >
            Toutes les alertes météo sont terminées. La situation est revenue à
            la normale.
        action: notify.notify
mode: single
```

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
