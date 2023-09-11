---
title: "Getting Started"
author: [{ name: "Luke Van", link: "https://github.com/lukevanlukevan" }]
---

# Getting Started

This is going to be a biggie, so strap in.

## Quick note:

There are 2 ways to do anything that uses python scripts/files. The first is to script in the file, like with shelf tools, but I prefer the next method, which is just having all the external files, and importing them and calling them from Houdini.

Quick rundown of the 3 key bits:

yourpanel.pypanel (in your $PKG/python_panels)

yourpanel.py (in your $PKG/scripts/python/PANEL_NAME)

yourpanel.ui (in your $PKG/scripts/python/PANEL_NAME)

## My First PyPanel

Go to Window -> Python Panel Editor. Open this and create a "new interface" in the top right. The first thing you want to do is change the path your package folder. Drop it in `$PKG/python_panels` and give it an appropriate name. In my case, this was `LVButton.pypanel`. Now you can change the name and label as normal.

You will see the default code with a big comment block and this at the bottom:

```python
from hutil.qt import QtWidgets

def onCreateInterface():
	widget = QtWidgets.QLabel('Hello World!')
	return widget
```

Before you even think about what's goin on, lets create the files I mentioned above in our package/python folder.

I will make a folder called LVButton and inside it, create LVButton.py for now, we will get to LVButton.ui later. Copy and paste the code from the Python Panel Editor into your new file.

Back in Houdini, go to Windows -> New Floating Window and then right click the panel name, and go to Misc -> Python Panel. Later on we can add this panel as a normal pane, this just allows us to hard refresh the panel which is useful in dev stage.

There is a dropdown in the top left with some of the other python panels, and yours most likely won't be there. Go to your Python Panel Editor window and click the tab in the top left called "Toolbar Menu". Find your newly created Panel and move it over the right side and click Apply/Accept. Now you can see it in the dropdown.

At this point, you should have something like this:

![](/img/PyPanel/01.png)

> Not very interesting, but it's something.

What we want to do now is start using the external file we created (LVButton.py) and use that rather than the code in the Houdini Editor.

For that, in the Python Panel Editor, we are going to change the code to this:

```python
from imp import reload
from LVButton import LVButton
reload(LVButton)

def OnCreateInterface():
	return LVButton.LVButton()
```

> Quick note on using reload from imp, this is just a habit to make sure that your external file gets reloaded while you edit it. Once you're done, its safe to remove the reload.

Now we can close the Python Panel Editor window. That's it for now, now we can just use our IDE and refresh the script in Houdini.

Let's get to breaking down the code now. The very first line is `from hutil.Qt import QtWidgets`. QtWidgets is the UI library that is used for python panels. You can also import them from PySide2. That's what I use, as they are cross compatible. There are some fancy Houdini only ones that are in the hutil one, so use your discretion.

Now, because we importing the code, we need to adjust the way it gets built. The most important thing is to create our UI as a class.

```python
from PySide2 import QtWidgets

class LVButton(QtWidgets.QtWidget):
	def __init__(self):
		super().__init__()
		widget = QtWidgets.QLabel("Hello World!")
```

Cool, let's save this and refresh the widget
Oh, a bunch of nothing. Why is that? Well, you can see we created the class and in brackets we set it to `QtWidgets.QWidget`. So what we have is essentially a QWidget that we are working with. We need to do this as the panel needs to have a single widget as the root of it.

Another quick side note. If you have ever worked with HTML, you may know how the nesting of elements works. This is no different, with the annoying exception that the flow in most cases is a widget, that has a child Layout, that can have one or more child widgets that can have a child layout, etc.

Right now, the reason we dont have anything showing is that we are just showing the native QWidget, and haven't added the QLabel we created yet.

We can now create a layout, and add the widget to that layout, and then set the layout out of hero widget to that layout. The order is important, just like nesting dolls. You should add the lowest level to the parent and then go up a level.

```python
from PySide2 import QtWidgets

class LVButton(QtWidgets.QtWidget):
	def __init__(self):
		super().__init__()
		widget = QtWidgets.QLabel("Hello World!")
		root_layout = QtWidgets.QVBoxLayout()
		root_layout.addWidget(widget)
		self.setLayout(root_layout)
```

Now we can refresh, and we can see our label!

This is called LVButton, so we have to add a button somewhere here. With our current layout, we have a simple vertical layout, called `QVBoxLayout`, but it is good to refer to [the docs](https://doc.qt.io/qtforpython-5/modules.html) to see which layout may be best for your case.

Back to our button, let's change the QLabel to a QPushButton

```python
from PySide2 import QtWidgets

class LVButton(QtWidgets.QtWidget):
	def __init__(self):
		super().__init__()
		widget = QtWidgets.QButton("Hello World!")
		root_layout = QtWidgets.QVBoxLayout()
		root_layout.addWidget(widget)
		self.setLayout(root_layout)
```

Button in, let's work on the clicking. QPushButton inherits from QAbstractButton, and if we check the docs for that, we can see it emits a signal when clicked. With QtWidgets, we have to connect that signal to a function.

```python
from PySide2 import QtWidgets

class LVButton(QtWidgets.QtWidget):
	def __init__(self):
		super().__init__()
		widget = QtWidgets.QButton("Hello World!")
		widget.clicked.connect(self.message_log)
		root_layout = QtWidgets.QVBoxLayout()
		root_layout.addWidget(widget)
		self.setLayout(root_layout)

	def message_log(self):
		print("You clicked the button")
```

You may have noticed we needed to call our function with `self.message_log` rather than just `message_log`. This is because we are inside the class still, and therefore we need to specificy that we are calling it's own function rather that a random function that may be floating around in our code.

Inside the `message_log` function, we can basically do anything we want there now, standard Houdini stuff. You have essentially created a button that you can dock and run anything, create nodes, or drive any other tools you have created.

During this, you may have noticed that the addWidget, setLayout dance is really annoying, so hop over to [Layout Building](#layout-building) to see how it can get easier!
