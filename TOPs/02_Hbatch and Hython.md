---
title: Hbatch and Hython
author: [{ name: "WaffleBoyTom", link: "https://www.github.com/WaffleBoyTom" }]
---

# Hbatch and Hython

Let's run TOPnets from the terminal using hbatch and hython

I have a simple TOPnet that creates directories and subdirectories, kind of like what the `Set Project`option does in Houdini : `$HIP/project_{wedgeindex}/tex|flips|geo|render`

`hbatch` is hscript, we'll use the python equivalent `hython` later.

when running `hbatch $myhipfile` or `hython $myhipfile` , it will open a textport or a python shell where you can input commands interactively. From there you can use whatever hscript or python commands to cook your topnet. Let's be lazy and run everything with a single command.

`hbatch` lets you use the `-c` flag to specify a `.cmd` file

`test.cmd`

```c
echo "Cooking Topnet"
topcook /obj/mytopnet
echo "Done"
quit
```

Then you can run:

`hbatch -v -c test.cmd $myfile`.

`-v` is the verbose flag.

"but this is a python wiki, I don't wanna hear about hscript".

Let's take a look at `hython`:

`$HHP/pdgjob/topcook.py` is a python utility script provided by SideFx that lets you run a topnet from the command line.

```python
hython $HHP/pdgjob/topcook.py --hip $myfile --toppath /obj/create_directories_and_sub_directories.
```

`--hip` expects a hipfile.
`--toppath` expects a topnet.

