---
title: "Reading Parameters"
author:  [
	{ name: "Luke Van",
	link: "https://github.com/lukevanlukevan"}
	]
---

# Reading Parameters

When working with a custom HDA, one might find themselves wanting to add more functionality to the parameters, especially if you are handing off this asset to a team who arent interested in digging around, and just need easy access as quick as possible.
This is an example of how to utilize the Scripts portion of an HDA.

In my example, I have built an empty HDA with 2 parameters, one file input and a button with an icon and no label.

![](/img/ReadingParameters/1.png)

We can right click on the node and click on 'Type Properties' and go the 'Scripts' tab. At the bottom left, we can click the 'Event Handler' dropdown and select 'Python Module,' this is the event handler that deals with actions on the HDA.

In the script box on the right, add a function like this: `def OpenDir(kwargs):`

If you've read the other pages, you will know that we can use `kwargs` to get all the extra information from the node.

so if we `print(kwargs)`, we get this beautiful mess:

```python
{'node': <hou.SopNode of type lukevan::Test_HDA::1.0 at /obj/geo1/Test_HDA>, 'parm':
 <hou.Parm openBtn in /obj/geo1/Test_HDA>, 'script_multiparm_index': '-1', 'script_v
alue0': '0', 'script_value': '0', 'parm_name': 'openBtn', 'script_multiparm_nesting'
: '0', 'script_parm': 'openBtn'}
```

If we fish through this, we can see a few bits of use, namely 'node'. So in our script, we can now use the node to get any parm on the node. From here, we have a basic hou.node case here, where the [docs](https://www.sidefx.com/docs/houdini/hom/hou/Node.html) can guide us.

So now, our updated function looks like this:

```python
def OpenDir(kwargs):
    node = kwargs['node']
    input = node.parm('file_input').eval()
```

> Note the use of `eval()` here, otherise we are just getting a reference to the parm. We specifically want to get the value of it, that is why we use `eval()`.

Our initial goal was to open the path in our native explorer, so let's add that last line into our function.

```python
def OpenDir(kwargs):
    node = kwargs['node']
    input = node.parm('file_input').eval()
    hou.ui.showInFileBrowser(input)
```

So now our button will open the location selected in the file input parameter.

