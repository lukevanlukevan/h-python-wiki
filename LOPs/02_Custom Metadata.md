---
title: Custom Metadata
author: [{ name: "WaffleBoyTom", link: "https://www.github.com/WaffleBoyTom" }]
---

# Custom Metadata

Here's how you can write custom data to USD prims and fetch them inside parameters.

In a python lop we can do :

```python
node = hou.pwd()
stage = node.editableStage()
prim = stage.GetPrimAtPath('/geo/myCube')
prim.SetCustomDataByKey("myCustomData","/geo/sphere")
```

You know have custom data in your prim's metadata.

![](/img/SavingThings/1.png)

You can now use a pretty long python expression to get that value inside a parameter (don't forget to tell Houdini it's a python expression and not a string value or a hscript expression)

```python
hou.node('.').input(0).stage().GetPrimAtPath('/geo/myCube').GetCustomDataByKey('myCustomData')
```

You could always create a primvar instead.

```python
from pxr import Sdf
node = hou.pwd()
stage = node.editableStage()
prim = stage.GetPrimAtPath('/geo/myCube')
customattrib = prim.CreateAttribute("myCustomAttrib",Sdf.ValueTypeNames.String)
customattrib.Set("/geo/sphere")
```

Then to get it, use

```python
hou.node('.').input(0).stage().GetPrimAtPath('/geo/myCube').GetAttribute('myCustomAttrib').Get()
```

![](/img/SavingThings/2.png)

