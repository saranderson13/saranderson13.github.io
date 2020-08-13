---
layout: post
title:      "The Anatomy and Usage of Paths "
date:       2020-08-13 05:27:53 +0000
permalink:  the_anatomy_and_usage_of_paths
---


Last week on the not-found blog I covered the [very very basics of moving around the terminal, with 'ls' and 'cd'](http://https://saranderson13.github.io/navigating_with_bash_for_super_noobs_noshame). I had intended to move on to things like making and modifying files and directories, but I want to make a quick detour to go a bit more into paths. Once again, this will be a 1 ★ complexity rating. These are the very basics. But understanding paths will help immensely with navigating and manipulating files from the terminal.

Paths are strings that indicate the unique location at which a file is stored. There are a lot of different syntaxes for paths, depending on which operating system and/or shell you're working with. When I say different syntaxes, I mean some will use a '/' to delineate directory separation, and some will use a '\'. Or some will use brackets, or colons or etc. [This wiki page shows all of the different ways that paths are written on different operating systems.](https://en.wikipedia.org/wiki/Path_(computing)#Representations_of_paths_by_operating_system_and_shell) My examples will be written in bash, which is a Unix shell, and what I'm most familiar with personally.

**I'm going to use this example tree for all of the examples: **
```
home
|---- kitchen
|     |---- cabinets
|     |     |---- pantry.file
|     |     |---- dishes
|     |     |     |---- plates.file
|     |     |     |---- cups.file
|     |     |     |---- bowls.file
|     |---- trash-can.file
|---- living-room
|     |---- seating
|     |     |---- couch.file
|     |     |---- loveseat.file
|     |     |---- beanbag-chair.file
|
```

** Note that `.file` is not really a file type. A file may be automatically assingned `.file` if the actual type is unknown. I'm just using it in the example so that you know which items in the trees are files, and which are directories. [For a more in depth explanation of .file, check this out.](https://file.org/extension/file))/

### Anatomy
A path consists of a bunch of directories, separated in some way. Or, if you're referencing a specific file, it's a bunch of directories followed by a file name, and it's file type. For example: 
- `home/kitchen` references the kitchen directory, which lives inside the home directory. 
- `home/kitchen/cabinets/dishes/cups.file` references the cups file.

### Absolute Paths & What They're Good For
(Spoiler, it isn't war.) There are two ways that paths can be expressed: absolute vs relative. 

An *absolute* path is a path written out in its entirety, from the context of your root directory. As in, the very top-most directory of your entire file system. It can be referenced from anywhere, and will return the file that you want it to return. Absolute paths come in handy, for some things, like when you need to access a file or folder in your browser, such as an `index.html` file. 

This is an example of an absolute path (on a Windows machine, mac would look a little different):

```
C:\Users\sarah\dev\labs\project-5\home\living-room\electronics\television.file
```

You can get the absolute path of a file in a few ways. In your code editor, if you right click the file there is usually an option to 'Copy Path' or 'Copy Relative Path'. If you're in Finder or Explorer, you can get the location from a file's properties, and then tack on your file name at the end. If you're in a bash terminal, you can use the command `pwd`

### Relative Paths
A *relative* path, is written from the context of the directory or file that you are currently in. If you're navigating around a project in your terminal, and you want to hop from one directory to another, it would be more convenient to use the relative path. To use and absolute path, you would need to know the exact full path leading up to the project folder. 

Lets look at an example of a relative path. Imagine for a moment, that you're in the `dishes` directory, and you want to get to the `seating` directory. You need to get up 3 directory levels, then down two. As covered in the last tutorial on navigating around in the terminal, you could do `cd ..` three times, then navigate back down from there. But there is a quicker way.

```
cd ../../../living-room/seating
```

Break that down. We know that `cd ..` takes you up one directory level. The `/` is the directory separator. By typing `../..` you are telling it to go up two directory levels, and you would be in the `kitchen`. By typing three,  `../../..`, you'd be in  `home`. You could do this indefinitely if you wanted to, at least until you hit your root directory.

From there, you add on your path as usual. `living-room/seating` is how you would get to the seating directory from your home directory.

---

So there are uses for both, and reasons to use one over the other in different situations. A relative path would mean nothing to your browser, unless you knew the directory your browser was in, and that directory's location relative to a common ancestor directory, which (eventually) contained the directory you're looking for, and navigate down that, which could be a TON of directories that you need to know by name. Needlessly complicated, when you can just give it the absolute path.

On the other hand, relative paths are generally used to reference files *within* a program because they are safer. As long as your file tree stays the same internally, you can move the entire project directory to a different location, and all of your relative paths would still work. If you were to hardcode all of your references with absolute paths, you would need to go in and change every single one. 


