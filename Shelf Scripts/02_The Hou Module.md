---
title: "The Hou Module"
author:  [
	{ name: "Luke Van",
	link: "https://github.com/lukevanlukevan"}
	]
---

# The Hou Module

In the previous example, we used `print()`. This is one of the 'native' python functions.
In most cases, you will be doing some python work where you will be using functions from the source program. In Houdini, all of the functions you could used are available in the `hou` module, which is available already, no need to import it.

If we create a new shelf script and type:

```python
node = hou.selectedNodes()
print(node)
```

If we select a node and run that script, the console pops up and says `(<hou.ObjNode of type geo at /obj/geo1>,)`.

Woo hoo. Now is a good time to open up the [Houdini docs](https://www.sidefx.com/docs/houdini/hom/hou/index.html) for the `hou` module. (Get used to looking at this alot).

Now let's make something useful. A shelf script to open the project directory:

```python
hou.ui.showInFileBrowser(hou.hipFile.path())
```

This script runs a _function_ called `showInFileBrowser`. It's a part of the `hou.ui` module. This module, according to docs is [Module containing user interface related functions](https://www.sidefx.com/docs/houdini/hom/hou/ui.html).
This function takes an argument `(file_path)` which we need to find out dynamically. That can be done with `hou.hipFile` ie. the file we are in, and then we use `.path()` to get the... path.
Note that the last part has an empty bracket pair. This is because it is a function, but it take no arguments.

So combined, the script gets the path of the project file, and shows it to us in the explorer.

