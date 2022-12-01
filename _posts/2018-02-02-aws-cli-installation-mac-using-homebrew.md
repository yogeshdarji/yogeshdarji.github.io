---
layout: post
title:  "AWS CLI(Command Line Interface) Installation on Mac Using Homebrew"
---

Below are the steps to install awscli using homebrew on your mac:

    Install homebrew on your mac terminal if you don’t have one using this command
`
$xcode-select --install$ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
`

2. Install awscli using brew

```
$brew install awscli
```
3. Give permissions to pkgconfig
```
$chmod 755 /usr/local/lib/pkgconfig
```
4. If you get below warning

```
Warning: awscli 1.14.30 is already installed, it's just not linked.You can use `brew link awscli` to link this version.
```

5. Link your awscli

$brew link awscli

6. Check if it is working
```
$aws
usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]To see help text, you can run:aws helpaws <command> helpaws <command> <subcommand> help
```
Please comment below, I would be more than happy to help, if you would like to know more about AWS or have any specific questions.


