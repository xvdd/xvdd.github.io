---
layout: post
title:  "Reduce a pdf file size with Imagemagick"
date:   2022-08-04 19:22:11 +0800
categories: [linux]
tags: []
---
Convert and split the pdf file to image files (on my system, Imagemagick is called with the `convert` command):

```terminal
$ convert -density 100 input_file.pdf -quality 90 page.jpg
$ ls
input_file.pdf
page-0.jpg
page-1.jpg
page-2.jpg
page-3.jpg
page-4.jpg
page-5.jpg
page-6.jpg
page-7.jpg
page-8.jpg
page-9.jpg
page-10.jpg
page-11.jpg
page-12.jpg
```

If the document is more than 10 pages, rename the page files to maintain numeric order:

```terminal
$ for i in $(seq 0 9); do mv page-$i.jpg page-0$i.jpg; done
$ ls
input_file.pdf
page-00.jpg
page-01.jpg
page-02.jpg
page-03.jpg
page-04.jpg
page-05.jpg
page-06.jpg
page-07.jpg
page-08.jpg
page-09.jpg
page-10.jpg
page-11.jpg
page-12.jpg
```

Rebuild the pdf file using the reduced images:

```terminal
$ convert page-* output_file.pdf
```

Adjust density and quality of the first step accordingly to the target file size and expected quality.