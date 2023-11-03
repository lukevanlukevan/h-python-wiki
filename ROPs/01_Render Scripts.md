---
title: Render Scripts
author: [{ name: "Luke Van", link: "https://github.com/lukevanlukevan" }]
---

# Render Scripts

Almost all ROPs will have a section for scripts:

-   Pre-Render: Before the whole sequence.
-   Pre-Frame: Before each frame.
-   Post-Frame: After each frame.
-   Post-Render: After the whole sequence.

These are pretty self explanatory. In the box you can type some Hscript (visit www.houhscriptwiki.com... jk) or some Python 😎. Make sure to change the right hand dropdown to Python for the fun stuff.

These boxes can run their own scripts written straight into them, or you can alt+E and get the big editor box and write some multiline stuff.

In the post render box, add:

```python
import LVRender
from imp import reload
reload(LVRender)

LVRender.post_render()
```

Standard reloading stuff here, then we call a function in the file we loaded.

Now that we have conquered that, we we can get into the next bit below.
