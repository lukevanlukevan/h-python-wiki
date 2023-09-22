---
title: Verbs
author: [{ name: "WaffleBoyTom", link: "https://www.github.com/WaffleBoyTom" }]
---

# Verbs

If you're not familiar with verbs, make sure you watch this Jeff Lait Masterclass:
https://vimeo.com/222881605

You can skip to around 50:00 for the verb part, but I recommend you watch the whole thing, otherwise verbs won't really make sense. If you're already familiar with compiling/compiled blocks then feel free to skip to the verbs part.

Here's a couple example of using verbs:

```python
def runVerb(geo,name,input=[], parms=None):

    verb = hou.sopNodeTypeCategory().nodeVerb(name)
    if parms:
        verb.setParms(parms)

    verb.execute(geo,input)

node = hou.pwd()
geo = hou.Geometry()

runVerb(geo,"box")

runVerb(geo,"vdbfrompolygons",[geo])

runVerb(geo,"vdbsmoothsdf",[geo])

parms = {"conversion" : 2}

runVerb(geo,"convertvdb",[geo],parms)

node.geometry().clear()
node.geometry().merge(geo)
```

runVerb() is a wrapper to help execute a verb. When executing a verb you need what you would need if you were using a node instead:

-   a name : "box", "vdbfrompolygons",...
-   a geo to execute the verb on. If you're generating a box then you can start from an empty hou.Geometry() object but if you want to run a smooth verb on a box for example, you'll need that box's geometry object.
-   an input / multiple inputs : if you try to execute a ray verb or a boolean verb you'll need multiple inputs. The input argument is a list of hou.Geometry() objects.
-   parameters : Parms is a dictionnary parameter. If left blank, values will be set to default. Any parameter included in the dictionary will override the defaults.

Remember that SideFx provides a couple examples here ![](/img/Verbs/1.png)

Here's another snippet that generates boxes on boxes on boxes:

```python
def box(geo,height):

    verb = hou.sopNodeTypeCategory().nodeVerb('box')
    parms = dict()
    parms["t"] = (0.0,height,0.0)
    verb.setParms(parms)
    verb.execute(geo,[])


node = hou.pwd()

startheight = 0.5

geo = hou.Geometry()

numboxes = 15  # node.parm('numboxes').evalAsInt()

for i in range(numboxes):

    box(geo,startheight+i)
    node.geometry().merge(geo)
```

![](/img/Verbs/2.mp4)

