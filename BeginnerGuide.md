# Beginner Guide

## Why
This beginner guide is to help developers new to iOS and/or Swift not fall into some really common pitfalls. This document is phrased as a list of "rules" to be A) cheeky and B) help with remembering.

# Rule #0: Don't Rape your Code
Never force your code `!` - always ask `?` instead.

# Rule #1: Mutants are your Enemy
Avoid using `var` 
Never use the `mutating` keyword - why do you even know about this?

# Rule #2: Be Marxist
Avoid race conditions and data errors by using `struct` and `enum` instead of `class`

# Rule #3: Be Functional
Avoid side-effects in your code by using functional patterns

# Rule #4: God is Dead
Break God-Objects down into task-specific handlers
