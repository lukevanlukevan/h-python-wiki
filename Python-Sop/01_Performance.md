---
title: "Performance"
author: [{ name: "WaffleBoyTom", link: "https://github.com/WaffleBoyTom" }]
---

# Performance

So you've been dabbling with Python and now you're wondering if maybe you should use the Python SOP to do geometry operations. Well, wonder no longer, you shouldn't.

VEX is absolutely perfect for manipulating geometry; that's what it was built for! Python is great for a ton of things but this isn't one of them. So unless you're calling an API or doing something really specific, prefer VEX over Python for geometry manipulation (unless you hate being efficient).

Now that you've been warned, let's wrangle some points with Python.

If you try to modify SOP geometry from outside of a Python SOP, Houdini will raise a `hou.GeometryPermissionError()` so we'll use a Python SOP to read and write to our geometry object.

We'll recreate what I made in VEX here: pushing points along their normal, randomize the amplitude and animate them back and forth using `cos()`.

![](/img/Performance/1.png)

If you drop down a Python SOP and look at the code presets, you'll find something that does almost what we want. ![](/img/Performance/2.png)
We'll use that as a launching pad for our effect.

Let's first import the math module so we can use `math.cos()`. We're also going to import the random module because we want to randomize our amplitude.

Now we're going to create a few channels that we can access from within our code. Unfortunately, the python sop doesn't have a handy button like the wrangle so we're going to have to create them ourselves.

For the time being, I'm only going to create a frequency channel, we can add more as we progress.

We can easily read that channel using `node.parm('freq').eval()`.

Let's now push our points along their normal, then we'll look into randomizing and animating.

Inside the for-each loop, I can read the normal attribute using `point.attribValue('N')`. That returns a tuple. Our `point.position()` is a `hou.Vector3()` object so we'll have to convert our tuple to that class to add them: `pos += hou.Vector3(dir)`.

Let's look at our code so far:

```python
import math
import random

node = hou.pwd()
geo = node.geometry()

frequency = node.parm('freq').eval()

for point in geo.points():

    pos = point.position()

    dir = point.attribValue('N')

    pos += hou.Vector3(dir)

    point.setPosition(pos)

```

Let's now add some random amplitude to make it a bit more interesting.

To get a random seed per point we could use `point.number()` but we're just going to use the loop's iterator. To achieve that, let's rewrite our loop to use enumerate: `for index,point in enumerate(geo.points()):`.

Next step is to create a Random() object, then set its seed to our iterator -- to which we can add some random value that we could potentially read from a channel but I'm just going to hard-code one -- . Then we can use `random.uniform(start,end)` to generate a random number.

The snippet now looks something like this :

```python
import math
import random

node = hou.pwd()
geo = node.geometry()

frequency = node.parm('freq').eval()

for index,point in enumerate(geo.points()):

    rng = random.Random()

    rng.seed(index+689)

    randamp = rng.uniform(1,2)

    pos = point.position()

    dir = point.attribValue('N')

    pos += (hou.Vector3(dir) * randamp )

    point.setPosition(pos)
```

Our result now looks like this: ![](/img/Performance/3.png)

We want to animate our `cos()` so let's access the current frame with `hou.frame()`.

We'll use that, multiplied with frequency, as an argument for `math.cos()`.

We now want to offset each point, let's use the iterator and the Random() object again

Here's what the final snippet looks like:

```python
import math
import random

node = hou.pwd()
geo = node.geometry()

frequency = node.parm('freq').eval()

frame = hou.frame()

for index,point in enumerate(geo.points()):

    rng = random.Random()

    rng.seed(index+689)

    randamp = rng.uniform(1,2)

    rng.seed(index+235)

    randoffset = rng.uniform(1,5)

    offset = randoffset * 35

    cos = math.cos((frame+offset) * frequency) * 0.1

    pos = point.position()

    dir = point.attribValue('N')

    pos += (hou.Vector3(dir) * randamp * cos )

    point.setPosition(pos)
```

Feel free to replace all the literals with channels so you can play around with sliders.

Let's now do some performance monitoring!

With 50K primitives, 25 002 points , the wrangle runs at _>120 fps_. The Python sop? _1.5_...

Calling `point.number()` inside the loop instead of using `enumerate()` seems to slow it down even more but it's barely noticeable, it's excruciatingly slow regardless...

With 2K prims and 1002 points, Python reaches around _34 fps_.

Now you know this isn't something you should do with Python!

