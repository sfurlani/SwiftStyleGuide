# Swift 3.0 Style Guide

This document is intended as a style guide for Swift written from the perspective of a long-time Objective-C developer.  As such, there's a lot of "why" included.

#### Thanks

* to github user `Nartiles` for some examples from [their 2.0 guide](https://github.com/nartiles/swift-style-guide)
* to Ray Wenderlich [for his guide](https://github.com/raywenderlich/swift-style-guide)
* to NatashaTheRobot for 
  * [KVO Alternatives](http://natashatherobot.com/ios-a-beautiful-way-of-styling-iboutlets-in-swift/)
  * [Railroad Programming](http://natashatherobot.com/swift-guard-better-than-if)

***

# Table of Contents

* [Naming and Code Clarity](#naming-and-code-clarity)
 * [Spacing](#spacing)
 * [Conditionals](#conditionals)
    - [Ternary Operator](#ternary-operator) 
    - [Nil Coalescing Operator](#nil-coalescing-operator)
    - [Unwrapping Optionals](#unwrapping-optionals)
    - [Labeled Statements](#Labeled-Statements)
 * [Type Inference](#type-inference)
 * [Closures](#closures)
 * [Default Parameters](#default-parameters)
 * [Protocol Naming](#protocol-names)
 * [Type Naming](#type-names)
 * [Type Conformity](#type-conformity)
 * [Variable Naming](#variable-naming)
 * [Verbosity](#verbosity)
 * [Operators](#operators)
 * [Extensions](#extensions)
 * [Nesting](#nesting)
 * [Comments](#comments)
 * [Documentation](#documentation)
 * [Singletons](#singletons)
 * [Property Observing](#Property-Observing)
* [Mutability](#mutability)
 * [`let` and `var`](#let-and-var)
 * [`mutating`](#mutating)
* [Failure Handling](#failure-handling)
 * [Optionals](#optionals)
 * [Error Handling](#error-handling)
 * [`guard`](#guard)
 * [`defer`](#defer)
* [Code Smells](#code-smells)

***

# Naming and Code Clarity

Unlike Objective-C, Swift allows us to keep the clarity of function naming while reducing verbosity.  A lot of verbose Objective-C conventions are baked into the Swift language via type inference, labeling parameters, and closures.  A few notes when naming things:

1. **Clarity != Verbosity** ([Great video on clarity in Swift](https://realm.io/news/swift-summit-swifty-methods-clarity-brevity/))
 1. There's a mental cost in _reading_ long verbose methods
 1. Avoid using type names in the method name
 1. Avoid using predicates (`of`, `by`, `with`, `in`, etc)
1. **Clarity > Brevity** and **Brevity ⊂ Clarity** 
 1. Don't use abbreviations (except for stuff like `URL` or `UUID`)
 1. Don't shorten for the sake of brevity.  Clarity is the goal.

## Swift 3.0 API Naming Guidelines

Apple has produced a [thorough guide on naming with Swift 3.](https://swift.org/documentation/api-design-guidelines/) . Where possible, adhere to this guideline.

### Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* Add a space between the `:` and the type, but not the label.  

**OK**
```swift
let foo: Bar
let hash: [String: Int]
class MyView: UIView
```

**NOT**
```swift
let foo:Bar
let foo : Bar
let hash: [String:Int]
let hash: [String : Int]
class MyView : UIView
class MyView:UIView
```


### Conditionals

* Conditional bodies in Swift must always be accompanied by braces as defined by the language (unlike Objective-C or C where they can be optional
* Parenthesis in conditional statements should surround logical blocks if there are more than one.
e.g.

**OK**
```swift
if something {

}

if something || (other < 10) {

}
```

**NOT**
```swift
if (something) {

}
```

* multi-line conditionals should begin on the next line as the conditional logic (for spacing), and end each logical expression with a logical statement.  
  * Expressions being `&&`, `||`, or `,` (in the case of optional `let` chaining)
  * The opening scope `{` should begin on the next line.  
  * Parenthetical expressions `(a == b)` should be grouped on their own line when possible.

**OK**
```swift
if 
    mySomething ||
    (otherSomething && thisThing) {
    
} 

guard
    let x = myString.toInt(),
    x > 0 else {
    
}
```

**NOT**
```swift
if mySomething || 
  (otherSomething 
   && thisThing) {
    
}

guard 
    let x = myString.toInt(),
    x > 0 
    else {
    
}
```

### Ternary Operator

The Ternary operator, `?:` , should only be used during assignment when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as a [pattern-matching `case` statement](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Patterns.html).

**OK**
```swift
result = a > b ? x : y;
```

**NOT**
```swift
result = a > b ? x = c > d ? c : d : y;
```

### Nil Coalescing Operator

The nil coalescing operator `??` should be used in place of the Ternary Operator whenever the initial test is for existence.

**OK**
```swift
let bob = mary.husband ?? Person("bob")
```

**NOT**
```swift
let bob = mary.husband != nil ? mary.husband : Person("bob")
```

### Unwrapping Optionals

Use [Optional Binding](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID330) instead of explicitly testing for `nil`

**OK**
```swift
if let actualNumber = possibleNumber.toInt() {
}

if _ = possibleNumber.toInt() {
   // only if the result isn't being used
}
```

**NOT**
```swift
if possibleNumber.toInt() != nil {
  let actualNumber = possibleNumber.toInt()!
}
```

### Labeled Statements

Use [Labeled Statements](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID135) when using any of the control transfer keywords like `break`, `continue`, or `fallthrough`.  Statement Labels should be in `ALL_CAPS`

Because loops and switches can be nested, it is preferred that the specific control structure being controlled be labeled as such.

**OK**
```swift
GAME_LOOP: while square != finalSquare {
    if ++diceRoll == 7 { diceRoll = 1 }
    ROLL_SWITCH: switch square + diceRoll {
    case finalSquare:
        break GAME_LOOP
    case let newSquare where newSquare > finalSquare:
        continue GAME_LOOP
    default:
        break ROLL_SWITCH
    }
}
```

**NOT**
```swift
while square != finalSquare {
    if ++diceRoll == 7 { diceRoll = 1 }
    switch square + diceRoll {
    case finalSquare:
        break; break
    case let newSquare where newSquare > finalSquare:
        continue
    default:
        break
    }
}
```

### Type Inference

Allow the Swift compiler to infer as much type information as possible.
```swift
// prefer this:
let name = "Name"

// over this:
let name: String = "Name"
```

### Shortcut Type Declarations

When declaring an array type, there are two ways of declaring the type: Array<T> and [T]. Apple’s Swift book prefers the latter shortcut. However, please allow the compiler to infer the type where possible.

**OK**
```swift
let stringArray: [String]
let stringDictionary: [String: String]
```

**NOT**
```swift
var stringArray: Array<String>
var stringDictionary: Dictionary<String, String>
```

**NOTE**: _Swift does not currently have a shortcut for `Set<T>` so the extended form has to be used_, but the compiler can infer the type of the set, so the following is preffered:

**Preferred**
```swift
let something: Set = ["a", "b", c"] // compiler knows it is a Set<String>
```

### Closures

Collapse closures as much as possible.
```swift
doSomething(this: Int, that: Int, completion: (Int, Int) -> Int )
```

**Preferred**
```swift
let result = myObject.doSomething(this: x, that: y) { $0 + $1 }
let result = myObject.doSomething(this: x, that: y, completion: self.myMethod)
```

**OK**
```swift
let result = myObject.doSomething(this: x, that: y) { (this: Int, that: Int) in 
    return this + that;
}
```

** Not Preferred**
```swift
let result = myObject.doSomething(this: x, that: y, completion: { (this: Int, that: Int) in 
    return this + that;
})
```

### Default Parameters

Where possible, avoid overloading a method call by providing default parameters.  This preserves the function's "Type" and allows it to be used as a variable to other functions.

**OK**
```swift
func doSomething(this: Int, that: Int, min: Int = 0, max: Int = Int.max) -> Int {

}
```

**NOT**
```swift
func doSomething(this: Int, that: Int) -> Int {
   return doSomething(this: this, that: that, max: Int.max)
}

func doSomething(this: Int, that: Int, max: Int) -> {
   return doSomething(this: this, that: that, min: 0, max: max)
}

func doSomething(this: Int, that: Int, min: Int, max: Int) -> Int {

}
```

By using defaults, we can actually write a method that accepts the first function as a parameter and we don't have to overload _this_ function for another one that takes a different level of input
```swift
func doSomethingLater(#todo: (Int, Int, Int, Int) -> Int) {

}
```

### Protocol Names
Protocols should be named according to the following format:

 1. **Acted On**: If a type is acted on then it should be an Adjective ending in [-able](http://dictionary.reference.com/browse/-able):
  * `Hashable`
  * `Equatable`
  * `Comparable`
 1. **Descriptive**: If a type is a kind of a thing, then it should follow the `-Type` format:
  * `SequenceType`
  * `CollectionType`
  * `SignedNumberType`
  * `MirrorType`
  * `BooleanType`
 1. **DataSource**: If a type provides data to another type, then it should follow the `-DataSource` format
  * `UITableViewDataSource`
 1. **Delegate**: If a type implements functionality for another type, then it should follow the `-Delegate` format
  * `UITextFieldDelegate`

### Type Names
Classes, Structs, and Enums should be named as succinctly as possible, allowing the Protocols that they implement to add additional descriptive information.

**OK**
```swift
struct Array<T>: MutableCollectionType, Sliceable
```

**NOT**
```swift
struct GenericOrderedMutableCollection<T>: MutableCollectionType, Sliceable 
```

> *Note:* If you find yourself adding adjectives to a type's name, consider breaking those adjectives out into their own protocols.

### Type Conformity

All types should conform to a public interface.  Swift's `stdlib` does this even with something as simple as `Bool`
```swift
struct Bool: BooleanType {
```

**Preferred**
```swift
struct User: UserType {
struct Player: UserType, Playable {
```

**Not Preferred**
```swift
struct User { 
struct Player {
```

### Variable Naming

* Variables should be camel-case with the leading word being lowercase.
* Prefixes and suffixes should only be used to conform to Apple's Styling or clarify information not readily available through the code or IDE.
* Avoid repeating type information in prefixes or suffixes

**OK**
```swift
let settings = UIButton()
let name = "Bob"
```

**Avoid**
```swift
let settingsButton: UIButton = UIButton()
let nameStr: String = "Bob"
```
* Avoid using type suffix to differentiate variables.  Instead, use tuples with named values.

**Preferred**
```swift
let transition: (view: UIView, image: UIImage)
```

**Not Preferred**
```swift
let transitionView: UIView
let transitionImage: UIImage
```

* Do not use underscores for private or local variables.  They can be marked as `private` when declared.
* Constants should be nested within their parent class, instead of being prefixed with the class name

**OK**
```swift
class MyClass {
    static let myConstantKey = "myClass.myConstantKey"
}
```

**NOT**
```swift
static let kMyClassMyConstantKey = "myClass.myConstantKey"
```

* Related Types should be nested within their parent class, instead of being prefixed with the class name

**OK**
```swift
class AutoEngine {
    enum Cylinders {
        case FourBanger, InLineSix, V6, V8, V12
    }
}
```

**NOT**
```swift
enum AutoEngineCylinders {
   case AutoEngineCylindersFourBanger
   case AutoEngineCylindersInLineSix
   // ...
}
```

* Enum cases should be in camel-case with the leading word being **Upper* case and not include the enum name or parent class name

**OK**
```swift
enum Season {
    case Winter, Spring, Summer, Fall
}
```

**NOT**
```swift
enum Season {
    case SeasonWinter
    case SeasonSpring
    case SeasonSummer
    case SeasonFall
}
```

#### Verbosity

Objective-C likes to be _overly_ verbose in naming methods.  Taking a few lines from `NSString`:

```objc
- (Array *)componentsSeparatedByString:(NSString *)separator;
```

Which has a few issues: 
* `By` is unnecessary (as are most predicates)
* `components` takes the place of the "return" value, which contradicts the `- (NSString *)`... is there a `NSComponents`?
* `separator` appears twice, as does the type information of the parameters `String`

The corresponding method in Swift for `String` is just as clear, but far more brief.

```swift
func split(separator: String) -> [String]
```

The same can be said for string replacing

```objc
- (NSString *)stringByReplacingOccurrencesOfString:(NSString *)target withString:(NSString *)replacement;
```

```swift
func replace(_: String, replacement: String) -> String
```

The Swift code, while being far less verbose, is still just as clear.

```objc
NSString *original = @"ABABABABABABABABABABA";
NSArray *justTheBs = [original componentsSeparatedByString:@"A"];
NSString *withCs = [original stringByReplacingOccurancesOfString:@"A" withString:@"C"];
```

```swift
let original = "ABABABABABABABABABABA"
let justTheBs = original.split("A")
let withCs = original.replace("A", replacement:"C")
```

### Operators

While Swift allows the overriding and creation of operators, they should be avoided in favor of named methods.  

**Why Named Methods are Better**
* Discoverable with `.` and `alt+click`
* Documentable with `///` and `/** */`
* No one knows what `<*>` does
* Order of operations is clearer ( `8 + 5 - 4` might seem innocent but what does `a <*> b <^> c` do??)

**When to use Operators**
* Your type implements `Equatable` (override `==`)
* Your type implements `Comparable` (override the `>` and `<`)
* Your type implements Collection (override or create subscript `[]`)
* Your type supports mathematical operations (override or create `+`, `-`, `*`, `/`, `^`, etc.)
* Your type supports bitwise operations (override or create `&`, `|`, etc)
* Your type supports pattern matching (override `~=` and `=~`)

**When NOT to use Operators**
* Overriding operators of System Library Classes (**don't** override `+` for `Int`)
* Adding new functions to System Library Classes (**don't** add `-+->` to `String`)
* For Brevity (**don't** use `+++` where a name like `append` would do)

**NOTE** An Operator body should *never* contain any logical information.  It should *always* make a call to a named function on a given class.

**OK**
```
extension MyClass : Equatable {
    /// compares the unique database `id` for equality
    func isEqualTo(other: MyClass) -> Bool {
        return self.id == other.id
    }
}

func ==(lhs: MyClass, rhs: MyClass) -> Bool {
    return lhs.isEqualTo(rhs)
}
```

**NOT**
```
func ==(lhs: MyClass, rhs: MyClass) -> Bool {
    return lhs.id == rhs.id
}
```

### Extensions

Extensions are a great way to encapsulate code functionality and provide documentable scope.

A class should be extended for each protocol it adheres to.  This both provides in-line documentation and avoids the need for the macro `#pragma mark - <UITableViewDelegate>` (although adding `//Mark -` is still OK)

```swift
class MyViewController : UIViewController {

}

extension MyViewContoller : UITableViewDelegate, UITableViewDataSource {

}

extension MyViewController : MyEventHandlerDelegate {

}
```

### Nesting

Nesting allows related information to be grouped together without worrying about namespace clashing.  Nesting should be used when nesting one type under another.  Do **not** nest types _within_ a function or getter/setter.

**OK**
```swift
class Monkey {
    enum FurType {
        case Fuzzy = "fuzzy"
    }
    getFur() -> String {
        return FurType.Fuzzy.toRaw()
    }
}
```
Here, the `enum FurType` won't clash with any other thing named `FurType` because the _actual_ name for this is `Monkey.FurType`


**NOT**
```swift
class Monkey {
    func getFur() -> String {
        enum FurType {
            case Fuzzy = "fuzzy"
        }
        return .Fuzzy.toRaw()
    }
}
```

### Avoid Chaining Optionals More Than One Level Deep

Swift allows you to chain optionals to avoid many nested levels of `if-let` clauses. For example, if you have a `Perso`n class, with an optional `Residence?`, which itself has an optional `let address: String?` field, you could print out a person’s address with the following code:

```
println(“\(person.residence?.address?)”)
```

However, doing so is a code smell and usually violates the [Law of Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter). A better way to handle this is to create a computed property called address on the Person class that returns the optional address:

```
class Person {
    ...
    var address: String? {
        return residence?.address?
    }
}

println(“\(person.address?)”)
```

### Comments

* When they are needed, comments should be used to explain **why** a particular piece of code does something. 
* Any comments that are used must be kept up-to-date or deleted.
* Comments _may_ be used to identify or reference a piece of code to an external source (such as a pivotal ticket)
* Do **NOT** use `TODO`s or `FIXME`s _unless_ a corresponding pivotal ticket has been created **and** scheduled.
* Block comments should be avoided except when providing documentation
* Delete all commented out code.  Xcode's `diff` and `blame` views provide us with enough history.

### Documentation

With the change in [Xcode Documentation](http://nshipster.com/swift-documentation/) Swift's documentation should be written in [reStructuredText](http://docutils.sourceforge.net/docs/user/rst/quickref.html) instead of HeaderDoc.

**What To Document**
* purpose, scope, and use of all types (includes nested types)
* convenience initializers
* _all_ methods marked with `throws`
* properties with a `var`

# Mutability

### `let` and `var`

Swift's strong static-type and powerful value-type systems reduce the need for mutable objects.

* Use `let` by default
* Avoid using `var`
* If you have to, use `var` only on class references
* Avoid using `var`
* Use `map`, `flatMap`, and `reduce` to limit the use of `var` on Collection Types
* Avoid using `var`
* Value-Types (such as `struct` and `enum`) should **NEVER** use `var` on properties
* Avoid using `var`

Did we mention to avoid using `var`?

### `mutating`

The `mutating` keyword adds a layer of confusion to the immutability of value-types in Swift, and as such, it's use is _strongly_ discouraged.  

A `mutating func` is a function that allows the developer to "mutate" a struct or enum.  I put "mutate" in quotes because what actually happens is _not_ actually mutating.  The function just returns a new struct and assigns it to the reference of the old one.

What you wrote:
```swift
extension CGPoint {
    mutating func moveRight(delta: CGFloat) {
        x += delta
    }
}

var myPoint = CGPoint(0,0)
print(myPoint) // prints {0,0}

myPoint.moveRight(10)
print(myPoint) // prints {10,0}
```

What _ACTUALLY_ happened:
```swift
extension CGPoint {
   // the compiler generates what you _should_ have written
   func moveRight(delta: CGFloat) -> CGPoint {
        return CGPoint(x + delta, y)
    }
}

var myPoint = CGPoint(0,0)
print(myPoint) // prints {0,0}

// The compiler re-assigns.
var myPoint = myPoint.moveRight(10)
print(myPoint) // prints {10,0}
```

So that means, that this bit of code is completely non-intuitive.  Because it makes sense in object-land, but definitely not in value-land.

```swift
var A = [1,2,3,4]
var B = A
B.append([5])

print(A) // prints [1,2,3,4]
print(B) // prints [1,2,3,4,5]
```

Another example on eschewing `mutating` keyword is the following: Below are 4 different way of achieving the same exact thing - insetting a rectangle.

**1 Global Function (Objective-C Style)**
```objc
CGRect CGRectInset(CGRect rect, CGFloat dx, CGFloat dy)
{
    return CGRectMake(origin.x+dx, origin.y+dy, size.width -dx, size.height -dy);
}
```

**2 Self-Assigned Mutating Function**
```swift
struct CGRect {
    mutating func inset(dx: CGFloat, dy: CGFloat) {
         self = CGRect(origin.x+dx, origin.y+dy, size.width -dx, size.height -dy);
    }
}
```

**3 Value-Assigned Mutating Function**
```swift
struct CGRect {
    mutating func inset(dx: CGFloat, dy: CGFloat) {
        origin.x += dx
        origin.y += dy
        size.width -= dx
        size.height -= dy
    }
}
```

**4 Immutable Function**
```swift
struct CGRect {
   func inset(dx: CGFloat, dy: CGFloat) -> CGRect {
        return CGRect(origin.x+dx, origin.y+dy, size.width -dx, size.height -dy)
    }
}
```

Here's how they'd play out in use:
```swift
let original = CGRect(0,0,100,100)

// 1 - Global Function
let globalResult = CGRectInset(original, 10, 10)
// PROBLEM: not discoverable!  typing "original."+TAB doesn't give you anything. 

// 2 & 3 - Mutating Function
var mutable = original
mutable.inset(10,10)
// GOOD: Discoverable!
// PROBLEM: blurs lines between values and objects.  A reader might assume the rect is some kind of object.
// PROBLEM: can't use this method in a closure!  doesn't "return" anything
// PROBLEM: assigning to `self` ??? the code is awkward to read unless you really understanding `mutating`

// 4 - Immutable Function
let immutableResult = original.inset(10,10)
// GOOD: Discoverable!
// GOOD: Not using `var`!
// GOOD: explicit functionality!  Don't need to understand "magic" behind `mutating`
// GOOD: using this method in a map/reduce/etc. closure!!
let myRects: [CGRect] = // some data
let insetRects: [CGRect] = myRects.map { $0.inset(10,10) }
```

### Singletons

Use a static class variable.  ([`dispatch_once` proof](http://krakendev.io/blog/the-right-way-to-write-a-singleton))

```swift
class MyClass {
    static let sharedInstance = MyClass()
}
```

Singletons should be used to facilitate code uniformity or access static data, but not to store application state or mutable data.  Dependency Injection is preferred for state and/or mutable data, as this increases the applications Test-ability and decreases potential thread conflicts.

Good uses for Singletons
 * Web Services
 * Managers for 3rd party services

Good uses for Dependency Injection
 * Passing data through a Segue
 * Asynchronous/threading tasks

### Property Observing

Gone are the days of KVO!  With Swift's `didSet` and `willSet` closures on properties, we can now observe them directly.  A fantastic use case for Property Observing is the following [(hat-tip to NatashaTheRobot for the code)](http://natashatherobot.com/ios-a-beautiful-way-of-styling-iboutlets-in-swift/)

**AVOID**
```swift
import UIKit

class ViewController: UIViewController {

    @IBOutlet weak var myLabel: UILabel!
    @IBOutlet weak var myOtherLabel: UILabel!
    @IBOutlet weak var myButton: UIButton!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // extract this into a private function
        // to keep viewDidLoad short
        configureStyling()
    }
}

// MARK: UI Styling
private extension ViewController {
    
    func configureStyling() {
        myLabel.textColor = UIColor.purpleColor()
        myOtherLabel.textColor = UIColor.yellowColor()
        myButton.tintColor = UIColor.magentaColor()
    }
}
```

**OK**
```swift
import UIKit
 
class ViewController: UIViewController {
 
    @IBOutlet weak var myLabel: UILabel! {
        didSet {
            myLabel.textColor = UIColor.purpleColor()
        }
    }
    
    @IBOutlet weak var myOtherLabel: UILabel! {
        didSet {
            myOtherLabel.textColor = UIColor.yellowColor()
        }
    }
    
    @IBOutlet weak var myButton: UIButton! {
        didSet {
            myButton.tintColor = UIColor.magentaColor()
        }
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
}
```

# "Failure" Handling

There are several different ways to represent "failure" in Swift - two are built-in to the language (Optionals and try/catch), and the third is a fairly standard application of a monad.

With the advent of Swift 2.0's native error handling, the `Result<S,E>` error handling is deprecated in favor of the `do/try/throw/catch` semantics.

## Types of Failures

### Optionals

The simplest way to indicate that a method has failed somehow is to just mark the return as `Optional` and return `nil` when something goes wrong.

```swift
func isBiggerThanTen(input: Int) -> Int? {
   return input > 10 ? input : nil
}
```

##### When to Use:

* When the caller does not need to know _why_ something failed
* When the method does not need to offer additional business logic to the rest of the app.

##### Example:

```swift
typealias Ticket Int

class Raffle {
    private var bucket: [Ticket]
    init(values: [Ticket]) {
        bucket = values
    }

    /// pop and return ticket, or nil
    func draw() -> Ticket? {
        guard bucket.count > 0 else {
            return nil
        }
        let temp = bucket.last
        bucket.remove(temp)
        return temp
    }
}


// Using Optional
let myRaffle = Raffle([1..5])
if let myTicket = myRaffle.draw() {
    print(myTicket)
}

```

### Swift Errors

In Swift 2.0, Apple has added a [definitive "try/catch" error handling](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html) that transforms the CocoaTouch's API of the dreaded `error:(NSError **)error` pattern into something delightful to use.  The new "try/catch" logic is pretty awesome ([and best described elsewhere](https://www.bignerdranch.com/blog/error-handling-in-swift-2/)) when combined with `guard` and `defer`.

Apple's documentation provides a thorough guide on using errors.

**Preferred**
```swift
do {
    let myThing: String = try methodWhichThrows()
} catch {

}
```

While not preferred, it is OK to discard the error and treat the call as if it was an Optional

**OK**
```swift
let myThing: Optional<String> = try? methodWhichThrows()
```

Along with force-unwrapping optionals, **DO NOT** force-unwrap errors.

**NOT OK***
```swift
let myThing = try! methodWhichThrows()
```

And I'd be remiss if I didn't add something here about [`rethrows`](http://robnapier.net/re-throws).  :smile: 

## How to Generate a Failure

### `guard`

When using ["Railroad Oriented Programming"](http://fsharpforfunandprofit.com/rop/) or "Early Exit" error handling in a function, use `guard` instead of `if`.  Natasha the Robot has some [great examples of this.](http://natashatherobot.com/swift-guard-better-than-if)

The `guard` keyword can be used with all 3 different failure types above.  Examples below:

```swift
// `guard` with optional return value
func createPerson(age: String, name: String) -> Person? {
    guard 
        let age = age, let name = name 
        where name.characters.count > 0 && age.characters.count > 0 
    else {
        return nil
    }   
    guard let ageFormatted = Int(age) else {
        return nil
    }
    return Person(name: name, age: ageFormatted)
}

enum InputError: ErrorType {
    case InputMissing
    case AgeIncorrect
}

// guard with return value, but throws an error
func createPerson(age: String, name: String) throws -> Person {
    guard 
        let age = age, let name = name 
        where name.characters.count > 0 && age.characters.count > 0 
    else {
        throw InputError.InputMissing
    }   
    guard let ageFormatted = Int(age) else {
        throw InputError.AgeIncorrect
    }
    return Person(name: name, age: ageFormatted)
}
```

### `defer`

In the most simplest terms, the `defer` keyword allows the API developer to put a `finally` closure within the method where the error is thrown, instead of outside.

The clearest example of this is in the case of file manipulation:

```swift
func writeLog() {
    let file = openFile()
    defer { closeFile(file) }

    let hardwareStatus = fetchHardwareStatus()
    guard hardwareStatus != "disaster" else { return }
    file.write(hardwareStatus)

    // Do whatever you want... `closeFile` will ALWAYS ALWAYS be called before the application exists the scope of this function.
    // you can call `return` early
    // you can `throw` an error
    // CoreData could throw an `NSException`

    let file2 = openFile()
    defer { closeFile(file2) }

    // you can create multiple defer closures

    // just don't `return` `throw` or anything like that within the `defer` closure
}
```

# Code Smells

The following is a list of bad practices and anti-patterns to be avoided when using Swift.

## `!`

Because of the delicate nature of Swift / Obj-c interoperability, any use of the `!` is discouraged.  Examples include:
* Implicit Unwrapping: `let sam = optionalSam!` (_An exception exists when the value **might** be `nil` fr a short period of time.  See `UIViewController` and `IBOutlet`_)
* Forced Unwrapping: `delegate!.doSomething()`
* Forced Down-cast: `let bob: Employee = person as! Employee`
* Forced Error Handling: `let this = try! MyMethodCanThrow(that: x)`
* Previously unwrapped values:

**OK**
```
let myString: String? = // something
if let hasString = myString, let myInt = hasString.toInt() {
   print("I entered \(hasString)")
}
```

**NOT** (this is _technically_ safe, but because we can unwrap many values
```
let myString: String? = // something
if let myInt = myString?.toInt() {
   print("I entered \(myString!)")
}
```

## `mutating func`

As mentioned earlier under the [Mutability](#mutating) section, using this keyword _hides_ functionality. What the compiler generates is _not_ what you wrote in the code, and there are better alternatives than trying to force a `struct` to behave like a `class`.

## `inout`

Similar to `mutating func`, the `inout` keyword hides the actual implementation behind an obscure keyword. This should be reserved for C or C++ compatibility _only_ - and **never** in any developer-generated code.

Here is a [link to the Apple Documentation](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Declarations.html#//apple_ref/doc/uid/TP40014097-CH34-ID545) which says the following:

> In-out parameters are passed as follows:
> 1. When the function is called, the value of the argument is copied.
> 2. In the body of the function, the copy is modified.
> 3. When the function returns, the copy’s value is assigned to the original argument.
> This behavior is known as *copy-in copy-out* or *call by value result*. 


