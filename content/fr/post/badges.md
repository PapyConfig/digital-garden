---
author: Lucas David Vadilho
title: Badges
date: 2023-08-01
description: Comment ajouter des badges à vos articles
tags: 
    - theme
    - shortcodes
categories:
    - heyo
badges:
    github:
        subject: GitHub
        status: Check it on GitHub
        icon: github
        url: https://github.com/LucasVadilho/heyo-hugo-theme
        color: grey
        label: ""
    colab:
        subject: Colab
        status: Run it on Google Colab
        label: ""
        color: orange
        icon: https://upload.wikimedia.org/wikipedia/commons/d/d0/Google_Colaboratory_SVG_Logo.svg
        url: https://colab.research.google.com/github/GoogleCloudPlatform/vertex-ai-samples/blob/main/notebooks/official/model_monitoring/model_monitoring.ipynb
    kofi:
        subject: kofi
        status: Buy me a coffee ❤️
        icon: kofi
        label: ""
        url: https://ko-fi.com/oioipio
    template:
        flat: false
        subject: subject
        status: status
        label: label
        color: 000
        label_color: pink
        icon: awesome
        url: https://badgen.net/
---

Cet article montre comment ajouter et personnaliser des badges dans vos articles.
<!--more-->

Dans {{< theme >}}, les badges sont générés via [badgen.net](https://badgen.net/). Ils peuvent être créés avec le shortcode `badge` et il est aussi possible d'ajouter plusieurs badges au [résumé de l'article](#post-summary) via le front-matter.

# Shortcode

The shorcode is `{{</* badge */>}}`. You can check all the parameters [here](#configuration).

## Example

Le shortcode suivant :

```go-html-template
{{</* 
badge 
    status="Checkout the Wiki"
    icon=wiki
    label=""
    color=grey
    url=https://www.wikipedia.org
*/>}}
```

Génère :

{{< 
badge 
    status="Checkout the Wiki"
    icon=wiki
    label=""
    color=grey
    url=https://www.wikipedia.org
>}}


# Résumé de l'article

Vous pouvez aussi afficher des badges dans le résumé de l'article ; cela se fait via la variable `badges` du front-matter.

## Exemple

Les badges de cet article ont été générés par le front-matter suivant :

```yaml
---
author: Lucas David Vadilho
title: Badges!
⋮
badges:
    github:
        subject: GitHub
        status: Check it on GitHub
        icon: github
        url: https://github.com/LucasVadilho/heyo-hugo-theme
        color: grey
        label: ""
    colab:
        subject: Colab
        status: Run it on Google Colab
        label: ""
        color: orange
        icon: https://upload.wikimedia.org/wikipedia/commons/d/d0/Google_Colaboratory_SVG_Logo.svg
        url: https://colab.research.google.com/github/GoogleCloudPlatform/vertex-ai-samples/blob/main/notebooks/official/model_monitoring/model_monitoring.ipynb
    kofi:
        subject: kofi
        status: Buy me a coffee ❤️
        icon: kofi
        label: ""
        url: https://ko-fi.com/oioipio
    template:
        flat: false
        subject: subject
        status: status
        label: label
        color: pink
        label_color: 000
        icon: awesome
        url: https://badgen.net/
```

## Configuration

Le tableau suivant présente toutes les manières de paramétrer un badge.

| Paramètre | Description | Valeurs possibles | Valeur par défaut |
|---|---|---|---|
| `status` | Texte du générateur <br/> ou texte affiché à droite | Chaîne de caractères | `status` |
| `subject` | Texte du générateur <br/> ou texte affiché à gauche[^1] | Chaîne de caractères | `subject` |
| `generator` | Générateur de badge | Voir la [documentation de badgen](https://badgen.net/help#generators) | `static` |
| `flat` | Définit le style du badge | `true \| false` | `true` |
| `scale` | Échelle du badge | Nombre | `1` |
| `label` | Texte affiché à gauche[^2] | Chaîne de caractères | Aucun |
| `url` | Transforme le badge en lien | URL | Aucun |
| `icon` | Icône à gauche | [Icônes intégrées](https://badgen.net/help#icons) ou URL d'un SVG | Valeur par défaut de badgen |
| `color` | Couleur du statut | [Couleurs intégrées](https://badgen.net/help#colors) ou code hexadécimal | Valeur par défaut de badgen |
| `label_color` | Couleur du label | [Couleurs intégrées](https://badgen.net/help#colors) ou code hexadécimal | Valeur par défaut de badgen |

[^1]: Serait remplacé par `label` si présent
[^2]: Peut être vide (`""`) si vous souhaitez n'afficher que l'icône