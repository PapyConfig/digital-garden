---
author: Lucas David Vadilho
title: Custom JavaScript and CSS
date: 2023-12-11
description: 
tags: 
    - theme
    - customization
    - JS
    - CSS
categories:
    - heyo
---

Avec du CSS et du JavaScript personnalisés, vous pouvez modifier quasiment n'importe quoi dans l'apparence ou le comportement de {{< theme >}}, et c'est très simple !

<!--more-->

Dans `config.toml`, sous `[params]`, il existe deux champs : `customCss` et `customJs`. Ces champs peuvent pointer vers des fichiers qui seront importés lors de la génération du site. Cela permet d'écraser les valeurs par défaut et d'ajouter vos propres fonctionnalités.

{{< callout 
    kind="info"
    title="Vous pouvez utiliser des fichiers distants"
    content="Si vous souhaitez utiliser un fichier distant (depuis un CDN, par exemple), vous pouvez simplement mettre l'URL ici.<br/><br/>Par exemple, si vous définissez <code>customJs = ['https://cdnjs.cloudflare.com/ajax/libs/hi-sven/1.29.0/index.js']</code>, vous devriez voir `Hi Sven?` dans la console DevTools (<kbd><kbd>CTRL</kbd>+<kbd>SHIFT</kbd>+<kbd>I</kbd></kbd>)."
>}}

{{< callout 
    kind="info"
    title="Vous n'êtes pas limité à un seul fichier"
    content="Vous pouvez avoir plusieurs fichiers personnalisés, séparez-les simplement par une virgule, par exemple `customCss = ['customCss/my-styles-1.css', 'customCss/my-styles-2.css']`"
>}}

# Exemple CSS

Si vous souhaitez changer la police par défaut, il suffit de définir `customCss = ['customCss/my-font-family.css']` dans `config.toml` et de créer le fichier `exampleSite/static/customCss/my-font-family.css` avec le contenu suivant :

```css
* {
    font-family: 'sans serif';
}
```

Votre site devrait alors ressembler à ceci :

![exampleSite avec une police sans-serif](/images/my-font-family.png)

# Exemple JavaScript

Supposons que vous avez ceci dans `config.toml` :

```toml
customJs = ['customJs/my-js.js', 'https://cdnjs.cloudflare.com/ajax/libs/hi-sven/1.29.0/index.js']
```

Et que vous créez `exampleSite/static/customJs/my-js.js` avec :

```js
console.info("Hello from my-js.js");
```

Vous verrez alors `Hello from my-js.js` et `Hi Sven?` dans la console DevTools (<kbd><kbd>CTRL</kbd>+<kbd>SHIFT</kbd>+<kbd>I</kbd></kbd>).
