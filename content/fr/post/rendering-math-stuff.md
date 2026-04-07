---
author: Lucas David Vadilho
title: Rendering Math Stuff
date: 2023-08-03
description: Comment configurer la composition mathématique dans heyo
math: true
tags: 
    - theme
categories:
    - heyo
---

Cet article explique comment configurer votre site avec le thème {{< theme >}} pour afficher du $\LaTeX$.

<!--more-->

La composition mathématique est réalisée via des bibliothèques JavaScript. Dans {{< theme >}}, vous pouvez choisir d'utiliser soit [MathJax 3](https://www.mathjax.org/), soit [KaTeX](https://katex.org/).

Ces bibliothèques sont incluses dans le partial `math.html` (`/layouts/partials/math.html`).

## Configuration globale

La composition mathématique peut être activée globalement en définissant le paramètre `math` dans `config.toml`.

- Le paramètre `math.enable = true` active la composition mathématique pour toutes les pages.
- La bibliothèque par défaut est MathJax, mais vous pouvez la remplacer par KaTeX avec `math.use = "katex"`.

## Activation par page

Même si `math.enable = false` dans `config.toml`, vous pouvez composer des formules sur des pages spécifiques en ajoutant `math = true` dans le front-matter de la page.

## Exemples

Dans les exemples suivants, nous utilisons MathJax.

### Mathes inline

MathJax est configuré pour utiliser `$...$` ou `\\(...\\)` comme délimiteurs pour les mathes inline.

```latex
Suddenly we need to define $\varphi = \dfrac{1+\sqrt5}{2} = 1.6180\dots$
```

Sera rendu comme :

Suddenly we need to define $\varphi = \dfrac{1+\sqrt5}{2}= 1.6180\dots$

### Mathes en bloc

Pour les mathes en bloc, les délimiteurs MathJax sont `$$...$$` ou `\\[...\\]`.

```latex
$$
    \varphi = 1+\frac{1}{1+\frac{1} {1+\frac{1} {1+\cdots}}}
$$
```

Sera rendu comme :

$$
    \varphi = 1+\frac{1}{1+\frac{1} {1+\frac{1} {1+\cdots}}}
$$

### Échapper le symbole \\\$

Si vous devez afficher littéralement `\\$` dans un texte, vous pouvez l'échapper avec `\`. Dans un fichier `.md`, il faut échapper le `\`.

Ainsi, si vous avez `A random amount between \\\$5 and \\\$10` dans votre fichier `.md`, après le traitement par Hugo et MathJax, le résultat sera :

A random amount between \\\$5 and \\\$10