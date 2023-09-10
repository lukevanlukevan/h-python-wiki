---
title: "External Code Editor"
author:  [
	{ name: "Luke Van",
	link: "https://github.com/lukevanlukevan"}
	]
---

# External Code Editor

My IDE of choice is VS Code. You know the drill here, pick the one you like the most, google things that don't line up!

First thing: Open up your `settings.json` file and add this.

```json
// paths depend on your Houdini installation
"python.analysis.extraPaths": [
	"C:\\Program Files\\Side Effects Software\\Houdini 19.5.303\\houdini\\python3.9libs",
	"C:\\Program Files\\Side Effects Software\\Houdini 19.5.303\\python39\\lib\\site-packages-forced"
]
```

Now you can edit python and have access to the `hou` autocompletes which is helpful.
