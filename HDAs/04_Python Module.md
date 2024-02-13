---
title: Python Module
author: [{ name: "Luke Van", link: "https://github.com/lukevanlukevan" }]
---

# Python Module

You can do a lot of scripting right in an HDA. If you create a new HDA, in the type properties you can can click to tab for "Scripts". On the bottom left, there is an "Event Handler" dropdown. You can select any of these and bind some python to those events. The most basic on is the "Python Module" one, that is basically just a little environment for functions and logic that you can call from other places in the script.

In the Python Module script, create a function like so:

```python
def hello_world(**kwargs):
	print("Hello World")
```

Now you can use the script in the callback script of any parm. For example, make a button parm and on the callback script, set the language to Python and in the box, type `hou.phm().hello_world()`

Now when you click the button, you run the function.
