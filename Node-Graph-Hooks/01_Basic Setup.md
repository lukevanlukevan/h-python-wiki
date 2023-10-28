---
title: Basic Setup
author: [{name: 'Luke Van', link: 'https://github.com/lukevanlukevan'},]
---
# Basic Setup

Provided we have a [Personal Package](https://www.houpywiki.com/Personal-Pipeline#personal-package) set up, you can create a python3.9libs folder, and inside that, a nodegraphhooks.py file.

There is a [writeup from SideFX](https://www.sidefx.com/docs/houdini/hom/network.html) on some basics, but I found the docs to be a little thin when searching for info on this part.

Back to `nodegraphhooks.py`, you need to setup up and event handler that gets passed an event from Houdini, which accepts a uievent and pending actions as its arguments.

```python
import hou
from canvaseventtypes import *

def createEventHandler(uievent, pending_actions):
	return None, False

```

This is one of those great Houdini things that needs a restart every time, so for live devving, I suggest setting it up as follows:

```python
import hou
from canvaseventtypes import *
import nodegraphdisplay as display
import nodegraphview as view

import extra
from importlib import reload


def createEventHandler(uievent, pending_actions):
    reload(extra)
    if extra.do_stuff(uievent):
        return None, True
    else:
        return None, False

```

Because of the reload function, we can now create an `extra.py` next to the `nodegraphhooks.py` and do all our work there and have it update.

Inside of `extra.py` I have set up the `do_stuff` function and now we can get started.

```python
import hou
from canvaseventtypes import *
import nodegraphdisplay as display
import nodegraphview as view
from datetime import datetime


def do_stuff(uievent):
    if isinstance(uievent, MouseEvent):
        print("there is a mouse event")
        return False
```

If we load up Houdini now and move out mouse around in the network view, you will see lines being printed in the console. Now that we have connected ourselves, we can experiment with what info we get. One thing to note is that the mouse moved event is fired very often, so I will just set up a condition so we miss that bit.

```python
import hou
from canvaseventtypes import *
import nodegraphdisplay as display
import nodegraphview as view
from datetime import datetime


def do_stuff(uievent):
    if not uievent.eventtype == 'mousemove':
        print(uievent)

    return False
```

If we click once and press a key on the keyboard, we can get these two to analyze:

```
MouseEvent(editor=<hou.NetworkEditor panetab7>, eventtype='mouseenter', selected=NetworkComponent
(item=None, name='invalid', index=0), located=NetworkComponent(item=None, name='invalid', index=0
), mousepos=<hou.Vector2 [142, 670]>, mousestartpos=<hou.Vector2 [142, 670]>, mousestate=MouseSta
te(lmb=0, mmb=0, rmb=0), dragging=0, wheelvalue=0, modifierstate=ModifierState(alt=0, ctrl=0, shi
ft=0), time=183989.407)

KeyboardEvent(editor=<hou.NetworkEditor panetab7>, eventtype='keyhit', located=N
etworkComponent(item=None, name='invalid', index=0), mousepos=<hou.Vector2 [256,
 623]>, mousestate=MouseState(lmb=0, mmb=0, rmb=0), key='C', modifierstate=Modif
ierState(alt=0, ctrl=0, shift=0), time=183986.282)
MouseEvent(editor=<hou.NetworkEditor panetab7>, eventtype='mouseexit', selected=
NetworkComponent(item=None, name='invalid', index=0), located=NetworkComponent(i
tem=None, name='invalid', index=0), mousepos=<hou.Vector2 [-1, 664]>, mousestart
pos=<hou.Vector2 [-1, 664]>, mousestate=MouseState(lmb=0, mmb=0, rmb=0), draggin
g=0, wheelvalue=0, modifierstate=ModifierState(alt=0, ctrl=0, shift=0), time=183
987.416)
```

Lots of info to digest in those, and in [Intercepting Mouse Events](#intercepting-mouse-events) we will look further at these.