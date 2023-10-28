---
title: Intercepting Mouse Events
author: [{name: 'Luke Van', link: 'https://github.com/lukevanlukevan'},]
---
# Intercepting Mouse Events

Now that we are set up, we can get down into how to leverage more from this system. If you scroll up and look at how I set up `nodegraphhooks.py` you will see that I have an if statement, that returns either `None, True` or `None, False`. If we look into the definition for the `createEventHandler` function, we see the 2 args are passed off down the line to some mystical Houdini event pipeline. The bottom line is, if we reurn the second arg as True, we are essentially saying "don't use the native event handler, we are doind our own stuff here". I set the if statement up so we dont need to keep adjusting the intial file and restarting Houdini. Now in our `extra.py` we can return True or False wherever we break out, and control what is hijacked and not.

For example:

```python
def do_stuff(uievent):
    if isinstance(uievent, MouseEvent):
        print("Nobody move! Im hijacking this mouse event")
        return True
    else:
        return False
```

Now if we drop down a node, by default it will be selected, and if we try click off it to deselect it... we can't! That is by design, above we returned True if our event is a mouse event. Now we are pretty much just writing up if statements to refine until we only wrap the case we want, otherwise we block half of the native functionality.

Let's say for example that we want to be able to shift + ctrl click a node and create a Null connected to it.

```python
import hou
from canvaseventtypes import *
import nodegraphdisplay as display
import nodegraphview as view
from datetime import datetime


def do_stuff(uievent):
    if isinstance(uievent, MouseEvent):
        if (uievent.eventtype == 'mousedown'):
            ctrl = uievent.modifierstate.ctrl
            shift = uievent.modifierstate.shift
            if (ctrl and shift):
                print("we made it")
    else:
        return False
```

Here we see we can we checked the `modifierstate` from our `uievent` to check which modifiers were down when the event fired.

Let's add that Null functionality:

```python
import hou
from canvaseventtypes import *
import nodegraphdisplay as display
import nodegraphview as view
from datetime import datetime


def do_stuff(uievent):
    if isinstance(uievent, MouseEvent):
        if (uievent.eventtype == 'mousedown'):
            ctrl = uievent.modifierstate.ctrl
            shift = uievent.modifierstate.shift
            if (ctrl and shift):
                try:
                    pos = uievent.mousepos
                    editor = uievent.editor
                    items = editor.networkItemsInBox(pos, pos, for_select=True)
                    node = items[-1][0]

                    name = node.name()

                    name = "OUT_" + name

                    null = node.createOutputNode('null', name)
                    null.moveToGoodPosition()
                    return True
                except:
                    return False
    else:
        return False
```

The fun part of this setup is that it doesnt use the selected node, it grabs the one under your cursor. Nice and snappy!

![](/img/InterceptingMouseEvents/01.mp4)