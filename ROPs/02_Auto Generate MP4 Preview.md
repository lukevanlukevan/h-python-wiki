---
title: Auto Generate MP4 Preview
author: [{ name: "Luke Van", link: "https://github.com/lukevanlukevan" }]
---

# Auto Generate MP4 Preview

In almost every shot, you will be comping your work in some way, but sometimes it can save time and have a video that you can use for quick preview or dropping into Slack, etc.

Let's create a new script eg. `LVRender.py` and inside it, we can add a function.

```python
import hou

def pos_render(node=hou.pwd()):
	print(node)
```

We pass in an optional argument of `hou.pwd()`. This allows up to run the script elsewhere, not just from the post render. If we run it, we get our ROP the script was called from.

Now we can get a few values that we will need.

```python
import hou

def post_render(node=hou.pwd()):
    start = node.parm("f1").eval()  # type: ignore
    parm = node.parm("RS_outputFileNamePrefix")
    wide = parm.unexpandedString()  # type: ignore
    wide = wide.replace("$OS", node.name())

    if "$F4" in wide:
        wide = wide.replace("$F4", "%04d")
    elif "$F3" in wide:
        wide = wide.replace("$F3", "%03d")
    elif "$F2" in wide:
        wide = wide.replace("$F2", "%02d")

    full = hou.expandString(wide)
    print("full: ", full)

    video = "".join(full.split(".")[:-1]) + ".mp4"

    video = video.replace("%04d", "")
    video = video.replace("%03d", "")
    video = video.replace("%02d", "")
```

A bunch of string manipulations here to get our unexpanded output path, and replace the `$F4` with the `%04d` that ffmpeg uses to glob all our output images.

At the end we replace that with nothing to get a clean no digit version of the path for our video

Now we need to run ffmpeg and create our video, for this I chose `subprocess` as its pretty simple to construct a string and pass it in.

To build our string:

```python
    command = f'ffmpeg -framerate 25 -y -start_number {start} -i "{full}" -c:v libx264 -crf 23 -pix_fmt yuv420p "{video}"'

```

Then we can finally call the full command with `subprocess.Popen()`:

```python
def post_render(node=hou.pwd()):
    start = node.parm("f1").eval()  # type: ignore
    parm = node.parm("RS_outputFileNamePrefix")
    wide = parm.unexpandedString()  # type: ignore
    wide = wide.replace("$OS", node.name())

    if "$F4" in wide:
        wide = wide.replace("$F4", "%04d")
    elif "$F3" in wide:
        wide = wide.replace("$F3", "%03d")
    elif "$F2" in wide:
        wide = wide.replace("$F2", "%02d")

    full = hou.expandString(wide)
    print("full: ", full)

    video = "".join(full.split(".")[:-1]) + ".mp4"

    video = video.replace("%04d", "")
    video = video.replace("%03d", "")
    video = video.replace("%02d", "")

    command = f'ffmpeg -framerate 25 -y -start_number {start} -i "{full}" -c:v libx264 -crf 23 -pix_fmt yuv420p "{video}"'

    subprocess.Popen(command)

    hou.ui.showInFileBrowser(video)
```

Cherry on top is that it opens and explorer window with our file selected.

If you were gonna be really snazzy, you could break this into 2 functions, one that renders the video, and a preprocess that you can use all over Houdini to create videos.
