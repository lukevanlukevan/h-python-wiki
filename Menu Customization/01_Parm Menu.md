---
title: "Parm Menu"
author:  [
	{ name: "Luke Van",
	link: "https://github.com/lukevanlukevan"}
	]
---

# Parm Menu

As usual, I always suggest starting with running through the [docs](https://www.sidefx.com/docs/houdini/basics/config_menus.html) to get used the the methods needed to get your ideas into working features.

If you have been interested so far, you probably have some form of personal package set up by now. If not, I suggest you create one so you have the freedom to make changes (and break things).

So, if we create a new file in the root our package folder called `PARMmenu.xml`. This is the name of the file that augments the paramter right click menu.

In this file, paste this code:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<menuDocument>
	<menu>
		<scriptItem id="my_rightclicktool">
			<label>My Rightclick Tool</label>
		</scriptItem>
	</menu>
</menuDocument>
```

When we go back and forth editing the menu, Houdini wont automatically reload the menu. So create a shelf script as shown:

```python
hscript(menurefresh)
```

This runs the hscript command `menurefresh` which reloads the menu xml files.

Now there are 2 directions you can go. The first is the ideal, where you have a python script in your package at `scripts/python/scriptName.py`. This allows you to easily edit individual files rather than the mess of all of the code inside the `PARMmenu.xml` file.

Now we can do something in our script, let's start of with a basic function:

```python
def getParmName():
	// script here
```

the reason we created a function rather than just running raw code in here, is so that we can bundle multiple functions into one script if they are related.

If we add a `print('hello')` into our function, we can link that function in our `PARMmenu.xml` file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<menuDocument>
    <menuBar>
        <separatorItem></separatorItem>

            <scriptItem id="my_rightclicktool">
            <label>My Rightclick Tool</label>
            <scriptCode>
<![CDATA[
import scriptName

scriptName.getParmName()
]]>
            </scriptCode>
            </scriptItem>
        <separatorItem></separatorItem>
    </menuBar>
</menuDocument>
```

You may have noticed the weird indentation in the XML file, this is sadly just how the file needs it. It doesnt play nicely with the indentation level from the XML stuff, and the python stuff needs to start at indent 0.

What we did here is is create a script item with a unique ID. We added a label (what is shown in the menu), and added a `<scriptcode>` tag that contains a small python script that imports our script ('scriptName') and then runs the function we created ('getParmName').

Right now, there is no way for our script file to know any information about the parameter we right clicked on. We can now pass that into our function in the XML file and our scipt.

```python
// before
scriptName.getParmName()

//after
scriptName.getParmName(kwargs)
```

We also need to add this to our script so that we can use the information:

```python
def getParmName(kwargs):
	print(kwargs)
```

Now when we right click, and run our script, we can see all of the information that we can get from the parameter.

A little issue with constantly going back and forth between Houdini and editing the code, is that you may find the script is updating on the Houdini side. A quick fix is adapt the XML script code to have this at the start:

```python
from imp import reload
import scriptName
reload(scriptName)

scriptName.getParmName(kwargs)
```

This uses the reload module to reload the script when the paramter script is run. Ideally you should remove this when shipping to customers or users.

Now when we run the script on the parameter, we get a console popup with all the information. The first bit is named 'parms' and we can access the first (and only) parameter with square brackets. In this case, we want to be getting the parameter name, so first adjust your script as so:

```python
def getParmName(kwargs):
    p = kwargs['parms'][0]
```

Now we have the parameter as a variable, and we can go to the docs for [hou.Parm](https://www.sidefx.com/docs/houdini/hom/hou/Parm.html) and treat it exactly how we would any other script.

Last thing, we want to get the name, obviously!

```python
def getParmName(kwargs):
    p = kwargs['parms'][0]
	name = p.name()
	print(name)
```

And the console says: "scale" (I clicked on the scale parameter). Success!

The last bit I want to share is how to filter based on the parameter type. If you have a script that you only want to show up when clicked on ramps, for example.

in our XML file, we can use the `<expression>` tag to filter based any rules:

```xml
<expression>
parm = kwargs["parms"]
if parm is None:
	return False

return parm[0].parmTemplate().type().name() == 'Ramp'
</expression>
```

The menu item will only be displayed if the expression returns true.

