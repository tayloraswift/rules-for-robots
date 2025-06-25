# Swift Style Guidance, for GitHub Copilot

This document outlines the key coding conventions that you (GitHub Copilot) should follow when generating Swift code. Only deviate from them if explicitly instructed to.

## Type Annotations and Initializers

Always include explicit type annotations for all local variable bindings. Spell out all initializer calls with explicit 'init' tokens. Use leading dot notation for initializers.

```swift
// ✓ CORRECT
let random: PseudoRandom = .init(seed: 3)
let array: [Int] = .init(repeating: 0, count: 10)
let value: Double = .init(intValue)

// ✗ INCORRECT
let random = PseudoRandom(seed: 3)
let array = [Int](repeating: 0, count: 10)
let value = Double(intValue)
```

This includes variables bound in `for`, `while`, and `if` statements.

## Prefer full words

Prefer spelling out complete words, unless the abbreviation is a common standalone term of art, like “min” or “max”. Avoid abbreviate variable or function names.


```swift
// ✓ CORRECT
let probability: Double = 0.5
let frequency: Double = count / total
let max: Double? = values.max()

// ✗ INCORRECT
let prob: Double = 0.5
let freq: Double = count / total
let maximum: Double? = values.max()
```

## Spacing Around Operators

Include spaces around range operators and other binary operators.

```swift
// ✓ CORRECT
for i: Int in 0 ..< count {
    let result: Double = a * b + c / d
}

// ✗ INCORRECT
for i in 0..<count {
    let result: Double = a*b+c/d
}
```

## Closure Parameter Names

Prefer shorthand argument names (`$0`, `$1`) for simple closures. Only name parameters in very large closures, or when nested closures require disambiguating outer parameters.

```swift
// ✓ CORRECT
let sum: Double = values.reduce(0, +)
let squares: Double = values.reduce(0) { $0 + $1 * $1 }

// ✗ INCORRECT
let sum: Double = values.reduce(0) { accumulator, value in accumulator + value }
let squares: Double = values.reduce(0) { (accumulator, value) in accumulator + value * value }
```
