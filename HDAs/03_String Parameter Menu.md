---
title: "String Parameter Menu"
author: [{ name: "WaffleBoyTom", link: "https://github.com/WaffleBoyTom" }]
---

# String Parameter Menu

When using nodes in Houdini you'll often come across these handy menus that usually come with string parameters.

![](/img/StringParameter/1.png)

Let's look at how we can DIY that.

Let's create a new string parameter and head to the menu tab of the parameter description.

![](/img/StringParameter/2.png)

We'll tick _Use Menu_ and set it to _Replace (Field + Selection Menu)_. You can experiment with other options but this is usually the one you want.

Now you can write your own menu by setting a token then a label. That's cool and all but this is Houdini, we'd like something procedural. Let's use the menu script tab to write some python instead !

The script is pretty much a callback that runs every time you click the button. The callback needs to return a list that's built exactly like a regular menu:
_(token1,value1,token2,value2,token3,value3)_

In this example we'll get a list of directories. Let's say my hip is _/home/what/projects/pythonstuff.hip_. I want to get all the directories that live in _/home/what/projects_, my hip's parent directory.

Let's look at our code ![](/img/StringParameter/4.png)

```python
import os
from pathlib import Path

basedir = hou.expandString("$HIP")

parent = Path(basedir).parent

menuitems = list()

dirs = os.listdir(parent)

for dir in dirs:

    menuitems.append(dir)
    menuitems.append(dir)

return menuitems
```

We're getting the path to the HIP with `hou.expandString('$HIP')` . We could do the same with `hou.getenv('HIP')`. Then we're getting the parent directory with `Path.parent`. We could also use `os.path.split()[-1]`. Many ways to skin a cat ; this example focuses on how you can create the menu, not so much on which methods/packages you should use. We're then creating a empty list. `os.listdir()` gives us a list of the directories contained within _parent_. We then iterate through the list and add the item to the menu. Why twice ? A menu needs a token and a label. In this case, we want them to have the same value. Let's illustrate this by reworking the loop.

```python
for index,dir in enumerate(dirs):

	menuitems.append(str(index))
	menuitems.append(dir)
```

In this example, the token would be : 0,1,2,... and the value would be each directory. So clicking on the menu item would return :0,1,2.

![](/img/StringParameter/5.png)

This might be something you want but most of the time you want the values to match.

If you want to check how more menus are implemented, the Labs or SideFx HDAs are a good place to start.

