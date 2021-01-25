---
title: Command Line Interface
unit: Frontend Development
order: 5
---

We will be exploring the Command-Line Interface and learning to navigate our file system.

## The Prompt

When you are working with the Command-Line Interface, you will have a prompt. Sometimes the prompt will appear as a `$`. Other times you may see a `%`. These are just variations, but function the same. It tells you that the comptuer is ready for you to enter a command.

If you don't see a prompt, then that means that something is running. Typically there will be information letting you know that a script is running, but sometimes it's not super clear. In order to stop a script from running and get back to the prompt, you'll need to use the keyboard shortcut `ctl+c`. This is the same on most CLI terminals, so it will be the same whether you are on the Chromebook or the MacBook.

![Anatomy of the command line](../images/anatomy.png)
<small>Image Source: https://www.learnenough.com/command-line-tutorial/basics</small>

```bash
⌃A	# Move to beginning of line
⌃E	# Move to end of line
⌃U	# Delete to beginning of line
```

## Navigating Directories

Our files system is kind of like a tree. You're probably used to use a GUI (graphical user interface) to navigate and find files, but the CLI requires us to navigate using only text commands.

![Directory Tree](../images/linux-directory-tree.jpg)
<small>Image Source: www.linuxtrainingacademy.com</small>

```bash
cd pathname	# Change directory (folder) in the file system.
cd ..	# Move one level up (one folder) in the file system.
cd ~ # Move to the home directory (folder) in the file system.
ls	# List the directory (folder) system.
ls -al # List the directory (folder) system with additional details.
pwd # Print the working directory (folder).
```

## Other Important Commands

```bash
cp filename new_filename	# Copy a file to another folder.
mv filename new_location/filename	# Move a file to another folder.
touch filename # Create new file.
mkdir	dir_name # Creates a new directory (folder).
rmdir	dir_name # Remove a directory (folder) if it is empty.
rm -rf dir_name # Remove a directory (folder) and all its content. Can't be undone.
clear	# Clears the CLI window.
```

## Vim

Vim is a command-line text editor. Many developers use this to develop software. It is also something that many beginners get stuck in by accident. Vim requires you to use keyboard commands to quit and save files.

```bash
$ vi filename # Opens a file in Vim
```

Once you are in Vim you will not be able to escape. You'll need to issue a command to quit. Type `:q` to quit. If you do not see anything happen press the `esc` key and try again.

Here's a resource if you want to [Learn more about Vim Commands](https://www.radford.edu/~mhtay/CPSC120/VIM_Editor_Commands.htm).
