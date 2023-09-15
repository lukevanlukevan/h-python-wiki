---
title: Wedge from Parameter
author: [{ name: "Luke Van", link: "https://github.com/lukevanlukevan" }]
---

# Wedge from parameter

Someone asked about a way to copy a parameter, and have a hotkey to create a wedge node for that parm. So here is that.

We start off with the usual jump through hoops to get the pane under cursor, as we want to be able to press the hotkey and place a node.

```python
d = hou.ui.curDesktop() // our current desktop
ctx = d.paneTabUnderCursor() // pane under cursor

con = ctx.pwd()
t = con.type().name() // get the name of the type of our current pane tab. ie 'network editor'
```

```python
d = hou.ui.curDesktop()
ctx = d.paneTabUnderCursor()

p = hou.parmClipboardContents()[0]['path']

con = ctx.pwd()
t = con.type().name()

if t == 'topnet' or t == 'topnetmgr':

    try:
        sel = hou.selectedNodes()[-1]
        if sel.type().name() == 'wedge':
            w = sel
            count = w.parm('wedgeattributes').eval()
            w.parm('wedgeattributes').set(count+1)
            w.parm(f'exportchannel{count+1}').set(1)
            w.parm(f'channel{count+1}').set(p)
            w.parm(f'name{count+1}').set(p.split("/")[-1])
            w.parm('previewselection').set(1)

    except:
        w = con.createNode('wedge')
        w.setPosition(ctx.cursorPosition())
        w.parm('wedgeattributes').set(1)
        w.parm('exportchannel1').set(1)
        w.parm('channel1').set(p)
        w.parm('name1').set(p.split("/")[-1])
        w.parm('previewselection').set(1)
```

