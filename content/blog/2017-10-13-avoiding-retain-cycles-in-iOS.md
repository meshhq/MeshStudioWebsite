---
title: Avoiding Retain Cycles in iOS
sub_title: Using the tools in Xcode to debug retain cycles in an ARC project.
tags: []
date: 2017-10-13
publishdate: 2017-10-13
hero_image: /images/blog/2017-10-13-avoiding-retain-cycles-in-iOS/retain-cycle-hero.png
author:
  name: Reid Weber
  url: https://medium.com/@reidweber_34407
  mail: reid@meshstudio.io
  avatar: "https://avatars2.githubusercontent.com/u/8824846?s=460&v=4"
---

Retain cycles can be easily overlooked in the post ARC (automatic reference counting) world of iOS. They can quickly balloon out of control and crash your app with very cryptic crash logs if you don’t know how to catch them.

For those who may not be familiar, retain cycles are when two objects hold strong references to each other. Objects are only deallocated when they are not being held by strong references, so two objects that are referencing each other will never be deallocated. Enter Instruments!

Xcode’s Instruments provides a number of very useful tools you can use to monitor battery usage, networking calls, track zombie objects and monitor memory allocation. 
The allocations tool is the one I’ve found most useful in the past for spotting retain cycles. It’s extremely useful for tracking the reference counts of objects living in your application.

To access instruments, go Product→Profile (or hit ⌘+i). When Xcode is finished profiling the app, select the allocations tool and hit the ‘record’ button in the upper left. Instruments will start logging everything that calls [`malloc`] in your app. Use the search bar in the bottom right to filter out the system allocations you have no control over.

| ![Allocations tool.](/images/blog/2017-10-13-avioding-retain-cycles-in-iOS/allocation-tool.png) |
|:--:|
|Our app running in the allocations tool.|

In my example, I’m creating a retain cycle by having the parent view controller hold a strong reference to its child view controller. The child is also holding a strong reference to the parent. When the view controller’s UINavigationController’s stock ‘back’ button is pressed, it’s presented view controller is removed from the super view and should be deallocated. Since the root view controller is still hanging on to the strong reference to the child, the instance of `SecondViewController` will still be retained until the parent’s reference is removed.

```objective-c
SecondViewController *viewController = (SecondViewController *)segue.destinationViewController;
viewController.retainedParent = self;
self.retainedChild = viewController;
```
(Here is the simple storyboard we’re using for this example.)

| ![Storyboard](/images/blog/2017-10-13-avioding-retain-cycles-in-iOS/storyboard.png) |
|:--:|
|Our app's simple storyboard.|

You can search for the allocation information of your custom classes with the search bar in the lower left. If I search for `SecondViewController` we can see that, even after it popped off screen, it is still being retained by its former parent view controller.

| ![SecondViewController allocation](/images/blog/2017-10-13-avioding-retain-cycles-in-iOS/vc-allocation.png) |
|:--:|
|Our Second View Controller is allocated.|

You can also view a detailed view of a particular instance by clicking the little arrow to the right of the class name. This detail view will provide a stack trace that can be helpful for finding where allocation calls are originating.

| ![Stack Trace](/images/blog/2017-10-13-avioding-retain-cycles-in-iOS/stack-trace.png) |
|:--:|
|Seeing the stacktrace from `malloc` to the Storyboard calling `instantiateViewController`|

As we illustrated above, one way to create a retain cycle is to create a strong reference between two objects when the first of those objects already has a strong reference to the second. Every strong reference to an NSObject will increment that object’s `reference count`. When memory is being deallocated, an object’s reference count is decremented when other objects who had strong references to it are deallocated. If two objects strongly reference each other, then it will create a loop (memory leak), and neither will ever be deallocated.

(Note: iOS storyboards hold strong references to all of the UIViews created in them. This is why IBOutlet references are weak by default)

Keeping an eye on references is especially important when using the common `delegate` pattern in iOS. Delegates should always be accessed through a weak reference.

The other most common way to create a retain cycle in iOS is referencing `self` from within an anonymous function (‘block’). You commonly see these mistakes while using GCD, or one of the common callbacks in AFNetworking. You always need create a `__weak` reference before using `self` in a block that is owned by that same object. The code below will create a retain cycle.

```
// Will create retain cycle.
  
dispatch_async(dispatch_get_main_queue(), ^{
    [self performAnAction];
});
  
// Will NOT create retain cycle.

__weak ViewController *weakSelf = self;
dispatch_async(dispatch_get_main_queue(), ^{
    [weakSelf performAnAction];
});
```

Sometimes things aren’t always so straightforward though. That’s why becoming familiar with the allocations tool can be a real lifesaver for debugging retain cycles.