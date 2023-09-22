---
title: Custom Network Titles
author: [{ name: "WaffleBoyTom", link: "https://www.github.com/WaffleBoyTom" }]
---

# Custom Network Titles

Shout out to Remi Pierre and Matt Estela for telling me about this.

(this quick tip assumes you're using a version of Houdini that uses py3.9)

You can customize the top left and top right titles of your network editor.

Here's an example:

![](/img/CustomNetworkTitles/1.png)

First off, copy this file `$HFS/houdini/python3.9libs/nodegraphtitle.py`.

To find out where $HFS is, you can either :

`~$ echo $HFS` with the Houdini env initialized or
`~$ hconfig` to print the values of common houdini variables
or inside Houdini : Help>About Houdini> Show Details and scroll down until you find _$HFS_.

Now go to $HOUDINI*USER_PREF_DIR and in there, create a directory called \_python3.9libs* if there isn't one.

Dive in and paste the .py in there. If you're on Linux, the file will be read-only so open the terminal inside the directory and do :

`~$ chmod +rwx nodegraphtitle.py`

This will give you read, write and execute permissions.

The final path should look like this : `$HOUDINI_USER_PREF_DIR/python3.9libs/nodegraphtitle.py`
which is :
`$HOME/houdiniXX.X/python3.9libs/nodegraphtitle.py`

Now you can edit the file however you'd like.

In the screengrab, all I did was :

```python
...
...
...
def networkEditorTitleLeft(editor):
    try:
        title = "I'm so custom"
        pwd = editor.pwd()
...
...
...
...
```

I don't know how often that script executes, so do tread carefully.

