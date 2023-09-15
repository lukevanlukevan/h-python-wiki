---
title: Remove clip from agents
author: [{ name: "WaffleBoyTom", link: "https://www.github.com/WaffleBoyTom" }]
---

# Remove clip from agents

So the other day, Antini asked on Discord if there was a way to delete a clip from an agent's definition. I've been getting into crowds lately so I thought I'd take a look. It seems there's no sop that does that and while vex has utility functions for crowds, there's no function to delete clips, so python it is.

Here's some docs to get you going:

[What is an Agent Primitive](https://www.sidefx.com/docs/houdini/crowds/agents.html#agentdefinition)

[hou.AgentDefinition() class](https://www.sidefx.com/docs/houdini/hom/hou/AgentDefinition.html)

[hou.Agent() class](https://www.sidefx.com/docs/houdini/hom/hou/Agent.html)

This is my setup:

![](/img/RemoveClips/1.png)

A basic agent (mocapbiped3) with 3 different clips

The code is pretty short so here's the whole thing.

```python
node = hou.pwd()
geo = node.geometry()

for prim in geo.prims():

    adef = prim.definition()
    fadef = adef.freeze()
    fadef.removeClip("walk_turn")
    prim.setDefinition(fadef)
```

An agent is a packed primitive so it's just a _hou.Prim()_, the loop iterate through every agent (in this case we could do `prim = geo.prim(0)` to get the agent as there's only one prim).

By default, an agent's definition is read only so we need to create a read-write version with `hou.AgentDefinition().freeze()`.

We can then remove a clip from that definition, in this case 'walk_turn'.

Then we replace our agent definition with the new one.

