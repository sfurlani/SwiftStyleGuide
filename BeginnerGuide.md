# Beginner Guide

## Why
This beginner guide is to help developers new to iOS and/or Swift not fall into some really common pitfalls. This document is phrased as a list of "rules" to be A) cheeky and B) help with remembering.

* [Rule #0: Be Polite](#rule-0-be-polite)
* [Rule #1: Be Pure](#rule-1-be-pure)
* [Rule #2: Be Marxist](#rule-2-be-marxist)
* [Rule #3: Be Functional](#rule-3-be-functional)
* [Rule #4: Be Athiest](#rule-4-be-athiest)

# Rule #0: Be Polite
Never force your code `!` - always ask `?` instead.

## Swift - Optionals
### What is an Optional?
[Optionals in Swift](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID330) are a data type that helps answer the question "Does this data exist or not?" Their main purpose is to remove the need for special return values or flags. Consider the following:

```swift
let userInput = "123"
let convertedNumber = Int(userInput)
```
In this case, `convertedNumber` is of type `Int?` - because we don't know whether or not `userInput` contained a valid number.  The example could have just as easily been:
```swift
let userInput = "jimmy"
let convertedNumber = Int(userInput)
```
In this case, `"jimmy"` doesn't convert to an integer. Swift provides the optional type so that methods can return `nil` (a value meaning "nothing") instead of crashing the application.

### Why should I be polite?
In our example, when we want to use the `convertedNumber`'s value we need to do what's called "unwrapping." In swift, you can unwrap politely by using `?` or you can be rude and use force-unwrapping with `!`.

Here's the rude example:
```swift
let userInput = // some string value
let convertedNumber = Int(userInput)
let sum = 5 + convertedNumber! // <--- here! BAD
```

When you run this code, it will probably work. You, the developer, will probably always put some value into `userInput` that nicely converts to an integer. **HOWEVER** if the user enters `"jimmy"` for `userInput` the rude example above will **CRASH YOUR APP**.

This is **BAD**.

Never, ever, force-unwrap variables in your code.

Using `!` in your code is lazy, rude, and unacceptable.

However, being polite in your code does require some extra work - just like in real life. You can avoid being rude by using `guard let` or `if let` statements.

#### `if let` Pattern
By using conditional unwrapping, we avoid the danger of crashing our application. 
```swift
let userInput = // some string value
if let convertedNumber = Int(userInput) {
  // here, convertedNumber exists and is an integer we can use
  let sum = 5 + convertedNumber // <--- no ! - GOOD
} else {
  // handle the case where convertedNumber is nil
}
```

#### `guard let` Pattern
Sometimes the `if let` pattern is too restricting - we need `convertedNumber` to exist outside of the `if` statement.  Here, we'll use a guard statement instead. This helps us avoid ["pyramid programming"](https://thatthinginswift.com/guard-statement-swift/) (which is ugly, but not necessarily bad, programming).
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

# Rule #1: Be Pure 
Avoid using `var`  
Never use the `mutating` keyword - why do you even know about this?

# Rule #2: Be Marxist
Avoid race conditions and data errors by using `struct` and `enum` instead of `class`

# Rule #3: Be Functional
Avoid side-effects in your code by using functional patterns

# Rule #4: Be Athiest
Break God-Objects down into task-specific handlers
