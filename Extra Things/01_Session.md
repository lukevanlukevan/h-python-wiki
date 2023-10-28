---
title: Session
author: [{ name: "Luke Van", link: "https://github.com/lukevanlukevan" }]
---

# Session

Quick one that can be useful. If you ever need to store some form of variable in a way that it persists over multiple scripts or executes of a script, you can use `hou.session`. Open the python shell and write `hou.session.foo = "Hello!"`.

Now you can call `hou.session.foo` anywhere and your variable will persist!

If you open the Python Source Editor, you can also add functions and variables there. Annoyingly, variables created outside of the Source Editor don't show up when editing it, but they do persist.
