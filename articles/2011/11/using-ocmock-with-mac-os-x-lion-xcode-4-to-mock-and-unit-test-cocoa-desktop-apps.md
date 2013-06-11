<!--
author: JP Richardson
publish: Tue Nov 29 2011 17:03:07 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/11/29/using-ocmock-with-mac-os-x-lion-xcode-4-to-mock-and-unit-test-cocoa-desktop-apps/
tags: Cocoa, Objective C
slug: 2011/11/29/using-ocmock-with-mac-os-x-lion-xcode-4-to-mock-and-unit-test-cocoa-desktop-apps
title: Using OCMock with Mac OS X Lion, Xcode 4, to Mock and Unit Test Cocoa Desktop Apps
-->



If you're trying to learn how to use [OCMock](http://ocmock.org/),
you'll encounter a number of articles dedicated to using it with iOS.
You won't find very many related to writing tests, mocks, and stubs for
your Cocoa desktop applications for OS X Lion. If you're writing your
apps to exclusively target OS X Lion (10.7), then this article will be
of use to you. I'm not sure if this technique will work for Snow Leopard
apps or not. But, since OS X Lion 10.7 is a 64 bit OS the distributable
library in the [downloadable package
(1.77)](http://ocmock.org/downloads/ocmock-1.77.dmg) will not work.
Hence, the reason for writing this article.

Here are the steps that you need to follow, you will create a brand new
demo application that will demo using OCMock. You should be able to
apply part of these instructions to your own project.

I should preface these instructions by stating that I am not an Xcode or
Objective C expert. Also, I'm using Xcode version 4.1 despite version
4.2 being available. I just haven't upgraded yet.

### Building 64-bit OCMock Library

This is the first necessary task. As stated earlier, the libOCMock.a
file found in the downloadable package is 32 bit only.

1.  Download the [latest OCMock
    package](http://ocmock.org/downloads/ocmock-1.77.dmg). At the time
    of this writing, it's version 1.77. It's conceivable that later
    versions will include the 64 bit version and you'll be able to skip
    these steps entirely.
2.  Mount the package and extract the Source and Release directories.
3.  Navigate to Source/ocmock-1.77 and open up OCMock.xcodeproj
4.  When Xcode opens up, click the root node "5 targets, multiple
    platforms" of the project navigator. The project's build settings
    will show up. Observe that there are 5 targets: OCMock, OCMockTests,
    OCMockPhoneSim, OCMockPhoneDevice, OCMockLib
5.  Notice how there are 4 projects schemes: OCMockPhoneSim,
    OCMockPhoneDevice, OCMock, and OCMockLib
6.  Notice how there are four products: OCMock.framework,
    OCMockTests.octest, libOCMock.a, and libOCMock.a
7.  Recall, that we are most interested in a 64 bit libOCMock.a
8.  Delete the target OCMockPhoneSim. Select it. Right click and hit
    'Delete' You should notice that one of the libOCMock.a products
    disappears, leaving us with one left.
9.  You should still be in the OCMockPhoneSim target with "My Mac
    64-bit" Select the target OCMockPhoneDevice. Change the Base SDK to
    Mac OS X 10.7 on every dropdown that you can.
10. Change Architecture to 64-bit on every drop down that you can.
11. Remove i386 from Valid Architecture.
12. Change scheme to "OCMockLib - My Mac 64-bit"
13. Click 'OCMockLib' target. Click 'Build Phases" and then expand the
    'Run Script', remove the following text:

```bash
    # combine lib files for device and simulator platforms into one

    lipo -create "${BUILD_DIR}/${BUILD_STYLE}-iphoneos/libOCMock.a" "${BUILD_DIR}/${BUILD_STYLE}-iphonesimulator/libOCMock.a" -output "${TARGET_BUILD_DIR}/Library/libOCMock.a"

    &nbsp;

    # copy the headers (we could have used a copy files build phase, too)

    cp -R "${BUILD_DIR}/${BUILD_STYLE}-iphoneos/Headers" "${TARGET_BUILD_DIR}/Library"
```

14. Click 'Build' from the 'Product' menu. 64 bit libOCMock.a should be
    built now. Right click libOCMock.a in the project navigator under
    the 'Products' group. Click 'Show in Finder'. Copy libOCMock.a and
    the directory OCMock founder in the Headers directory that is
    located in the same directory as libOCMock.a. Copy these two items
    to a location that you can find them later.

### Adding OCMock to the Demo Project

Now we'll add the library and header to a demo project.

1.  Open Xcode. Click File...New Project. Select Cocoa Application.
2.  Name it whatever you want. Make sure that you click "Include Unit
    Tests"
3.  Verify that the default test is working... click Product...Test You
    should get a test error in the testing file. If so, works as
    expected.
4.  Navigate to your project directory. Create a directory in it called
    'TestLibraries'
5.  Copy libOCMock.a and the folder 'OCMock' containing the header files
    into the 'TestLibraries' directory.
6.  You'll have a group (folder) that is named like so:
    (YOUR\_PROJECT\_NAME)Tests. We'll refer to this as the testing
    group. Right click it and click 'Add Files to..'
7.  Select the folder in your project directory that you created:
    'TestLibraries' Make sure that "Copy items into designations group's
    folder" is NOT checked since these files already exist at the
    project root. Select 'Create groups for any added folders'. Uncheck
    your project target and make sure that your testing target is
    checked.
8.  Go to the Test target build settings. Make sure that: "Library
    Search Paths" has "TestLibraries" in it. If not, you'll need to add
    the following string WITH THE QUOTES: "\${SRCROOT}/TestLibraries"
9.  In the Test target build settings, make sure that "Header Search
    Paths" has "TestLibraries" with recursive selected. If not, add the
    following string WITH THE QUOTES:  "\${SRCROOT}/TestLibraries"
    Select the 'recursive' option.
10. In the Test target build settings, locate 'Other Linker Flags', add:
    -ObjC -all\_load
11. In your implementation test file, locate the textExample method. Put
    this snippet in its place:

```objc
    #include <OCMock/OCMock.h> //put this at the top
    id mockString = [OCMockObject mockForClass:[NSString class]];

    [[[mockString stub] andReturn:@"MOCKS UP IN"] lowercaseString];

    STAssertEqualObjects([mockString lowercaseString], @"MOCKS UP IN", nil);
```

12. Click "Product" menu and "Build For Testing"
13. Then click "Product" and "Test" All should pass.

If you get the following error: 'unrecognized selector sent to instance'
then you didn't at the 'Other Linker Flags'

Hope this helps. References:

-   [Poking Objective-C with a Testing
    Stick](http://everburning.com/news/poking-objective-c-with-a-testing-stick/)
-   [OCMock and the
    iPhone](http://iamthewalr.us/blog/2008/11/ocmock-and-the-iphone/)
-   [Making Fun of Things with
    OCMock](http://alexvollmer.com/posts/2010/06/28/making-fun-of-things-with-ocmock/)
-   [Unit Testing in Xcode 4 Quick Start
    Guide](http://www.raywenderlich.com/3716/unit-testing-in-xcode-4-quick-start-guide)
-   [Testing Cocoa Controllers with
    OCMock](http://erik.doernenburg.com/2008/07/testing-cocoa-controllers-with-ocmock/)
-   [Creating an Xcode Project Template with GHUnit and
    OCMock](http://www.sunetos.com/items/2011/01/21/creating-an-xcode-project-template-with-ghunit-and-ocmock/)
-   [Getting OCMock to Work with an iPhone
    Project](http://jtigger-learning.wikidot.com/getting-ocmock-to-work-with-an-iphone-project)

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git easy.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson
