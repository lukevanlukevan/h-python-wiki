---
title: "Personal Package"
author:  [
	{ name: "Luke Van",
	link: "https://github.com/lukevanlukevan"}
	]
---

# Personal Package

So, when you get deeper and start creating HDAs and scripts, you will notice the default saving location is always relative to some default Houdini location. With the package we are creating, the goal is to have a mirrored skeleton directory of the native Houdini folder, where you can build (and break) things without affeting the default stuff. This also makes it very easy to share and keep track of the chaos you have made.

To start off, create a folder somewhere that suits you. Maybe it's on the work NAS, or on Dropbox. The key point here is that it is not in your Houdini folder. In my case, I will call it `LVTools`.

Then you can jump to your Houdini preferences folder and go into the packages folder and create a new .json file. For organization purposes, I would suggest naming it the same as your package folder. In my case, I now have `LVTools.json`. Inside it, its pretty basic for now:

```json
{
	"env": [
		{
			"LV": "Z:/Assets/LVTools"
		}
	],
	"path": "$LV"
}
```

Now you have 2 things. Houdini will load this folder at startup, and you have a path link that you can access with `$LV` in any path field. When saving things, now its pretty easy to link the relative to this new variable.

If you're really committed, you would initialize a git repo here too and commit and push your changes.
