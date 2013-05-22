---
layout: post
category: lessons
title:  "Objective-C Coding Convention and Best Practices"
tags: [beginner, objc, tutorial]
---
{% include JB/setup %}

Most of these guidelines are to match Apple's documentation and community-accepted best practices. Some are derived some personal preference. This document aims to set a standard way of doing things so everyone can do things the same way. If there is something you are not particularly fond of, it is encouraged to do it anyway to be consistent with everyone else.

This document is mainly targeted toward iOS development, but definitely applies to Mac as well.

## Operators


{% highlight objective-c %}
NSString *foo = @"bar";
NSInteger answer = 42;
answer += 9;
answer++;
answer = 40 + 2;
{% endhighlight %}

Operators separated should **always** be surrounded by spaces unless there is only one operand.

The `++`, `--`, etc are preferred to be after the variable instead of before to be consistent with other operators.


## Types

`NSInteger` and `NSUInteger` should be used instead of `int`, `long`, etc per Apple's best practices and 64-bit safety. `CGFloat` is preferred over `float` for the same reasons. This future proofs code for 64-bit platforms.

All Apple types should be used over primitive ones. For example, if you are working with time intervals, use `NSTimeInterval` instead of `double` even though it is synonymous. This is considered best practice and makes for clearer code.

`true` and `false` are `Bool` values, `TRUE` and `FALSE` are `boolean` values, `YES` and `NO` are `BOOL` values. We are using `BOOL` values in 99% cases.

`nil` is a pointer value, and `NULL` is a pointer value to a pointer or a structure. So we usually use `NULL` when value is `CGColorRef` or `NSError **`, and `nil` is for `UIColor *` and `NSError *`.


## Indentions

Spaces and Tabs are both working greate in Xcode, Spaces are better when communicate between different materials, like viewing on github. Tabs are better when coding and navigating without mouse. Tabs is better supported by Xcode, and most of Xcode sample documents are using tabs for indentions.

We should **always** using Tabs for indention on Objective-C code, you should set your Xcode to emit tabs when you hit tabs.


## Methods


{% highlight objective-c %}
- (void)someMethod
{
    // Do stuff
}

- (NSString *)stringByReplacingOccurrencesOfString:(NSString *)target withString:(NSString *)replacement
{
    return nil;
}
{% endhighlight %}


There should **always** be a space between the `-` or `+` and the return type (`(void)` in this example). There should **never** be a space between the return type and the method name.

There should **never** be a space before or after colons. If the parameter type is a pointer, there should **always** be a space between the class and the `*`.

There should **always** be a space between the end of the method and the opening bracket. The opening bracket should **always** be on the following line.

There should **always** be one new line between methods, and two new lines between `@interface`s, `@protocol`s and `@implementation`s, This matches some Xcode templates.


## Pragma Mark and Implementation Organization

An excerpt of a UIView:


{% highlight objective-c %}
#pragma mark - NSObject
- (void)dealloc
{
    // Release
    [super dealloc];
}

#pragma mark - UIView
- (id)layoutSubviews
{
    // Stuff
}

- (void)drawRect:(CGRect)rect
{
    // Drawing code
}
{% endhighlight %}

Methods should be grouped by inheritance. In the above example, if some `UIResponder` methods were used, they should go between the `NSObject` and `UIView` methods since that's where they fall in the inheritance chain.

There should **never** be a new line between pragma mark and methods.


## Control Structures

There should **always** be a space after the control structure (i.e. `if`, `else`, etc).


### If/Else

{% highlight objective-c %}
if (button.enabled) {
    // Stuff
} else if (otherButton.enabled) {
    // Other stuff
} else {
    // More stuf
}
{% endhighlight %}

`else` statements should begin on the same line as their preceding `if` statement.
    
{% highlight objective-c %}
// Comment explaining the conditional
if (something) {
    // Do stuff
}

// Comment explaining the alternative
else {
    // Do other stuff
}
{% endhighlight %}

If comments are desired around the `if` and `else` statement, they should be formatted like the example above.


### Switch

{% highlight objective-c %}
switch (something.state) {
    case 0: {
        // Something
        break;
    }
    
    case 1: {
        // Something
        break;
    }
    
    case 2:
    case 3: {
        // Something
        break;
    }
    
    default: {
        // Something
        break;
    }
}
{% endhighlight %}

Brackets are desired around each case. If multiple cases are used, they should be on separate lines. `default` should **always** be the last case and should **always** be included.


### For

{% highlight objective-c %}
for (NSInteger i = 0; i < 10; i++) {
    // Do something
}


for (NSString *key in dictionary) {
    // Do something
}
{% endhighlight %}

When iterating using integers, it is preferred to start at `0` and use `<` rather than starting at `1` and using `<=`. Fast enumeration is generally preferred.


### While

{% highlight objective-c %}
while (something < somethingElse) {
    // Do something
}
{% endhighlight %}

