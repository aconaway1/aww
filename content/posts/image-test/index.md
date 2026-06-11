---
title: "Image Test"
date: 2026-06-10T15:00:00-07:00
draft: false
---

This post demonstrates adding an image as a page bundle resource.
You can use the `responsive` shortcode to generate responsive images with WebP support.

{{< responsive "image.svg" alt="Image Test" >}}

You can also access the image in templates using Page Resources:

```gotemplate
{{ $img := .Resources.GetMatch "image.svg" }}
{{ $res := $img.Fit "800x" }}
<img src="{{ $res.RelPermalink }}" alt="Image Test">
```
