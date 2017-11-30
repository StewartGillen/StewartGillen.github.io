---
layout: post
title:  "How to maintain Xcode configuration"
date:   2017-11-19 09:50:47 -0600
categories: xcode tip configuration
---
By default Xcode places all of its settings in the project's `.xcodeproj` bundle's `project.pbxproj` file. When starting out this is fine, but over time it becomes a problem. This file's markup is like a `plist`. Frequently over the course of your development you will need to change some values. Since Xcode manages this file it is in control of the order of the values and the format. Changes over time can be difficult to track because of this. If you use `git blame` to see git comments on why a setting is set it can be difficult to see why.

No descriptions
---------------

Another issue is that there are no comments for why the setting is changed from it's default value. A setting may have to be changed for supporting 3rd party components or for specific features. Not having a way to document and describe this will cause trouble if in the future a developer is looking to update the setting. Every setting overriding the default value needs to have a comment.

Also, it may be useful to define user-defined settings. These custom settings are used in your project or your builds. Not having a clear way to describe it's use and how it should be updated is a problem.

Every setting needs to have a reason. In code we have comments and in the worst case scenario we can use commit messages. But when the settings are in the project we can't 

`project.pbxproj` conflicts
---------------------------

Even if you are a single developer team working on a project merge conflicts can come up anytime you branch or fork a project. On multiple person teams you will definitely get merge conflicts. 

Merge conflicts are best resolved if you know the markup of the file. Often merge conflicts in the `project.pbxproj` file are difficult to resolve. Worse is the indeterminate nature of the order of items in the file.

No shared settings
------------------

Most projects start out with a single target, and only two schemes (debug and release). That's fine but eventually you will grow your project. By having shared settings adding additional schemes and targets is easier. 

Getting started
---------------

The goal is to have no settings in the project file. 

Create a group in your project and name it `Config`. Right click on the group and select `New File` then select a `Configuration Settings File`. Name this file `Shared`. 

Copy shared settings
--------------------

SDKROOT = iphoneos