## Import

**Always** use `@class` whenever possible in header files instead of `#import` since it has a slight compile time performance boost.

From the [Objective-C Programming Guide](http://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ObjectiveC/ObjC.pdf) (Page 38):

> The @class directive minimizes the amount of code seen by the compiler and linker, and is therefore the simplest way to give a forward declaration of a class name. Being simple, it avoids potential problems that may come with importing files that import still other files. For example, if one class declares a statically typed instance variable of another class, and their two interface files import each other, neither class may compile correctly.

### Header Prefix

Adding frameworks that are used in the majority of a project to a header prefix is preferred. If these frameworks are in the header prefix, they should **never** be imported in source files in the project.

For example, if a header prefix looks like the following:

{% highlight objective-c %}
#ifdef __OBJC__
    #import <Foundation/Foundation.h>
    #import <UIKit/UIKit.h>
#endif
{% endhighlight %}

`#import <Foundation/Foundation.h>` should never occur in the project outside of the header prefix.

## Properties

{% highlight objective-c %}
@property (nonatomic, strong) UIColor *topColor;
@property (nonatomic, assign) CGSize shadowOffset;
@property (nonatomic, weak, readonly) UIActivityIndicatorView *activityIndicator;
@property (nonatomic, assign, getter=isLoading) BOOL loading;
@property (nonatomic, weak) id<MyViewDelegate> delegate;
{% endhighlight %}

If the property is `nonatomic`, it should be first. The next option should **always** be `strong` or `assign`, `assign` can be omitted when value is not a pointer. `readonly` should be the next option if it is specified. `readwrite` should never be specified in header file. `readwrite` should only be used in class extensions. `getter` or `setter` should be last. `setter` should rarely be used.

For UIViewController, UIView and CALayer subclasses, component properties can be `weak`, and better be. Since those will get released when container get released, that matches the original idea, also this brings more coding check while creating them

`Delegate`s/`DataSource`s must be `weak` references.

**Always** add a space after `@property` and before property type.

## Private Methods and Properties

{% highlight objective-c %}
//MyShoeTier.h

@interface MyShoeTier : NSObject
{
	...
}

@property (noatomic, strong, readonly) MyShoe *shoe;

@end

//MyShoeTier.m

@interface MyShoeTier ()

@property (nonatomic, strong, readwrite) MyShoe *shoe;
@property (nonaomic, strong) NSMutableArray *laces;

@end

@implementation MyShoeTier
{
    ...
}
...

@end
{% endhighlight %}

Private methods should **always** be created in a class extension for simplicity since a named category can't be used if it adds or modifies any properties.

**Always** add a new line between `@interface` and `@property`s and before `@end`


## Extern, Const, and Static

{% highlight objective-c %}
extern NSString * const kMyConstant;
extern NSString * MyExternString;
static NSString * const kMyStaticConstant;
static NSString * staticString;
{% endhighlight %}

If a value should be seen outside this class, it should **always** expose it in its header file. When this happens, the constant name should **always** follow project prefix naming, otherwise, it can be started with `k`.


## Naming

Naming rules are very important in maintainable code. Objective-C method names tend to be very long, but this has the benefit that a block of code can almost read like prose, thus rendering many comments unnecessary.

When writing pure Objective-C code, we mostly follow standard [Objective-C naming rules](http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html).

In general, everything should be prefixed with a 2-3 letter prefix. Longer prefixes are acceptable, but not recommended.

It is a good idea to prefix classes with an application specific application if it is application specific code. If there is code you plan on using in other applications or open sourcing, it is a good idea to do something specific to your or your company for the prefix.

If you company name is *Awesome Buckets* and you have an application named *Bucket Hunter*, here are a few examples:

{% highlight objective-c %}
ABLoadingView // Simple view that can be used in other applications
BHAppDelegate // Application specific code
BHLoadingView // `ABLoadingView` customized for the Bucket Hunter application
{% endhighlight %}


## Enums

{% highlight objective-c %}
enum {
    Foo,
    Bar
};

typedef NS_ENUM(NSUInteger, SSLoadingViewStyle) {
    SSLoadingViewStyleLight,
    SSLoadingViewStyleDark
};

typedef NS_OPTIONS(NSUInteger, SSHUDViewStyle) {
    SSHUDViewStyleLight = 1 << 1,
    SSHUDViewStyleDark = 1 << 2,
};
{% endhighlight %}

We should **always** using `NS_ENUM` macro for `enum` and `NS_OPTOIONS` macro for mask option.

## Blocks

{% highlight objective-c %}

typedef void (^items_block_t)(NSArray *items, NSError *error);

+ (void)createStripeCard:(STPCard *)card completion:(void (^)(NSNumber *tokenId, NSError *error))block;

CAAnimation *(^doorAnimation)(CGFloat) = ^(CGFloat degree) {
    ...
}
{% endhighlight %}



