---
title: "GraphicsMagick batch convert png to jpg"
date: 2022-06-09T14:28:28+0200
draft: false
---

For a recent project I needed to batch convert a large set of png files to jpeg. The trouble was, that some png files had transparency. After conversion the transparent regions showed up as black regions in the jpeg files. It took an embarrassingly long time to solve the issue, hence I'll post the solution here.

``` bash
gm mogrify -resize 25\% -background white -extent 0+0 \\
-output-directory ~/out_dir/ -format jpg *.png
```

The regular `convert` mode of GraphicsMagick has the `flatten` option. The `mogrify` mode does not have this option, hence the `extent` option is used. This `extent` option together with the geometry specifier (`0+0` in this case) will composite the input image on a new background.

# References
* [GraphicsMagick doc](http://www.graphicsmagick.org/GraphicsMagick.html#details-extent)
