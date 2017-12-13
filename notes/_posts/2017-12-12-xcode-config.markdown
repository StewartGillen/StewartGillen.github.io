---
layout: post
title:  "How to maintain Xcode settings"
date:   2017-12-12 11:53:47 -0600
categories: xcode tip configuration
---
By default Xcode places all of its settings in the project's `.xcodeproj` bundle's `project.pbxproj` file. When starting out this is fine, but over time it becomes a problem. This file's markup is like a `plist`. Frequently over the course of your development you will need to change some values. Since Xcode manages this file it is in control of the order of the values and the format. Changes over time can be difficult to track because of this. If you use `git blame` to see git comments on why a setting is set it can be difficult to see why.

No descriptions
---------------

Another issue is that there are no comments for why the setting is changed from it's default value. A setting may have to be changed for supporting 3rd party components or for specific features. Not having a way to document and describe this will cause trouble if in the future a developer is looking to update the setting. Every setting overriding the default value needs to have a comment.

Also, it may be useful to define user-defined settings. These custom settings are used in your project or your builds. Not having a clear way to describe it's use and how it should be updated is a problem.

Every setting needs to have a reason. In code we have comments and in the worst case scenario we can use commit messages. But when the settings are in the project we can't 

project.pbxproj conflicts
---------------------------

Even if you are a single developer team working on a project merge conflicts can come up anytime you branch or fork a project. On multiple person teams you will definitely get merge conflicts. 

Merge conflicts are best resolved if you know the markup of the file. Often merge conflicts in the `project.pbxproj` file are difficult to resolve. Worse is the indeterminate nature of the order of items in the file.

No shared settings
------------------

Most projects start out with a single target, and only two schemes (debug and release). That's fine but eventually you will grow your project. By having shared settings adding additional schemes and targets is easier. 

Getting started
---------------

The goal is to move all settings defined in the project file and move them into the flat config files.

Create a group in your project and name it `Config`. Right click on the group and select `New File` then select a `Configuration Settings File`. Name this file `Shared`. 

Next you should create a config file for each scheme you have in your project. In the default case we have only `Debug` and `Release` and will create `Debug.xcconfig` and `Release.xcconfig` files respectively.

In these target config files we want to import the shared config file with the line:

`#include "Shared.xcconfig"`

Use configs
-----------

Now that we've our config skeletons we need to setup the project to use them. Go to your project settings, and click on the `Info` tab. Under the `Configurations` section you will see each scheme listed and expanding each scheme you see a project level and a target level. Next to each scheme at the project level click on the drop down and select the corresponding config we made above.

In my example I just need to set `ConfigExample` to either `Debug.xcconfig` or `Release.xcconfig`. 

Clean out project build settings
--------------------------------

Now that we have the project level configs setup we need to start moving settings into them. Go to your project settings and "Build Settings". Make sure you have "All" and "Levels" selected. These two settings allow us to see all the settings in the project and where the setting is coming from.

You should look at each line reading from right to left. Looking for the first place the setting is applied which will be highlighted in green. The far right side is where the "iOS Defaults" are defined. To the left of that is the config file settings, this is where we want any custom settings to be made. To the left of that is the project settings, this is where we want to make sure nothing is set, and thus nothing is green. Just scrolling the default settings made when creating a template there are a numerous settings that are made there. 

If it's already a default, leave it
-----------------------------------

When creating a new project from the template the base SDK is set to `iphoneos`, but that is also the default. Typically, we don't need this setting since it is a default, and we would normally delete settings that already specify the default value, but base SDK is special. It's used here to determine the type of settings, iOS settings in this example, for the whole project.

So we will copy the base SDK line and past it in our shared config file since that setting is the same for both `Debug` and `Release`.

Once it's in the config file we need to delete it from the project build settings by selecting the line and pressing delete.

Set scheme specific settings as needed
--------------------------------------

The next setting we see that is in the project build settings is `Build Active Architecture Only`. But this setting is only overridden in the debug build. So we copy that line and paste it in the debug config file. 

Further down is another that is scheme specific, `Debug Information Format`. `Debug` is set to `Dwarf` and `Release` is set to `Dwarf with dSYM File`. So copy each scheme specific setting and paste in its respective config file.

Wrapping up
-----------

After you have done this to your project and target build settings you should have everything defined in your three config files. Everything mostly will be in your shared config and a few specific settings will be in your debug and release configs.

