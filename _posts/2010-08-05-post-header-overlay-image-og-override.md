---
title: "Post: Overlay Image with OpenGraph Override"
header:
  image: /assets/images/page-header-teaser.png
  teaser: /assets/images/page-header-teaser.png
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"
categories:
  - Layout
  - Uncategorized
tags:
  - edge case
  - image
  - layout
published: true
hidden: true
last_modified_at: 2017-10-26T15:12:19-04:00
---

This post has a header image with an OpenGraph override.

```yaml
header:
  #overlay_image: /assets/images/unsplash-image-1.jpg
  og_image: /assets/images/page-header-og-image.png
  image:
  teaser: /assets/images/page-header-teaser.png
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"
```