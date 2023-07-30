---
title: "Fast Resize with Generic Generator"
author: [
	{ name: "Luke Van",
	link: "https://github.com/lukevanlukevan"},
]
---

# Fast Resize with Generic Generator

If you've ever tried to resize images with the dreaded ROP Composite TOP, you will know the pain of waiting 8 seconds for a single crop from 1:1 to 16:9.

We can use ImageMagick commandline tools to resize the image far faster.

We can start with a file pattern TOP that is reading all images from our directory, like this `$HIP/images/*.png`.

One caveat is that the images don't get passed perfectly to the resize setup, so just enabe the `Output Attribute` parm on the file pattern TOP.

From here, we can drop down an ImageMagick node, set the input source to custom file path and pass our output attriute from above in to this box.

We set the "Operation" to "Convert" and then go down to the extra arguments section.

Set the "Argument Name" to `resize` and set the "Argument Source" to Custom Value. Then we can set the "Arguement Value" to `1920x1080` or whatever values you need. I have this wrapped up in an HDA with a slider for size that lets me quickly chop down images for ImageMagick montages, etc. This is more of an excercise into using custom ImageMagick commands in the extra arguments section. Read more commands [here.](https://imagemagick.org/script/command-line-options.php)
