---
title: Creating a CodePen shortcode for Hugo
slug: /creating-codepen-shortcode/
description: null
author: Buddha
date: '2022-02-02T05:31:36.893Z'
lastmod: '2022-02-02T06:13:33.378Z'
draft: true
tags: []
categories: []
---

Shortcode for hugo are easy to create. We just have to follow basic rules.

https://gohugo.io/templates/shortcode-templates/
<!--more-->

```l {title=true}
layout/shortcodes/codepen.html
```
```l
<p class="codepen" 
{{ if .IsNamedParams }}
    data-height='{{ if .Get "height" }}{{ .Get "height" }}{{ else }}300{{ end }}' 
    data-default-tab='{{ if .Get "tabs" }}{{ .Get "tabs" }}{{ else }}js,result{{ end }}'
    data-slug-hash='{{ .Get "id" }}' 
{{ else }}
    data-height="{{ if .Get 2 }}{{ .Get 2 }}{{ else }}300{{ end }}" 
    data-default-tab="{{ if .Get 1 }}{{ .Get 1 }}{{ else }}js,result{{ end }}" 
    data-slug-hash="{{ index .Params 0 }}" 
{{ end }}
style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
</p>

<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
```