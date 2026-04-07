---
author: Lucas David Vadilho
title: Sketches!
date: 2023-08-07
badges:
    p5:
        subject: p5js
        status: p5js
        color: ed225d
        label: ""
        label_color: grey
        url: https://p5js.org/
        icon: https://upload.wikimedia.org/wikipedia/commons/c/c6/P5.js_icon.svg
tags: 
    - theme
    - p5js
    - javascript
    - creative coding
categories:
    - heyo
---

Les sketches sont les jolies visualisations dans la barre latérale de {{< theme >}}. Les visualisations par défaut ont été créées avec [p5.js](https://p5js.org/), une bibliothèque JavaScript pour le creative coding.

<!--more-->

# Visualisations

Actuellement, nous proposons les visualisations suivantes :

| Sketch | Description |
| ------ | ----------- |
| Graph | Des points animés suivant le bruit de Perlin |
| Digital Rain | Symboles tombants, style Matrix |
| Circles | Dessin de cercles avec un pinceau particulier |

N'hésitez pas à proposer ou contribuer de nouvelles visualisations pour {{< theme >}} !

# Configuration

## Configuration globale

La visualisation (sketch) peut être activée globalement en configurant le paramètre `sketch` dans `config.toml`.

| Param                   | Description                                            |
| ----------------------- | ------------------------------------------------------ |
| `sketch.enable`         | contrôle si la visualisation apparaît sur toutes les pages[^1] |
| `sketch.displayOptions` | contrôle si les options de visualisation sont affichées au survol |
| `sketch.use`            | sélectionne la visualisation à afficher                 |

[^1]: La Table des matières (TOC) prend le pas sur la visualisation

## Activation par page

Même si `sketch.enable = false` dans `config.toml`, vous pouvez activer des visualisations pour des pages spécifiques en configurant le front-matter de la page. Cela permet d'afficher différentes visualisations selon les pages.

# Implémenter votre propre sketch

En utilisant `customJs`, vous pouvez importer votre propre code JavaScript. Pour implémenter votre sketch :

1. Enveloppez votre sketch dans une classe
2. Assurez-vous qu'elle implémente les méthodes `setup` et `draw` (elle peut aussi implémenter `mouseWheel`, `mouseClicked`, et `getSettings`)
3. Ajoutez votre classe à l'objet `SKETCHES` (`SKETCHES['yourName'] = yourClass`)
4. Réglez `sketch.use` sur `yourName`
5. Envisagez d'envoyer une pull request sur {{< theme >}} pour partager votre sketch :heart: