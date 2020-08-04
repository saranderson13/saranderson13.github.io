---
layout: post
title:      "Navigating with Bash for Super Noobs #noshame"
date:       2020-08-04 20:01:29 +0000
permalink:  navigating_with_bash_for_super_noobs_noshame
---

I'm going to start giving my blog posts complexity/difficulty ratings. This post has a 1 ★ complexity rating, which means that it's for total noobs. (Like I said though, #noshame. We've all been there.) All I'm saying is if you work with Bash with any frequency, you will know ALL of this, and I'll be explaining it down to what you probably consider to be a painfully basic level, so it won't be very helpful for you. But if you've just opened your terminal and you're like...whoa man, I have no idea where to start...I'm hoping I can be of some assistance. And there are a million tutorials on how to do what I'm about to explain, but it was honestly kind of hard to find something that explained things down to that incredibly basic level where I didn't immediately have several follow up questions.

Moving on with a little backstory. When I first started working heavily in the terminal, it felt like trying to walk through an unfamiliar house in the dark. In the beginning, working on a macbook, I had a chance to just dip my toe in. It was better to attempt to navigate and work with files using the Terminal, because I needed to learn how, (and trust me, you'll definitely need to know how), but I could still access my files through the Finder. When I got super frustrated trying to modify or find files in the Terminal I had the fallback of using the Finder. It was like training wheels. Then, for a variety of reasons, (the main one being my macbook is/was archaic and couldn't handle the most recent versions of many libraries, and also Homebrew yelled at me nearly every time with either deprication warnings or a flat refusal to download what I needed), I needed to switch to a windows laptop and use the Windows Subsystem for Linux (WSL). I didn't immediately know how to access the files in the file explorer because in the process of meticulously following directions to up my environment I created a shortcut command to go straight to the folder where all of my projects were. Stubbornly I decided to just go for it and force myself to get comfortable with terminal commands. It wasn't until about a month later that I decided to figure out where my files actually lived from the explorer's perspective, and by then I felt comfortable enough with the terminal that I didn't need to use the explorer. 

**The good news and the reason for my life story**: Everything worked pretty much the same because Mac OS Terminal and WSL using an Ubuntu terminal both respond to Bash. I am NOT going to get into anything under the surface of that. It's a lot to unpack and there are people who are far more qualified to explain what's going on with the differences between Unix (Mac OS) and Linux (WSL), and their respective shells. But they are similar enough for at least basic commands to work to the same effect, and this may help no matter which one you are using, or if you're using anything else where Bash commands work. 

### Navigating in a dark and unfamiliar house.
###### (AKA a guide to seeing what is in your directories, and then getting there.)
- - -
**First up, 'ls':**
```
ls
```
'ls' is short for 'List'. It will list out all directories and files inside a directory, albeit not always in a super easy-to-read way. Depending on the size of your terminal window it might make things line up kind of weird. But all the information is there. 

Just typing 'ls' will show you the files and directories that live the directory you're currently in. You could also follow up the command with a path and see the files and directories in *that* directory. More details on that below, including what it means to be 'in' a directory, once we get through the next bit about navigation. Because it includes some info on path anatomy. For now, just know that 'ls' is how you can 'turn on the light', if you will.
 
- - -  

**Now to actually get into one of those directories so that you can do some stuff with whatever is in there.**

```
cd [directory name]
```
'cd' stands for Change Directory, and can be used in a few different ways. (A directory is a folder... not trying to patronize anyone, but like I said, this is meant to be a resource for those with barely a toe in the pool.) You can use 'cd' to navigate to a directory by name, either one that is in the directory you are currently in, or one (or more) branches deeper. Say your file tree looks something like this:
```
home
|---- cool-project-directory
      |---- project-file1
      |---- project-file2
|---- another-project-directory
      |---- subdirectory
            |---- subdirectory-file1
```
**You want to get to your 'cool-project-directory'.** Using the syntax above, you do *not* include the brackets. That's just how you will commonly see documentation for commands. Either with [ ] or sometimes < >. You can also disregard a $ if you ever see one at the beginning of a line of code. (It was not clear to me at first that the $ and these brackets were not included.) So assuming you are in the 'home' directory, to get to your 'cool-project-directory', you would use this command:
```
cd cool-project-directory
```
You would then be *in* that folder, as if you had clicked on that folder in Finder or the file explorer. Note that to go into a directory that is directly inside the directory you're in, you don't need to preface the target directory with anything. You can just type its name after the 'cd'. Your terminal will change something like this:
```
# The time, followed by the current directory, then your typing line with the cd command.)
[13:43:20] home
// cd cool-project-directory

# The time, followed by the new directory, then your *empty* typing line.)
[13:43:25] cool-project-directory
// 
```
**You've just moved one step down. But, say you just went into the wrong folder. Honest mistake. And now want to go back to your home directory.**
```
[13:44:50] cool-project-directory
cd ..
```
The two periods after your 'cd' are saying that you want to go to the directory one step up from where you are. A single period would refer to your current directory.

**Where you really wanted to get to was the 'subdirectory' folder inside your 'another-project-directory' folder.** You have two options. You can navigate to your 'another-project-directory'. And then navigate to your 'subdirectory'. OR you can just do all of that with one command. First, lets see taking the steps one at a time.
```
[13:45:12] home
// cd another-project-directory

[13:45:13] another-project-directory
// cd subdirectory

[13:45:18] subdirectory
// 
```
A+. But we can do better. Navigating directly there does require knowing what folders are in 'another-project-directory', but lets say you already know that because you've made brilliant use of the 'ls' command.
```
[13:45:12] home
// cd another-project-directory/subdirectory

[13:45:18] subdirectory
// 
```
A++! By putting the '/subdirectory' after your 'another-project-directory' you indicated that it was the directory *within* the 'another-project-directory' that you wanted to get to. A '/' will be the seperator between directories OR files within a path. But remember when we were in the 'cool-project-directory' and you knew you wanted to get to the subfolder directory? That's possible too.
```
[13:44:50] cool-project-directory
// ../another-project-directory/subdirectory

[13:45:12] subdirectory
// 
```
That command utilizes all three of the path anatomies broken down above. the '..' takes you back to the directory one step above 'cool-project-directory', and from there, your nested path is the same as if you were *actually in* that 'home' directory to begin with.

- - -

As you become more aquainted with the idea of visualizing the anatomy of your file tree in your mind, as opposed to being able to see everything laid out in a Finder or Explorer, navigating will become easier. In fact, using 'ls', you can even check to see what directories and files are inside a target directory before trying to go there. 

The premise: You are in the 'cool-project-directory' and you know the folder you want to hop into is inside the 'another-project-directory' but you can't remember what it's called.
```
ls ../another-project-directory
```
This would show you all of the directories and folders in there. You could go a step further and see the contents of the 'subdirectory' too:
```
ls ../another-project-directory/subdirectory
```

- - -

**This will be very helpful when you first open your terminal, and it dumps you into the root of all of your directories.** If when you open your terminal you see the time followed by a '~', you're probably in the top-most directory of your drive, or at least a higher up one than you'd like to be. You may have set it to open into something specific in your settings, or if someone else helped you set things up, they might have done it without really explaining what was going on. But you now have the tools to find your bearings. Hit up that 'ls', and see where you can go from there. And keep doing that until you find what you're looking for. Trust me, it'll get a lot less dark and scary eventually.

So, I hope this was helpful. Next week on the not-found blog: Some Interior Design, Now That you Have the Lights On. (AKA, creating, deleting, relocating and renaming!)


