---
title: Integrate GitHooks using Husky.NET
date: 2024-06-01 22:44:00 +05:30
tags:
- automation
- devops
layout: post
---

GitHooks are special scripts that Git executes before or after events such as: commit, push, and receive. These scripts are used to automate and customize Git's internal behavior and to trigger customizable actions at key points in the development life cycle.

These hooks reside in the .git/hooks directory of every Git repository and are not copied to the clone repositories upon 'git clone'. The hooks are all shell scripts, and you can write them in any scripting language supported by your environment.

## Why We Use GitHooks

The primary reason for using GitHooks is to automate and streamline your development process. Here are some specific ways you can use GitHooks:

1. **Enforce coding standards**: You can use GitHooks to automatically check the code being committed and reject the commit if it doesn't adhere to your coding standards.

2. **Automatic testing**: GitHooks can automatically run unit tests on your code every time you commit changes, catching bugs early in the development process.

3. **Integration with issue tracking**: You can link your GitHooks to your issue tracking system, so every commit is associated with a specific issue.

4. **Prevent unwanted actions**: GitHooks can be used to prevent certain actions, like direct commits to main or master branches, ensuring that all changes go through the appropriate review and testing processes.

[Husky.NET](http://husky.net/) is a .NET library that makes managing GitHooks in your .NET projects easier. By integrating GitHooks using [Husky.NET](http://husky.net/), you can improve your project's code quality and streamline your development process.

## Integration Steps

1. **Install [Husky.NET](http://husky.net/)**:

   Open a command prompt or terminal in your project’s root directory.

       cd <Your project root directory>
       dotnet new tool-manifest
       dotnet tool install Husky

2. **Attach Husky to your project**

   To attach Husky to your project, you can use the following command:

       dotnet husky attach <path-to-project-file>

   This will add the required configuration to your project file.

       <Target Name="husky" BeforeTargets="Build" Condition=" '$(Configuration)' == 'Debug' ">
         <Exec Command="dotnet tool restore" StandardOutputImportance="Low" StandardErrorImportance="High" />
         <Exec Command="dotnet husky install" StandardOutputImportance="Low" StandardErrorImportance="High" WorkingDirectory="../../" />
         <!--Update this to the relative path to your project root dir -->
       </Target>

### Integrate GitHook with Visual Studio

* Install Git. [Git - Downloads](https://git-scm.com/downloads)

* Add Git bin location to Window Path Variable(automatically done via installer).

* Open Visual Studio, Go Tools menu → Options → Projects and Solutions → Web Package Management

  * Remove the current GIT Path from the external tools location.

  * Add the Git Path location.

  ![Setup+Screenshot](/uploads/2e429e26-4ab8-4514-92af-57f6fbf5f59b.png)

* Restart Visual Studio.

* Now GitHooks Integrate with Visual Studio.