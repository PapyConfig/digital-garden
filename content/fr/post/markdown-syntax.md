+++
author = "Hugo Authors"
title = "Markdown Syntax Guide"
date = "2023-08-07"
description = "Exemple d'article présentant la syntaxe Markdown de base et le formatage des éléments HTML."
tags = [
    "markdown",
    "css",
    "html",
    "themes",
]
categories = [
    "themes",
    "syntax",
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
+++

Cet article propose des exemples de syntaxe Markdown de base utilisable dans les fichiers de contenu Hugo, et montre aussi si les éléments HTML de base sont stylés par le thème.
<!--more-->

## Titres

Les éléments HTML `<h1>`—`<h6>` représentent six niveaux de titres de section. `<h1>` est le niveau le plus élevé, `<h6>` le plus bas.

# H1
## H2
### H3
#### H4
##### H5
###### H6

## Paragraphe

Xerum, quo qui aut unt expliquam qui dolut labo. Aque venitatiusda cum, voluptionse latur sitiae dolessi aut parist aut dollo enim qui voluptate ma dolestendit peritin re plis aut quas inctum laceat est volestemque commosa as cus endigna tectur, offic to cor sequas etum rerum idem sintibus eiur? Quianimin porecus evelectur, cum que nis nust voloribus ratem aut omnimi, sitatur? Quiatem. Nam, omnis sum am facea corem alique molestrunt et eos evelece arcillit ut aut eos eos nus, sin conecerem erum fuga. Ri oditatquam, ad quibus unda veliamenimin cusam et facea ipsamus es exerum sitate dolores editium rerore eost, temped molorro ratiae volorro te reribus dolorer sperchicium faceata tiustia prat.

Itatur? Quiatae cullecum rem ent aut odis in re eossequodi nonsequ idebis ne sapicia is sinveli squiatum, core et que aut hariosam ex eat.

## Citations (blockquote)

L'élément blockquote représente du contenu cité depuis une autre source, éventuellement accompagné d'une citation qui doit être dans un élément `footer` ou `cite`, et éventuellement avec des modifications en ligne comme des annotations.

### Blockquote sans attribution

> Tiam, ad mint andaepu dandae nostion secatur sequo quae.
> **Note** that you can use *Markdown syntax* within a blockquote.

### Blockquote avec attribution

> Don't communicate by sharing memory, share memory by communicating.</p>
> — <cite>Rob Pike[^1]</cite>


[^1]: The above quote is excerpted from Rob Pike's [talk](https://www.youtube.com/watch?v=PAAkCSZUG1c) during Gopherfest, November 18, 2015.

## Tableaux

Les tableaux ne font pas partie du cœur de la spécification Markdown, mais Hugo les prend en charge nativement.

   Name | Age
--------|------
    Bob | 27
  Alice | 23

### Markdown inline dans les tableaux

| Inline     | Markdown  | In                | Table      |
| ---------- | --------- | ----------------- | ---------- |
| *italics*  | **bold**  | ~~strikethrough~~ | `code`     |

## Blocs de code

### Bloc de code avec backticks
Depuis [v0.60.0](https://gohugo.io/news/0.60.0-relnotes/), Hugo prend en charge la coloration syntaxique dans les fences de code par défaut.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Example HTML5 Document</title>
</head>
<body>
  <p>Test</p>
</body>
</html>
```
### Bloc de code indenté avec quatre espaces

    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title>Example HTML5 Document</title>
    </head>
    <body>
      <p>Test</p>
    </body>
    </html>

### Bloc de code avec le shortcode `highlight` interne de Hugo
{{< highlight html >}}
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Example HTML5 Document</title>
</head>
<body>
  <p>Test</p>
</body>
</html>
{{< /highlight >}}

## Types de listes

### Liste ordonnée

1. First item
2. Second item
3. Third item

### Liste non ordonnée

* List item
* Another item
* And another item

### Liste imbriquée

* Fruit
  * Apple
  * Orange
  * Banana
* Dairy
  * Milk
  * Cheese

## Autres éléments — abbr, sub, sup, kbd, mark

<abbr title="Graphics Interchange Format">GIF</abbr> est un format d'image bitmap.

H<sub>2</sub>O

X<sup>n</sup> + Y<sup>n</sup> = Z<sup>n</sup>

Appuyez sur <kbd><kbd>CTRL</kbd>+<kbd>ALT</kbd>+<kbd>Delete</kbd></kbd> pour terminer la session.

La plupart des <mark>salamandres</mark> sont nocturnes et chassent les insectes, vers et autres petites créatures.

