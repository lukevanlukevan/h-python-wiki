---
title: Houdini Types
author:
    [
        { name: "Igor Elovikov", link: "https://github.com/igor-elovikov" },
        { name: "Luke Van", link: "https://github.com/lukevanlukevan" },
    ]
---

# Houdini Types

When working with an [External Code Editor](#external-code-editor), you will see I often use `import hou` to get autocomplete suggestions. The only issue is natively the hou module errors out as needing `self` as the first argument for each function. This is easy to get around, as we can install [Houdini Types](https://pypi.org/project/types-houdini/).

To install these, its useful to put them in a location you can link to and also that is somewhat "linked" to Houdini.

To install these, open the Houdini shell by going to Windows -> Shell, then copy and paste this line:

`python -m pip install types-houdini --target %HOUDINI_USER_PREF_DIR%/python3.9libs`

It's important to do this in the Houdini shell, otherwise it just gets dropped into a location called "%HOUDINI_USER_PREF_DIR%".

Once that's installed, open up VSCode and install Pylance if not already installed. Open your user settings JSON and find the `"python.analysis.extraPaths"` line, or create it if missing.

Then, add 2 paths here:

```json
"C:\\Users\\YOUR_USERNAME\\Documents\\houdini19.5\\python3.9libs",
"C:\\Program Files\\Side Effects Software\\Houdini 19.5.493\\python39\\lib\\site-packages-forced",

```

These 2 lines are easily got using the Houdini shell by using `echo %HOUDINI_USER_PREF_DIR%/python3.9libs` and `echo %HFS/python39/lib/site-packages-forced`.
