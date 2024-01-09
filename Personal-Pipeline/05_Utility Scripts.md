---
title: Utility Scripts
author: [{ name: "Luke Van", link: "https://github.com/lukevanlukevan" }]
---

# Utility Scripts

Often you ay find youself building little shelf utility scripts. I have a messy shelf with all the ideas I have, and once they become useful enough, I port them over to a main utility file, in my case, `scripts/python/LVUtils.py`.

With this file, there is the benefit of being able to create a bunch of helper functions I may use in multiple places and keep everything nice and neat. For example, if you want to always have a clean console when things go wrong, you could create a function like:

```python
def lv_error(f, message):

	print("------------------------")
	print(f"Error running: {f}")
	print("Logging error")
	print(message)
	print("------------------------")
```

Here the function takes two arguments, the function name and the message. In each tool you could have a simple try... except statement and get a clean log for each of them.

```python
def lv_error(f, message):

	print("------------------------")
	print(f"Error running: {f}")
	print(message)
	print("------------------------")


def print_selected_nodes():
	try:
		nodes = hou.selectedNodes()
		print(nodes)
	except Error as e:
		lv_error("Print Selected Nodes", e)
```

Now if the function ever goes wrong, you get a clean log for the error.
