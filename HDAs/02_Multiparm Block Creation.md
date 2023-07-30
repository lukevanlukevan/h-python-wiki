---
title: "Multiparm Block Creation"
author:
    [
        { name: "WaffleBoyTom", link: "https://github.com/WaffleBoyTom" },
        { name: "Luke Van", link: "https://github.com/lukevanlukevan" },
    ]
---

# Multiparm Block Creation

How to create multiparm blocks, set and access them through the Python API / HOM.

To create a multiparm block, you actually need to grab the "Folder" parameter. Then, set the "Folder Type" to one of the 'Multiparm Block' types.

![1](/img/MultiparmBlockCreation/1.png)

All 3 types are essentially the same, only the UI differs. I tend to use the 'List' type most often but that's personal preference.

You can now create a parameter of your choosing inside the block. In this example, I created a string parameter. Each time you press the '+' button on the block, it will create an instance of your parameter(s). In my case, it creates a string parameter. Note the '#' character in my parm name `parm_#`. This refers to the instance of the parameter. If you look at at the image below, you can see my parms are labelled: Parm 1, Parm 2, Parm 3.

![1](/img/MultiparmBlockCreation/2.png)

## Creating a Multiparm block and setting its instances with Python

To create a parameter on a node, you need `node.addSpareParmTuple()` which requires a `hou.ParmTemplate`. Now that you know that a multiparm block is a folder, we can use the `hou.FolderParmTemplate` class to create our multiparm block.

We need to initialize our FolderParmTemplate to have the `folder_type` argument as one of the MultiparmBlock types. In this case we will use the hou.folderType.MultiparmBlock.

We will now create a parameter inside the multiparm.

First we create the parm template, choosing for this example the `StringParmTemplate` class.

We can now call `addParmTemplate()` on the multiparm template to add the `StringParmTemplate` to it.

Then we use `addSpareParmTuple(template)` on the target node to create the multiparm block.

![1](/img/MultiparmBlockCreation/7.png)

Say you want to set a float parameter to 5.25; you would use the `set()` method on your parameter object, like so: `node.parm('my_float_parm').set(5.25)`

Multiparms work in a similar fashion. The `set()` method will accept an integer as an argument. For example:
![1](/img/MultiparmBlockCreation/3.png)

So, let's now call `parm.set(15)`. This will create 15 instances of the string parameter we added to the block.

Great! You've set your multiparm to the desired number of instances. If you want to clear it, simply set it to 0: `parm.set(0)`

We've now created a multiparm block that holds a string parameter, and created 15 instances of said string parameter. Let's see what that snippet looks like:

```python
node = hou.node('/obj/geo1/img/ME') #this is just a null inside of sops
template = hou.FolderParmTemplate("my*block","My Block",folder_type = hou.folderType.MultiparmBlock)
stemplate = hou.StringParmTemplate("my_instance*#","My Instance #",1)
template.addParmTemplate(stemplate)
parmtuple = node.addSpareParmTuple(template)
node.parm('my_block').set(15)
```

## Accessing the multiparm instances

You can use a for loop `range(multiparm.evalAsInt()` to iterate through all items.

Here's the catch: when creating a multiparm, there's an option to decide how the instances are numbered, the "First Instance" option.

![1](/img/MultiparmBlockCreation/4.png)

As you can see, our parms start at 1; that's because we have 'First Instance' set to 1. This means that when looping through the parms, we'll have to add 1 to the iterator.

![1](/img/MultiparmBlockCreation/5.png)

To get the Parm object, we're using `node.parm('parm_'+str(i+1))`, that's because 'First Instance' is set to 1 but iterators in loops start at 0 (you probably already know this).

This is fine but it's something you have to keep in mind.

Don't want to have to deal with that? Set 'First Instance' to 0, then you won't have to add 1 to your iterator. However that will also influence the look of your UI : the first parm is now labelled 'Parm 0'.

![1](/img/MultiparmBlockCreation/6.png)

Earlier we created a multiparm block with Python. What if you want to set 'First Instance' using Python too ?

When creating the parm template, you can specify a 'tags' argument that will let you set that 'First Instance' to whatever your heart desires ( as long as it is an unsigned int, aka x >=0.). Well I say uint but you then have to convert it to a string. You'll most likely only ever set it to '0' or '1'.

Let's take our previous snippet and specify the 'multistartoffset' tag to set the 'First Instance' option through Python.

```python
node = hou.node('/obj/geo1/img/ME') #this is just a null inside of sops
tags = dict()
tags['multistartoffset'] = '0' #note that 0 is a string
template = hou.FolderParmTemplate("my*block","My Block",folder_type = hou.folderType.MultiparmBlock,tags=tags)
stemplate = hou.StringParmTemplate("my_instance*#","My Instance #",1)
template.addParmTemplate(stemplate)
parmtuple = node.addSpareParmTuple(template)
node.parm('my_block').set(15)
```

Say you've rebuilt this setup, let's provide you with an example snippet that sets your multiparm block to 15 instances and sets each of them to some random gibberish.

In the snippet below, `index == str(i)`. This works because we had set 'First Instance' to 0 ! Don't forget that by default 'First Instance' is equal to 1 which means you would have to set index to be `str(i+1)`.

Here's what the code looks like:

```python
import random
import string
import hou #depending on where you write this you might not need this import

def create_rand_string(length):
characters = string.ascii_letters + string.digits + string.punctuation
rstring = ''.join(random.choice(characters) for i in range(length))
return rstring

node = hou.node('/obj/geo1/img/ME')
parm = node.parm('my_block')

instances = 15

parm.set(15)

for i in range(instances):

    index = str(i)

    parm = node.parm('my_instance_'+index)

    randint = random.randint(i+1,20)

    parm.set(create_rand_string(randint))
```

That's it! Set all the instances within a multiparm block using python.

