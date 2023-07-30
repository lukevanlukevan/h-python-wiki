---
title: "Using Kwargs"
author:  [
	{ name: "Luke Van",
	link: "https://github.com/lukevanlukevan"}
	]
---

# Using kwargs

In a shelf script, it is also possible to access some extra information related to the running of the command. The docs say:
"When Houdini calls your script, it adds a dictionary variable named kwargs to the script’s context." You can read more [here](https://www.sidefx.com/docs/houdini/hom/tool_script.html#:~:text=Arguments-,When,-Houdini%20calls%20your).

The link above has the listed variables you can access, but for this example, I'm going to be checking if the shift key is pressed. The key name is `shiftclick` and the type is `bool`.

So, new shelf script, and we type:

```python
if kwargs['shiftclick']:
	print('Shifted')
else:
	print('unshifted')
```

The name and type are fairly self explanatory, and you can do everything you could do with any other variable.
