# Beginner Guide

## Why
This beginner guide is to help developers new to iOS and/or Swift not fall into some really common pitfalls. This document is phrased as a list of "rules" to be A) cheeky and B) help with remembering.

* [Rule #0: Be Polite](#rule-0-be-polite)
* [Rule #1: Be Pure](#rule-1-be-pure)
* [Rule #2: Be Marxist](#rule-2-be-marxist)
* [Rule #3: Be Functional](#rule-3-be-functional)
* [Rule #4: Be Athiest](#rule-4-be-athiest)

# Rule #0: Be Polite
Never force your code `!` - always ask `?` instead. Using `!` in your code is lazy, rude, and unacceptable.

## Optionals
[Optionals in Swift](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID330) are a data type that helps answer the question "Does this data exist or not?" Their main purpose is to remove the need for special return values or flags. In swift, you can use an optional's value by unwrap politely by using `?` or you can be rude and use force-unwrapping with `!`.

Here's the rude example:
```swift
let userInput = // some string value
let convertedNumber = Int(userInput)
let sum = 5 + convertedNumber! // <--- here! BAD
```

This is **BAD**. Never force-unwrap optionals.

#### `if let` Pattern
By using Optional Binding, we avoid the danger of crashing our application. 
```swift
let userInput = // some string value
if let convertedNumber = Int(userInput) {
  // here, convertedNumber exists and is an integer we can use
  let sum = 5 + convertedNumber // <--- no ! - GOOD
} else {
  // handle the case where convertedNumber is nil
}
```

Sometimes you will see code that looks like this:
```swift
let userInput = // some string value
let convertedNumber = Int(userInput)
if convertedNumber != nil {
  // while this is technically safe, it is still rude to use !
  let sum = 5 + convertedNumber! // <--- here! BAD
} else {
  // handle the case where convertedNumber is nil
}
```
While _technically_ ok, this code still uses force-unwrapping and everyone should avoid it.

#### `guard let` Pattern
Sometimes the `if let` pattern is too restricting - we need `convertedNumber` to exist outside of the `if` statement.  Here, we'll use a guard statement with Optional Binding. This helps us avoid ["pyramid programming"](https://thatthinginswift.com/guard-statement-swift/) (which is ugly, but not necessarily bad, programming).
```swift
let userInput = // some string value
guard let convertedNumber = Int(userInput) else {
  // handle the case where convertedNumber is nil
  return // guard statements must always exit the current scope
}

// Continue on with your code as if convertedNumber exists
let sum = 5 + convertedNumber // <--- no ! - GOOD
```

There are many other patterns you can use to do conditional unwrapping but remember! Never Force! Don't use `!`

## Downcasting
[Swift, like other languages, allows converting a value's type into another type.](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TypeCasting.html#//apple_ref/doc/uid/TP40014097-CH22-ID341) Swift offers two forms of downcasting - conditional `as?` and forced `as!`. Just like with optionals, you need to be polite and avoid forcing.

Here is the rude example:
```swift
let userInput: Any
let userNumber = userInput as! Int // <--- here! BAD
let sum = 5 + userNumber
```

This will crash your app at runtime. Instead, use optional binding (like above) to safely and politely downcast.
```swift
let userInput: Any
if let userNumber = userInput as? Int {
  // userNumber is an Int, and we can use it safely
  let sum = 5 + userNumber
} else {
  // handle the case where userInput is not an integer
}
```

## Error Handling
[Swift has Error Handling](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html) and in addition to re-throwing or handling via a `do/catch` block, you can convert Errors into optionals using `try?` and `try!`.

However, never use `try!`. This is rude and will crash your app.
```swift
let z = try! someThrowingFunction()
```
Don't do this. Even if you think "nah, this'll never happen at runtime" it is RUDE and BAD.

Instead, use `try?` with Optional Bidning to make sure your code is safe
```swift
if let x = try? someThrowingFunction() {
  // x is safe to use
} else {
  // handle case where an error was thrown
}
```

## Exception: Implicitly Unwrapped
So... there's always exceptions to rules, and in iOS there is a BIG one.

I mentioned before that using `!` us rude, lazy, and unacceptable programming.

Well, Apple decided to be *LAZY* and not update UIKit for Swift.

Hence, you will see this Implicitly Unwrapped variables everywhere in your code:

```swift
class MyViewController: UIViewController {
  @IBOutlet weak var backgroundImageView: UIImageView! // <--- WHAAAT!!! BAD
...
```

Here, except for a short period of time between when the `UIViewController` has been `init` and `viewDidLoad`, UIKit says you can assume that IBOutlets are not-nil.

But this is not a pattern you should repeat. You are still _forcing_ the code to work - and it is still rude.  When you give a type the `!` you are opening up your code to errors and crashing at run time.

Do a little bit of extra work and safely unwrap that value before using it.


# Rule #1: Be Pure 
Avoid using `var`  
Never use the `mutating` keyword - why do you even know about this?

# Rule #2: Be Marxist
Avoid race conditions and data errors by using `struct` and `enum` instead of `class`

# Rule #3: Be Functional
Avoid side-effects in your code by using functional patterns

# Rule #4: Be Athiest
Break God-Objects down into task-specific handlers
