---
title: Creating a CodePen shortcode for Hugo
slug: creating-codepen-shortcode-hugo
description: null
author: Buddha
date: '2022-02-02T05:31:36.893Z'
lastmod: '2022-02-10T21:53:49.054Z'
draft: true
tags: []
categories: []
---

Shortcodes simplify creating pages that needs lot of repititive html code in a a website built using hugo. Typically, if you want to insert a flickr image you need to write following html code in a md or any other template file you create. If flickr changes the embed script after a few years, we need to go back and change each one of the usages. Shortcodes allows us to modularize and write neat markdown/or any markup files you may use. If you are familiar with Hexo, they are analogous to Tag Plugins.

<!--more-->

```html
<figure class="wp-caption alignnone">
    <a data-flickr-embed="false" data-header="false" data-footer="true" onclick='return false'
      data-context="false"
      href="URL_OF_IMAGE"
      title="TITLE_OF_IMAGE">
      <img src="URL_OF_IMAGE" width="WIDTH" height="HEIGHT" alt="ALT TEXT OF IMAGE" />
      </a><script async src="//embedr.flickr.com/assets/client-code.js" charset="utf-8"></script>
      <figcaption class="wp-caption-text">CAPTION FOR THE IMAGE</figcaption>
</figure>
```

However, writing so much code everytime we want to embed a flickr image makes the template or content file look ugly and it is tough to remember so much code or we have to always copy page the code. However, short codes allows us to create reusable short cuts to add. For example, the same flickr code in this blog is generated through flickr short code I created my self. My flickr shortcode also allow me to pass width and height if I have to change them in some cases. This makes it easy for me to focus on writing rest of the blog. And keep the markup clean.
So, with the flickr shortcode, my markdown file will have following markup.

```md {title=true}
content/posts/my-post.md
```
```java
{{%/* flickr "Browser Storages"
           "https://www.flickr.com/photos/140760885@N04/49959194511/in/dateposted/"
           "https://live.staticflickr.com/65535/49959194511_65bced3703_z.jpg"
 */%}}
```

In this post, we won't see how to create a flickr short code, let us see how to create a custom shortcode for embedding codepens from https://codepen.io in our static websites with ease.

<!--more-->

## Creating a Simple ShortCode
This may sound like a complex endeavour to abstract complex html code using simple shortcodes. However, creating a shortcode for any of your needs in hexo is quite simple. You just need to create one html file. Name of the file should match with the shortcode. For example if we want to create a shortcode for with name `NEW_SHORTCODE`, we just need to create `NEW_SHORTCODE.html` in folder `layouts/shortcodes`

The content of the html file can be anything we want to replace the short code when the html file is rendedered. The code to embed a codepen with id `ABCXYZ` is as follows. This will need to be saved in file `codepen.html`

```html {title=true}
layouts/shortcodes/codepen.html
```
```html {linenos=true}
<p class="codepen" 
    data-height="300" 
    data-default-tab="js,result" 
    data-slug-hash="ABCXYZ" 
    style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
</p>

<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

```

### Using the shortcode

Once this file is created, please restart hugo server before this can be used. In order to use the shortcode that has just been created, we use the format , so in the markdown file we just use either 
```md {linenos=false}
{{</* codepen */>}}
``` 
or 
```md {linenos=false}
{{%/* codepen */%}}
```

Both the syntaxes have a slight different meaning. Shortcodes that use `%` 

## Passing parameters to ShortCode

Above code will successfully render the codepen in the html. However you may see a problem, this shortcode will only work for the pen with id `ABCXYZ`, but to make it truly reusable, we need to ensure we can use the codepen for different pen ids. In order for us to do it, we need to parametrize the shortcode. We can create two types of parameters in a short code. Both have their own purposes. 

### Indexed parameters

### Named parameters

## ShortCodes that accept both named and index parameters 


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