---
author: Léo Rohmer
title: Callout shortcode
date: 2026-04-05
description: "How to use callouts : obsidian vs heyo"
categories:
  - Memo
tags:
  - theme
  - shortcodes
  - markdown
process: false
---
## Obsidian vs Digital Garden
#### Callouts
Obsidian : 
```markdown
> [!note] Note
> Test
> OK
```
Digital Garden : 
```go-html-template
{{</* 
callout 
    kind="note"
    title="Here's something you should probably know"
    content="Even light has a speed"
*/>}}
```

Style Obsidian : 
> [!note]
> Test
> OK

> [!abstract] Abstract, Summary, Tldr

> [!info] Info, Todo

> [!tip] Tip, Hint, Important

> [!success] Success, Check, Done

> [!question] Question, Help, FAQ

> [!warning] Warning, Caution, Attention

> [!failure] Failure, Fail, Missing

> [!danger] Danger, Error

> [!bug]

> [!example]

> [!quote] Quote, Cite

VS Style Digital Garden : 
Info
{{< callout 
    kind="info"
    title="Here's something you should probably know"
    content="Even light has a speed"
>}}

Question
{{< callout 
    kind="question"
    title="What is it?"
    content=""
>}}

Note that `content` can be empty.

Success
{{< callout 
    kind="success"
    title=""
    content="The speed of lights is _exactly equal_ to $c$"
>}}

The `title` can be empty, too.

Alert
{{< callout 
    kind="alert"
    title="Stop messing with me!"
    content="Just tell me the value"
>}}

Nope
{{< callout 
    kind="nope"
    title="Nope"
    content="You should do some research"
>}}


Note
{{< callout 
    kind="note"
    title="Research"
    content="The speed of lights is _exactly equal_ to 299,792,458 m/s"
>}}

## Classique
Pour faire un retour à la ligne, il faut laisser un espace à la fin d'un paragraph et revenir à la ligne.