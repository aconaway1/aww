---
title: "Image Test"
date: 2026-06-10T15:00:00-07:00
draft: false
---

This post demonstrates adding an image as a page bundle resource.

![Image Test](image.svg)

You can also reference this image in templates via Page Resources:

{{< highlight >}}
{{ $img := .Resources.GetMatch "image.svg" }}
{{ $res := $img.Fit "800x" }}
<img src="{{ $res.RelPermalink }}" alt="Image Test">
{{< /highlight >}}
