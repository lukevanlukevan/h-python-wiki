---
title: "Data Encapsulation"
author: [{ name: "Milan Lampert", link: "https://github.com/lampmilan" }]
---

## Encapsulate data in a HDA

Sometimes, we need to create data that is unique to a specific node and cannot be modified by other nodes of the same type.
In the Python module, most data and variables are shared among every node of the same type.

```python
import hou

# GLOBAL VAR EXAMPLE
previous_value = 0

def get_difference_from_global(current_value):
    global previous_value

    difference = current_value - previous_value
    print("DIFFERENCE: {}".format(difference))

    previous_value = current_value
```

In this example, we manipulate the `previous_value` variable using the `global` keyword. You might expect the global keyword affect only that node, but in reality, that information is the same for every instance of the node.
![global var example](img/DataEncapsulation/1.gif)

To avoid conflicts between node information, we need to encapsulate those pieces of information. For this, I'll demonstrate four different approaches to achieve this. All of them have some drawbacks, so the best choice often depends on the situation.
For demonstration, I will use the same problem for all four approaches: calculating the difference between the previous and current value when a parameter is changed.

## Hidden parameter

The most obvious solution is to use a hidden parameter and utilize it to store the value. There are many advantages to this approach: it's simple and fast to implement, it saves data between sessions, and this is the most "Houdini-like" approche. However, some data types don't have parameter types (such as lists, sets, and tuples). In such cases, you need to rely on type conversion. Additionally, over time as our HDA grows and we add more invisible parameters, it might become challenging to keep track of which parameters connect where and which ones get updated.

I've created two parameters: one that the user can interact with and another one that will serve as our hidden parameter.
![hidden parmameters](img/DataEncapsulation/2.png)

```python
# HIDDEN PARM EXAMPLE
def get_difference_from_invisible_parm(current_value):
    previous_value = hou.pwd().parm('prev_value').eval()

    difference = current_value - previous_value
    print("DIFFERENCE: {}".format(difference))

    hou.pwd().parm('prev_value').set(current_value)
```

## UserData

By default, every node comes with two dictionaries: "userData" and "cachedUserData". If we take a closer look at the Houdini documentation, they even refer to these two dictionaries as the "Per-node user-defined data", making them the closest thing to the de facto encapsulation method. First, let's examine userData. It's a handy solution for storing string variables between sessions. Compared to parameters, userData can be created and accessed where we actually need it, reducing clutter in our parameter list. However, the problem is that it can only store strings, so we either heavily rely on type conversion or implement workarounds. In this case, I go with the last one and simply convert the UserData entry to an integer, which is then converted back to a string after we do the calculation.

I define an entry in the `OnCreate` module, so there's no chance it's not available when I call it:

```python
node = kwargs['node']
node.setUserData('previous_value', '0')
```

And this is the `PythonModule` section:

```python
# USER DATA EXAMPLE
def get_difference_from_user_data(current_value):
    previous_value_str = hou.pwd().userData('previous_value')
    previous_value = int(previous_value_str)

    difference = current_value - previous_value
    print("DIFFERENCE: {}".format(difference))

    hou.pwd().setUserData('previous_value', str(current_value))
```

## CachedUserData

Compared to userData, cachedUserData can store any type of data, such as integers, lists, dictionaries, and even objects. However, the main disadvantage is that it only stores data for the current session.

Like previously I create the entry in the `OnCreate` module:

```python
node = kwargs['node']
node.setCachedUserData('previous_value_cache', 0)
```

Python module:

```python
# USER CACHE EXAMPLE
def get_difference_from_user_cache(current_value):
    previous_value = hou.pwd().cachedUserData('previous_value_cache')

    difference = current_value - previous_value
    print("DIFFERENCE: {}".format(difference))

    hou.pwd().setCachedUserData('previous_value_cache', current_value)
```

## Singleton pattern

Basically, it's like `cachedUserData`, but it's overcomplicated. It can store any data type for the current session. One minor advantage is that it gives you full access to every value while still encapsulating them. Additionally, it's open to extension, unlike the UserData classes (so we can add new methods if needed, such as `destroy_all` or `return_every_instance`).

There are two main issues:
-Singleton is a controversial topic among Python developers because Python modules work exactly like a singleton. Here we use singleton because it's a more stable solution compared to HDA modules. (One issue I found out recently is that sometimes returning an object from another module actually gets converted to a string).
-The implementation is rather robust, and we need to handle instance removal as well, otherwise, we'll face with some nice "hou.ObjectWasDeleted" errors.

```python
# SINGLETON
class PreviousValues:
    _instances = {}

    @classmethod
    def get_instance(cls, node_instance):
        if node_instance not in cls._instances:
            cls._instances[node_instance] = 0
        return cls._instances[node_instance]

    @classmethod
    def set_value(cls, node_instance, value):
        cls._instances[node_instance] = value

    @classmethod
    def delete_instance(cls, node_instance):
        cls._instances.pop(node_instance)



def get_difference_from_singleton(current_value):
    previous_value = PreviousValues.get_instance(hou.pwd())

    difference = current_value - previous_value
    print("DIFFERENCE: {}".format(difference))

    PreviousValues.set_value(hou.pwd(), current_value)
```

The `OnDelete` module handle the remove of the instance:

```python
type = kwargs['type']
node = kwargs['node']

type.hdaModule().PreviousValues.delete_instance(node)
```
