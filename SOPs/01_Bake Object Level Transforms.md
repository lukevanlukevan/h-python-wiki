---
title: "Bake Object Level Transforms"
author: [{ name: "WaffleBoyTom", link: "https://github.com/WaffleBoyTom" }]
---

# Bake Object Level Transforms

This might seem a bit niche but I discovered this while helping someone with their HDA and thought it might be interesting to look at.

Say you have this ![sm](/img/BakeObjLevelTransforms/1.png)
How would you bake that transform so you just have a camera with a bunch of keyframes instead ? ![](/img/BakeObjLevelTransforms/2.png)

My scene, as shown earlier, is simple : a camera ('/obj/cam1') and a null ('/obj/null1').

The rotation along the y axis of the null is animated with an expression so the camera orbits around the null, very basic stuff.

The final code is fairly awkward to try and run in a shell, so I prototyped in the source editor instead. ![](/img/BakeObjLevelTransforms/3.png)

First step, grab the original camera, copy it, then offset it along the x axis in the network so it isn't sitting right on top of our original camera, then disconnect it from the null.
I've hardcoded the original camera here but depending on what you want to do you might want to use hou.selectedNodes() or something else.

```python
import hou

origcam = hou.node('/obj/cam1')

cam2 = hou.copyNodesTo((origcam,),origcam.parent())[0]
cam2.setPosition(cam2.position() + hou.Vector2((2.0,0.0)))
cam2.setInput(0,None)
```

In Houdini, object nodes have local and world transforms that you can access with Python, [refer to this docs page for more information.](https://www.sidefx.com/docs/houdini/hom/hou/ObjNode.html)

If I get my original camera's world transform and set that to my new camera , they will be at the same position, using objNode.worldTransform() and objNode.setWorldTransform().
The limitation here is that you can access a worldTransform matrix at a certain point in time(objNode.worldTransformAtTime()) but can't set it at a certain point in time (~~objNode.setWorldTransformAtTime()~~). Bummer, right ? It does make sense though, because otherwise you would have something animated but no indication that it actually is.

The obvious workaround is to set keyframes.
Luckily, objNodes come with a setParmTransform() method that sets that world transform using the parameters, which we can set keyframes on.

Even more luckily, SideFx shows us how the method is implemented, so we can tweak it to fit our needs.

This is SideFx's code, which you can find at the linked posted above.

```python
def setParmTransform(self, matrix):
parm_values = matrix.explode(
    transform_order=self.parm('xOrd').evalAsString(),    rotate_order=self.parm('rOrd').evalAsString(),    pivot=hou.Vector3(self.evalParmTuple('p')))
for parm_name, key in ('t', 'translate'), ('r', 'rotate'), ('s', 'scale'):
    self.parmTuple(parm_name).set(parm_values[key])
```

The main issue is that we can't call parmTuple.setKeyframe(). We have to call parm.setKeyframe(). We'll be adding a nested for loop to iterate through the tuple.

```python
for index,parm in enumerate(node.parmTuple(parm_name)):
	val = parm_values[key]
	val = val[index]
	parm.setKeyframe(hou.Keyframe(val,time))
```

This way we're iterating through every parameter of the tuple and setting a keyframe at the given time.

One thing to know is that methods that ask for a _time_ argument require a _time_ argument, which means it can't be frames. SideFx provides a hou.frameToTime() function and again the way it's implemented [which is gonna be useful for us.](https://www.sidefx.com/docs/houdini/hom/hou/frameToTime.html)

What we'll do is iterate through the timeline by getting the start and end frame, convert that to a _time_ with the formula mentioned in the docs and then we can run our function.

Let's convert setParmTransform() to be a function instead of a method, integrate our nested loop, then run the function in a loop that runs for every frame of the timeline.

Here's our final snippet :

```python
import hou

origcam = hou.node('/obj/cam1')

def customSetParmTransform(node, matrix,time=0):

    parm_values = matrix.explode(
        transform_order=node.parm('xOrd').evalAsString(),
        rotate_order=node.parm('rOrd').evalAsString(),
        pivot=hou.Vector3(node.evalParmTuple('p')))

    for parm_name, key in ('t', 'translate'), ('r', 'rotate'), ('s', 'scale'):

        for index,parm in enumerate(node.parmTuple(parm_name)):

            val = parm_values[key]
            val = val[index]
            parm.setKeyframe(hou.Keyframe(val,time))


cam2 = hou.copyNodesTo((origcam,),origcam.parent())[0]
cam2.setPosition(cam2.position() + hou.Vector2((2.0,0.0)))
cam2.setInput(0,None)

frange = hou.playbar.frameRange()
start = int(frange[0])
end = int(frange[1])


for i in range(start,end+1):

    ftt = (float(i)-1.0) / hou.fps()

    customSetParmTransform(cam2,origcam.worldTransformAtTime(ftt),ftt)
```

This runs a bit slowly admittedly. SideFx recommends using parm.setKeyframes((keyframeTuple)) for faster performance but I couldn't think of a clever way to integrate this, so it's brute force for now. Let us know if you find a better way.

Right, so I got frustrated and decided to rewrite it using parm.setKeyframes().
After some wrangling, this is what I ended up with

```python
import hou

origcam = hou.node('/obj/cam1')

def customSetParmTransform(node, matrix,time,it,kflist):

    parm_values = matrix.explode(
        transform_order=node.parm('xOrd').evalAsString(),
        rotate_order=node.parm('rOrd').evalAsString(),
        pivot=hou.Vector3(node.evalParmTuple('p')))


    for parm_name, key in ('t', 'translate'), ('r', 'rotate'), ('s', 'scale'):

        letters = ('x','y','z')

        for index,parm in enumerate(node.parmTuple(parm_name)):

            val = parm_values[key]
            val = val[index]
            keyf = hou.Keyframe(val,time)

            klist = keydict[parm_name+letters[index]]
            klist[it] = keyf

cam2 = hou.copyNodesTo((origcam,),origcam.parent())[0]
cam2.setPosition(cam2.position() + hou.Vector2((2.0,0.0)))
cam2.setInput(0,None)

parms = ('tx','ty','tz','rx','ry','rz','sx','sy','sz')

frange = hou.playbar.frameRange()
start = int(frange[0])
end = int(frange[1])

total = end-(start-1)

keydict = dict()

for parm in parms:

    keydict[parm] = [0]*total


nindex = 0

for i in range(start,end+1):

    ftt = (float(i)-1.0) / hou.fps()

    customSetParmTransform(cam2,origcam.worldTransformAtTime(ftt),ftt,nindex,keydict)

    nindex+=1



for index, parm in enumerate(parms):

    kftuple = tuple(keydict[parm])

    cam2.parm(parm).setKeyframes(kftuple)
```

This runs so much quicker.

