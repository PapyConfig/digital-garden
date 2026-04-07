---
author: Lucas David Vadilho
title: Post Thumbnail
date: 2023-08-06
thumbnail:
    src: 'images/thumbnail.jpg'
    alt: 'Imi em meio as flores'
    object_position: '50% 100%'
    height: 250px
categories: ["heyo"]
tags: ["theme", "shortcodes"]
images: ['images/thumbnail.jpg']
---

Vous pouvez désormais ajouter des miniatures (thumbnails) à vos articles avec {{< theme >}} !

<!--more-->

Pour ajouter une miniature à un article, il suffit d'ajouter la variable `thumbnail` dans le front-matter. Pour la miniature, vous pouvez définir :

| Paramètre | Description | Valeur par défaut |
| --- | --- | --- |
| `src` | Source de l'image, peut être une URL ou un chemin relatif | -- |
| `alt` | Valeur alt de l'image, chaîne de caractères | `thumbnail` |
| `object_position` | Valeurs passées à la propriété CSS [object-position](https://developer.mozilla.org/en-US/docs/Web/CSS/object-position) | `50% 50%` |
| `height` | Hauteur du conteneur de la miniature [^1] | `250px` |

[^1]: La hauteur par défaut de la miniature est `250px`. Elle est gérée par la variable CSS `--thumbnail-size` et peut être surchargée globalement via du CSS personnalisé.

# Exemple

La miniature de cet article a été générée par la section suivante du front-matter :

```yaml
thumbnail:
    src: 'images/thumbnail.jpg'
    alt: 'Miniature de l'article'
    object_position: '50% 100%'
    height: 250px
```

# Crédits

Imi — le [personnage](https://www.tdvadilho.com/portfolio/?id=imiFlores) sur cette miniature — a été gracieusement fourni par TD Vadilho. Consultez son [site](https://www.tdvadilho.com?utm_source=heyo) et sa [chaîne YouTube](https://www.youtube.com/@TDVadilho) !