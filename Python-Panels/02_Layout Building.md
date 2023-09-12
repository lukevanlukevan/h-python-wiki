---
title: "Layout Building"
author: [{ name: "Luke Van", link: "https://github.com/lukevanlukevan" }]
---

# Layout Building

Remember that myseterious `mypanel.ui` file I spoke about above? Let's demystify that.

The first thing you want to do is [download Qt Designer](https://build-system.fman.io/qt-designer-download)

Once you open it up, the "New Form" window should pop up, and I always just click the "Widget" one. Save this file to your `$PKG/scripts/python/PANEL_NAME` folder.

You should be staring at a blank widget now. The next step is to set the layout of the root widget. You would think you can drag a layout from the left side, but this software is so strange, that you do it a bit backwards. Drag a button from the left and then right click in the background of the widget and Set Layout to "Lay Out Vertically"

![](/img/LayoutBuilding/2.mp4)

Save again and swap back to your code editor. Let's dive back into the example above, and make some changes at the start.

```python
import hou
from PySide2 import QtWidgets, QtUiTools

class LVButton(QtWidgets.QtWidget):
	def __init__(self):
		super().__init__()
		self.folderpath = hou.getenv("PKG") + "/scripts/python/PANEL_NAME"
		ui_file_path = self.folderpath + "/LVButton.ui"

		loader = QtUiTools.QUiLoad()
		self.ui = loader.load(ui_file_path)

		widget = QtWidgets.QButton("Hello World!")
		widget.clicked.connect(self.message_log)
		root_layout = QtWidgets.QVBoxLayout()
		root_layout.addWidget(widget)
		self.setLayout(root_layout)

	def message_log(self):
		print("You clicked the button")
```

> Note: PKG is swapped out to whatever your package name is, specifically without the $ in this case. PANEL_NAME is also swapped out to whatever your panel name is.

We create a variable that stores the where to find the .ui file and we use the QtUiTools.QUiLoader to load the ui file.

Now that we have this ui as a variable, it isn't actually in our panel yet. We need to find an actual widget in the ui, which is our root widget. I like to go back to Qt Designer and rename the top level element to "root".

Then in our code, we can add the imported UI to the main layout we already created.

```python
import hou
from PySide2 import QtWidgets, QtUiTools

class LVButton(QtWidgets.QtWidget):
	def __init__(self):
		super().__init__()
		self.folderpath = hou.getenv("PKG") + "/scripts/python/PANEL_NAME"
		ui_file_path = self.folderpath + "/LVButton.ui"

		loader = QtUiTools.QUiLoad()
		self.ui = loader.load(ui_file_path)

		root_layout = QtWidgets.QVBoxLayout()
		root_layout.addWidget(self.ui)
		# widget.clicked.connect(self.message_log)

		self.setLayout(root_layout)

	def message_log(self):
		print("You clicked the button")
```

Now that we have our imported layout working, it becomes a lot easier to build out a good layout in Qt Designer and go back and forth until it suits your needs.

Above, the `widget.clicked` line was commented out as we ditched that widget. We can connect our new button to this again, but we need to get a reference to the button. For that, we can use the `findChild()` function, that takes 2 arguments, the type of widget and the name. This is why its crucial to name all widgets in Qt Designer (or at least the important ones). Go back and rename the QPushButton to "btn".

```python
import hou
from PySide2 import QtWidgets, QtUiTools

class LVButton(QtWidgets.QtWidget):
	def __init__(self):
		super().__init__()
		self.folderpath = hou.getenv("PKG") + "/scripts/python/PANEL_NAME"
		ui_file_path = self.folderpath + "/LVButton.ui"

		loader = QtUiTools.QUiLoad()
		self.ui = loader.load(ui_file_path)

		root_layout = QtWidgets.QVBoxLayout()
		root_layout.addWidget(self.ui)

		self.btn = self.ui.findChild(QPushButton, "btn")
		self.btn.clicked.connect(self.message_log)

		self.setLayout(root_layout)

	def message_log(self):
		print("You clicked the button")
```

Your code editor may have picked up a squiggly line under QPushButton. This is because we called it directly rather than using `QtWidgets.QPushButton`. This is fixable by adding QPushButton to our import in the top.

```python
import hou
from PySide2 import QtWidgets, QtUiTools
from PySide2.QtWidgets import QPushButton

...
```

So now we can connect the whole thing together, and we have restored our original functionality with a far easier layout building method.

```python
import hou
from PySide2 import QtWidgets, QtUiTools

class LVButton(QtWidgets.QtWidget):
	def __init__(self):
		super().__init__()
		self.folderpath = hou.getenv("PKG") + "/scripts/python/PANEL_NAME"
		ui_file_path = self.folderpath + "/LVButton.ui"

		loader = QtUiTools.QUiLoad()
		self.ui = loader.load(ui_file_path)

		root_layout = QtWidgets.QVBoxLayout()
		root_layout.addWidget(self.ui)

		self.btn = self.ui.findChild(QPushButton, "btn")
		self.btn.clicked.connect(self.message_log)

		self.setLayout(root_layout)

	def message_log(self):
		print("You clicked the button")
```

While it's still a bit of a mystery to me where `self` is needed, I normally opt for creating variables for anything that is an interface item with self.

## Happy Building! 🔨

