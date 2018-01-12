# Beginner Guide

## Why
This beginner guide is to help developers new to iOS and/or Swift not fall into some really common pitfalls. This document is phrased as a list of "rules" to be A) cheeky and B) help with remembering.

* [Rule #0: Don't be Rude](#rule-0-dont-be-rude)
* [Rule #1: Don't Be Cancerous](#rule-1-dont-be-cancerous)
* [Rule #2: Be Marxist](#rule-2-be-marxist)
* [Rule #3: Be Functional](#rule-3-be-functional)
* [Rule #4: Be Athiest](#rule-4-be-athiest)

# Rule #0: Don't be Rude
Never force your code with `!` - always ask `?` instead. Using `!` in your code is lazy, rude, and unacceptable.

Being polite will take extra work. You might need to write a few more lines of code or do some safety-checking. But it is always preferrable to be safe and polite, than risking your app crashing in the hands of your users.

There are three main areas where you will see `!` and `?` used in code.

* [Optionals](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID330)
* [Downcasting](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TypeCasting.html#//apple_ref/doc/uid/TP40014097-CH22-ID341)
* [Error Handling](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html)

In all 3 cases, it is preferred to use [Optional Binding](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/OptionalChaining.html) instead of forcing the code to do what you want.

Here's a _rude_ example to avoid:
```swift
let userInput = // some string value like "123" or "bob"
let convertedNumber = Int(userInput)
let sum = 5 + convertedNumber! // <--- BAD
```

Here is a preferred _polite_ example:
```swift
let userInput = // some string value like "123" or "bob"
if let convertedNumber = Int(userInput) {
  let sum = 5 + convertedNumber // <--- GOOD
} else {
  // handle the case where convertedNumber is nil
}
```

## Exception: Implicitly Unwrapped Properties
Apple did not update UIKit for Swift, and so you will see this Implicitly Unwrapped variables everywhere in your code when it comes to UIKit:

```swift
class MyViewController: UIViewController {
  @IBOutlet weak var backgroundImageView: UIImageView! // <--- WHAAAT!!! BAD
...
```

But this is _not a pattern_ you should repeat. You are still _forcing_ the code to work - and it is still rude.  When you give a type the `!` you are opening up your code to errors and crashing at run time.

Do a little bit of extra work and safely unwrap that value before using it.

# Rule #1: Don't Be Cancerous 
> Mutability in swift is a massive topic which cannot be covered quickly here, and I will likely create an entire guide on mutability later.
> -- editor

Mutability is a term which describes how your code can be changed at run-time. In almost all cases, mutability should be considered a _cancer_ in your code and avoided where possible.  However, unlike Rule #0, there are some _good_ exceptions to this rule.

The following areas in swift allow for mutability that should be avoided:
* [Avoid using `var`](#avoid-using-var)
* [Avoid using Objects](#avoid-using-objects)
* [Never use `mutating`](#never-use-mutating)

## Avoid using `var`
When you define a variable in swift, you can do so in two ways:
* `let` - a fixed reference to a value
* `var` - a mutable reference to a value

Please note that `let` does not declare _constants_. You can assign a mutable value to a `let` reference (like a `UILabel`). When you use `let`, you are creating a reference which can be assigned _only once_. When you use `var` you're creating a reference which can be assigned over and over.

In general, your code should prefer using `let` instead of `var`.

### Exception: Computed Properties
Using `var` for computed properties is perfectly acceptable and actually strongly encouraged.

### Exception: Building Values
The most common use for a `var` is that your code needs to take many inputs (like an array or dictionary) 

## Avoid using Objects
Prefer `struct` and `enum` over `class`. If you're coming from Javascript, Java, Objective-C, C++, or C# - your default reaction is to create an Object. In Swift, however, 
* [Andy Matsuchak - Controlling Complexity in Swift](https://academy.realm.io/posts/andy-matuschak-controlling-complexity/)

## Never use `mutating`
You can read the [swift style guideline](StyleGuide.md#mutating) on why `mutating` is bad.

Just *never* using `mutating`.  Don't.  There's NO REASON for you to use it.  It's there _only_ as an assist to bridge Swift and Objective-C.

# Rule #2: Be Marxist
Avoid race conditions and data errors by using `struct` and `enum` instead of `class`

# Rule #3: Be Functional
Avoid side-effects in your code by using functional patterns

# Rule #4: Be Athiest
Break God-Objects down into task-specific handlers