Moving forward
--------------

Now whenever there is a new version of Xcode and the defaults change or new settings are added with initial values you will know by doing these exact same steps above discover them and move them to the appropriate configuration file.

Shared.xcconfig
---------------
```
SDKROOT = iphoneos
COPY_PHASE_STRIP = NO
IPHONEOS_DEPLOYMENT_TARGET = 11.2
ALWAYS_SEARCH_USER_PATHS = NO
CODE_SIGN_IDENTITY = iPhone Developer
GCC_DYNAMIC_NO_PIC = NO
GCC_NO_COMMON_BLOCKS = YES
GCC_C_LANGUAGE_STANDARD = gnu11
CLANG_CXX_LANGUAGE_STANDARD = gnu++14
CLANG_CXX_LIBRARY = libc++
CLANG_ENABLE_MODULES = YES
CLANG_ENABLE_OBJC_ARC = YES
ENABLE_STRICT_OBJC_MSGSEND = YES
CLANG_WARN_BLOCK_CAPTURE_AUTORELEASING = YES
CLANG_WARN_DOCUMENTATION_COMMENTS = YES
CLANG_WARN_EMPTY_BODY = YES
CLANG_WARN_BOOL_CONVERSION = YES
CLANG_WARN_CONSTANT_CONVERSION = YES
GCC_WARN_64_TO_32_BIT_CONVERSION = YES
CLANG_WARN_ENUM_CONVERSION = YES
CLANG_WARN_INT_CONVERSION = YES
CLANG_WARN_NON_LITERAL_NULL_CONVERSION = YES
CLANG_WARN_INFINITE_RECURSION = YES
GCC_WARN_ABOUT_RETURN_TYPE = YES_ERROR
CLANG_WARN_STRICT_PROTOTYPES = YES
CLANG_WARN_COMMA = YES
CLANG_WARN_UNGUARDED_AVAILABILITY = YES_AGGRESSIVE
GCC_WARN_UNINITIALIZED_AUTOS = YES_AGGRESSIVE
CLANG_WARN_UNREACHABLE_CODE = YES
GCC_WARN_UNUSED_FUNCTION = YES
GCC_WARN_UNUSED_VARIABLE = YES
CLANG_WARN_RANGE_LOOP_ANALYSIS = YES
CLANG_WARN_SUSPICIOUS_MOVE = YES
CLANG_WARN_DIRECT_OBJC_ISA_USAGE = YES_ERROR
CLANG_WARN__DUPLICATE_METHOD_MATCH = YES
CLANG_WARN_OBJC_LITERAL_CONVERSION = YES
GCC_WARN_UNDECLARED_SELECTOR = YES
CLANG_WARN_OBJC_ROOT_CLASS = YES_ERROR
CLANG_ANALYZER_NONNULL = YES
CLANG_ANALYZER_NUMBER_OBJECT_CONVERSION = YES_AGGRESSIVE
TARGETED_DEVICE_FAMILY = 1,2
LD_RUNPATH_SEARCH_PATHS = $(inherited) @executable_path/Frameworks
INFOPLIST_FILE = ConfigExample/Info.plist
PRODUCT_BUNDLE_IDENTIFIER = SLG.ConfigExample
PRODUCT_NAME = $(TARGET_NAME)
CODE_SIGN_STYLE = Automatic
ASSETCATALOG_COMPILER_APPICON_NAME = AppIcon
SWIFT_VERSION = 4.0
```

Debug.xcconfig
--------------
```
#include "Shared.xcconfig"

ONLY_ACTIVE_ARCH = YES
DEBUG_INFORMATION_FORMAT = dwarf
ENABLE_TESTABILITY = YES
GCC_OPTIMIZATION_LEVEL = 0
GCC_PREPROCESSOR_DEFINITIONS = DEBUG=1 $(inherited)
SWIFT_OPTIMIZATION_LEVEL = -Onone
SWIFT_ACTIVE_COMPILATION_CONDITIONS = DEBUG
MTL_ENABLE_DEBUG_INFO = YES
```

Release.xcconfig
----------------
```
#include "Shared.xcconfig"

DEBUG_INFORMATION_FORMAT = dwarf-with-dsym
VALIDATE_PRODUCT = YES
ENABLE_NS_ASSERTIONS = NO
SWIFT_OPTIMIZATION_LEVEL = -Owholemodule
MTL_ENABLE_DEBUG_INFO = NO
```