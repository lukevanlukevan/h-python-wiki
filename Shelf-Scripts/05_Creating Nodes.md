---
title: "Creating Nodes"
author: [{ name: "lukevanlukevan", link: "www.github.com/lukevanlukevan" }]
---

# Creating Nodes

One of the simplest quality of life boosts is speeding up your common tasks with automation.

Let's create a script we can add to a radial menu that will let us create a new empty 'geo' node and name it in one go. We will also add this to the radial menu that you can use in the network editor.

> At this point, the ideal way to be working with all these python setups is to create a personal package and have it linked into your Houdini like other packages. If you're confused by this, you can read more here. This tutorial uses those methods, so it's good to be familiar.

Open up your IDE of choice, and create a new file in your `package/scripts/python` folder. In my case, I will bundle a bunch of scripts together, so one single Utils file will work. I called mine `LVUtils.py`.

The first thing I do is add `import hou` at the top of the file. This ensure that the code completion registers our functions.
Back to the scripting. Because we are going to use a radial menu, we have access to a bunch of glorius kwargs that tell us info about where we called it from.

```python
import hou

def create_named_node():
	geo_name = hou.ui.readInput("Container name:")[1]
```

Super basic here: we use the ![hou.ui](https://www.sidefx.com/docs/houdini/hom/hou/ui.html) function from the 'Scripted UI' section called `readInput()`

This stores the user input as the variable `geo_name`.

> Note the `[1]` we use here, it returns a dict and we want the second index, for me the first was empty.

```python
import hou

def createNamedGeo(kwargs):
	geo_name = hou.ui.readInput("Container name:")[1]

	path = kwargs['pane'].pwd()
```

> We changed the function to take kwargs as an argument.

Nowe we have a path variable that is reading `pane` from kwargs and using ![pwd](https://www.sidefx.com/docs/houdini/hom/hou/pwd.html) which gives us the path to our current location we called the script from. If you `print` it you can see it changes as we move around nodes.

Full script:

```python
import hou

def createNamedGeo(kwargs):
	geo_name = hou.ui.readInput("Container name:")[1]

	path = kwargs['pane'].pwd()

	new_geo = path.createNode("geo", geo_name)

	net_editor = hou.ui.paneTabOfType(hou.paneTabType.NetworkEditor)

	new_geo.setPosition(net_editor.cursorPosition())
```

We use the `net_editor` bit to get our network editor and get the cursor positon to move the created node to it.

Now we can go to the 'Radial Menus' menu and click 'Network Editor'.

On the left we can add a new Menu and call it what we like, set the hotkey, and now add your scrript from the shelf tool you created.
