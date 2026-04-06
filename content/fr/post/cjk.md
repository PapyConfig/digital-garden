---
author: Lucas David Vadilho
title: CJK Glyphs
date: 2023-12-12
description: 
tags: 
    - theme
    - CJK
categories:
    - heyo
---

Article de test pour les glyphes CJK.

<!--more-->

Dans une [issue GitHub](https://github.com/LucasVadilho/heyo-hugo-theme/issues/2), _@luobo202254_ a signalé que la police par défaut du navigateur pour les caractères chinois ne s'accordait pas bien avec `Computer Modern`. Nous avons testé plusieurs variantes et choisi `FandolKai-Regular` comme police par défaut[^1] pour {{< theme >}}. La police provient de [CTAN](https://ctan.org/tex-archive/fonts/fandol) et est publiée sous licence GNU GPL.

# Exemples[^2]
{{< callout 
    kind="note"
    title="🇨🇳 Chinese"
    content="学聞示市玉玲況代説景映僧。会問供独北読近借織町球通謙打作。平意軍寄自止注主報者極養航強。替長権示新月設済新子転同恋日村興語靖仕張。採収安師主訃崎告戦都書訃。属勲石寄祉夏抵最第名算護友権更。追強済流分量森非街政涯個侵。的沖就秋織年確混蓮楽融当降全。武国常景報後所阪特産格身労漁景情吹約賄初。恨国会手艦標名介前記日開阪希塚。"
    icon="fas fa-language"
    color="#717788"
>}}

{{< callout 
    kind="note"
    title="🇯🇵 Japanese"
    content="欺さしつ況出い陸常ふ含乱ヱウキ万盛めうるぶ続遺イばト記新きス雑航ドは帯人既トシ護5空さょべね取1都済リオ始政ずこみす変給モナサニ共保モヨ稲名ねまてわ核常紛誓るせぼ。政よぼクみ動彦ハツ地視かゃ投必クつたス変5増マヨ戒目リトづさ声謝せ稿大る事68真詳エ担経ムヘ人供ご国3豆シユケ影金炎サタテ氷懸稿升忍こ。"
    icon="fas fa-language"
    color="#717788"
>}}

{{< callout 
    kind="note"
    title="🇰🇷 Korean"
    content="대한민국의 주권은 국민에게 있고. 정당은 헌법재판소의 심판에 의하여 해산된다. 국가는 대외무역을 육성하며. 국회는 상호원조 또는 안전보장에 관한 조약."
    icon="fas fa-language"
    color="#717788"
>}}

# Choose your own fonts


Dans `config.toml`, sous `[params]`, il y a `customCss` — ce paramètre peut pointer vers un fichier CSS qui remplacera les valeurs par défaut.

Par exemple, si vous avez `customCss = ['customCss/my-font-family.css']` dans `config.toml` et que vous créez un fichier `exampleSite/static/customCss/my-font-family.css` contenant :

```css
* {
    font-family: 'sans serif';
}
```

Votre site devrait maintenant ressembler à ceci :

![exampleSite avec une police sans-serif](/images/my-font-family.png)

[^1]: Il est assez simple de modifier les valeurs par défaut — suivez juste les instructions de la section [Choisir vos propres polices](#choose-your-own-fonts).
[^2]: Tous les textes d'exemple ont été générés sur https://generator.lorem-ipsum.info.