---
author: Lucas David Vadilho
title: Table of Contents
date: 2023-08-04
tags: 
    - theme
categories:
    - heyo
showToc: true
---

Dans {{< theme >}} nous pouvons utiliser la barre latérale pour afficher une Table des matières !

<!--more-->

# Introduction

Ce billet explique comment afficher une Table des matières (TOC) dans la barre latérale.

Il va sans dire que la TOC est navigable.

# Méthode

1. Ajoutez `showToc: true` dans le front-matter
2. C'est tout

## Exemple

Ce billet contient le front-matter suivant :

```yaml
---
author: Lucas David Vadilho
title: Table of Contents
date: 2023-08-01
showToc: true
---
```

# Niveaux de titres

Dans `config.toml` se trouvent les réglages pour les niveaux, il est donc très facile de les personnaliser. Par défaut on commence à 1 et on termine à 6.

```toml
[markup]
    [markup.tableOfContents]
        startLevel = 1
        endLevel = 6
```

# Niveau 1

## Niveau 2

### Niveau 3

#### Niveau 4

##### Niveau 5

###### Niveau 6

Si vous en arrivez là, vous devriez probablement restructurer votre document (je plaisante).