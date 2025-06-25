# Swift Style Guidance, for GitHub Copilot

This document outlines the key coding conventions that you (GitHub Copilot) should follow when generating Swift code. Only deviate from them if explicitly instructed to.

## Line length

Adhere to a maximum line length of 96 characters (grapheme clusters). Use multiline string literals to avoid overlong strings.

## Type annotations and initializers

Always include explicit type annotations for all local variable bindings. Spell out all initializer calls with explicit '`init`' tokens. Use leading dot notation for initializers.

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

### Loop/expression bindings

This includes variables bound in `for`, `while`, and `if` statements.

```swift
// ✓ CORRECT
for x: Int in 0 ... 10 {
}

// ✗ INCORRECT
for x in 0 ... 10 {
}
```

### Interior numeric casts

Use leading dot notation for initializers when possible, except for interior numeric casts.

```swift
// ✓ CORRECT
let x: Double = .init(intValue)
let y: Double = x + Double.init(z)

// ✗ INCORRECT
let x: Double = Double.init(intValue)
let y: Double = x + .init(z)
```

## Prefer full words

Prefer spelling out complete words, unless the abbreviation is a common standalone term of art, like “min” or “max”. Avoid abbreviating variable or function names.


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

## Spacing around operators

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

## Closure parameter names

Prefer shorthand argument names (`$0`, `$1`) for simple closures. Only name parameters in very large closures, or when nested closures require disambiguating outer parameters.

```swift
// ✓ CORRECT
let sum: Double = values.reduce(0, +)
let squares: Double = values.reduce(0) { $0 + $1 * $1 }

// ✗ INCORRECT
let sum: Double = values.reduce(0) { accumulator, value in accumulator + value }
let squares: Double = values.reduce(0) { (accumulator, value) in accumulator + value * value }
```

If you decide to name a closure parameter, always include a type annotation. It’s okay to omit the return type, if type inference allows for it.

## Using `reduce(into:_:)`

Prefer `reduce(into:_:)` for accumulation operations that would otherwise require multiple passes.

```swift
// ✓ CORRECT
let maximumFrequency: Double = (k.min ... k.max).reduce(into: 0.0) {
    let count: Int = histogram[$1, default: 0]
    let frequency: Double = .init(count) / .init(samples)
    $0 = max($0, frequency)
}

// ✗ INCORRECT
let maximumFrequency: Double = (k.min ... k.max).map {
    let count: Int = histogram[$0, default: 0]
    return Double(count) / Double(samples)
}.max() ?? 0.0
```

For accumulations that don’t mutate collections, you can also use an ordinary `reduce(_:_:)`.

## Grouping variables with tuples

Group related variables using tuples with named elements instead of separate variables.
Swift

```swift
// ✓ CORRECT
let range: (min: Int64, max: Int64) = (
    min: .init(expected.μ - 3 * Double.sqrt(expected.σ²)),
    max: .init(expected.μ + 3 * Double.sqrt(expected.σ²))
)
print("Range: \(range.min) to \(range.max)")

// ✗ INCORRECT
let minRange: Int64 = .init(expected.μ - 3 * Double.sqrt(expected.σ²))
let maxRange: Int64 = .init(expected.μ + 3 * Double.sqrt(expected.σ²))
print("Range: \(minRange) to \(maxRange)")
```

## Greek Letters as terms of art

Use proper mathematical symbols for well-known concepts.

```
// ✓ CORRECT
let μ: Double = 0.5       // mean
let σ: Double = 0.7       // standard deviation
let σ²: Double = 0.49     // variance

// ✗ INCORRECT
let mean: Double = 0.5
let stdDev: Double = 0.7
let variance: Double = 0.49
```

## Foundation

Never import `Foundation` unless explicitly instructed to.

This means you *may* need to provide local implementations for common `String` formatting tasks.


## Numerics (`RealModule`)

Remember that common mathematical functions in Swift (except for `min`, `max`, and `abs`) are static members on floating point types. A bare `sqrt(x)` call will not compile!

```
// ✓ CORRECT
let x: Double = .sqrt(a)
let y: Double = b + .sin(c)

// ✗ INCORRECT
let x: Double = sqrt(a)
let y: Double = b + sin(c)
```

## Testing

Use Swift Testing (`import Testing`), not XCTest, which is deprecated.

Always organize Swift Testing tests into suites (`@Suite`).

## Code organization

Place related functionality in extensions:

```swift
// ✓ CORRECT
extension NormalTests {
    static func statistics(from samples: [Double]) -> (μ: Double, σ²: Double) {
        // Implementation
    }
}

extension NormalTests {
    @Test
    mutating func Statistics(_ μ: Double, _ σ: Double) {
        // Test implementation
    }
}
```
