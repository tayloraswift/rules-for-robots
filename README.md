# Swift Style Guidance, for GitHub Copilot

This document outlines the key coding conventions that you (GitHub Copilot) should follow when generating Swift code. Only deviate from them if explicitly instructed to.

## Visual formatting

### Line length

Adhere to a maximum line length of 96 characters (grapheme clusters). Use multiline string literals to avoid overlong strings.

### Spacing around operators

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

### Comments

AI agents like yourself often use code comments to explain steps you took. These comments don’t make sense out of the context of a particular chat. Don’t use code comments to explain edits you make, use the chat for that instead. Code you generate should be understandable outside of the context of a particular Copilot conversation.


✓ CORRECT:

```md
Here, I have changed the formula I used to compute `x`!
```
```swift
let x: Double = y + z
```


✗ INCORRECT:

```swift
// Here, I have changed the formula I used to compute x!
let x: Double = y + z
```

## Literals

Use integer literals for integral floating point values.

```swift
// ✓ CORRECT
let x: Double = 1

// ✗ INCORRECT
let x: Double = 1.0
```

## Variables, static typing, and pattern matching

### Type annotations and initializers

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

### Loop/expression bindings

Always include explicit type annotations for variables bound in `for`, `while`, and `if` statements.

```swift
// ✓ CORRECT
for x: Int in 0 ... 10 {
}

// ✗ INCORRECT
for x in 0 ... 10 {
}
```

### Pattern binding in switch cases

When pattern matching with associated values in enums, use individual `let` keywords for each variable binding rather than placing a single `let` before the pattern.

```swift
// ✓ CORRECT - Individual let for each variable binding
switch result {
case .success(let value, let metadata):
    // use value and metadata
    
case .failure(let error):
    // use error
}

// ✗ INCORRECT - Single let before the pattern
switch point {
case let .coordinates(x, y, z):
    // use x, y, z
}
```

## Naming

### Prefer full words, or single letters

Prefer spelling out complete words, unless the abbreviation is a common standalone term of art, like “min” or “max”. Avoid abbreviating variable or function names.

```swift
// ✓ CORRECT
let probability: Double = 0.5
let frequency: Double = count / total
// 'max' preferred over 'maximum', because it is a common term of art, despite being an abbreviation
let max: Double? = values.max() 

// ✗ INCORRECT
let prob: Double = 0.5
let freq: Double = count / total
let maximum: Double? = values.max()
```

Default to single letters like `i` for indices, unless a longer name would provide meaningful clarity.

```swift
// ✓ CORRECT
let j: String.Index = string.index(after: i)

// ✗ INCORRECT
let nextIdx: String.Index = string.index(after: currentIdx)
```

Default to single letters like `x`, if reproducing well known mathematical formulae.

```swift
// ✓ CORRECT
let c: Double = .sqrt(a * a + b * b)

// ✗ INCORRECT
let hypotenuse: Double = .sqrt(sideOne * sideOne + sideTwo * sideTwo)
```

### Avoid meaningless suffixes

Omit suffixes like `-Value` that add no clarity, or are redundant in the context of static type information.

```swift
// ✓ CORRECT
let foo: String = "\(percentage)"

// ✗ INCORRECT
let fooValue: String = "\(percentage)"
```

### Binding `self`

Shadow the `self` keyword if something is semantically an instance method, but cannot be expressed as such due to the constraints of the language. Common examples include operator declarations.

```swift
// ✓ CORRECT
static func += (self: inout Self, next: Self) {
}

// ✗ INCORRECT
static func += (lhs: inout Self, rhs: Self) {
}
```

When switching on `enum` variants with payloads, shadow `self` if you are conceptually switching on cases of `self`.

```swift
// ✓ CORRECT
switch self {
case .bool(let self): ...
case .int(let self): ...
case .float(let self): ...
}

// ✗ INCORRECT
switch self {
case .bool(let bool): ...
case .int(let int): ...
case .float(let float): ...
}
```

Only do this for variant-like enums.

```swift
// ✓ CORRECT
switch self {
case .foo(id: let id, let value): ...
}

// ✗ INCORRECT
switch self {
case .foo(id: let self, let value): ...
}
```

### Avoid `lhs` and `rhs`

The names `lhs` and `rhs` are terrible parameter names, and should never be used. Use `a` and `b` in binary operator signatures instead.

### Closure parameters

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

### Grouping variables with tuples

Group related variables using tuples with named elements instead of separate variables.

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

### Greek Letters as terms of art

Use proper mathematical symbols for well-known concepts.

```swift
// ✓ CORRECT
let μ: Double = 0.5       // mean
let σ: Double = 0.7       // standard deviation
let σ²: Double = 0.49     // variance

// ✗ INCORRECT
let mean: Double = 0.5
let stdDev: Double = 0.7
let variance: Double = 0.49
```


## Coding patterns

### Using `reduce(into:_:)`

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

### Don’t parrot tuple lables

Swift’s type system is capable of propogating tuple labels through unlabeled tuples. Don’t repeat tuple labels if they’re specified from the outside, such as through a variable’s type annotation.

```swift
// ✓ CORRECT
let stats: (sum: Double, sumSquares: Double) = samples.reduce(into: (0, 0)) {
    $0.sum += $1
    $0.sumSquares += $1 * $1
}

// ✗ INCORRECT
let stats: (sum: Double, sumSquares: Double) = samples.reduce(into: (sum: 0.0, sumSquares: 0.0)) {
    $0.sum += $1
    $0.sumSquares += $1 * $1
}
```


## Object-oriented code

### Explicit `self`

When writing instance members, always refer to other instance members with explicit `self`. Static members should be referred to with explicit `Self` (for enum, struct, or protocol members), or the name of the type (for class members).

```swift
// ✓ CORRECT
let foo: Int = self.bar

// ✗ INCORRECT
let foo: Int = bar
```

In a `static` context, refer to `Self` as `Self`, even though the compiler would also allow it to be spelled as a lowercase `self`.

### Start with `static`

When writing new functions (including subscripts and properties), start with `static` and only upgrade them to instance members if the implementation actually needs to access instance members.

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

## External dependencies 

### Foundation

Never import `Foundation` unless explicitly instructed to.

This means you *may* need to provide local implementations for common `String` formatting tasks.


### Numerics (`RealModule`)

Remember that common mathematical functions in Swift (except for `min`, `max`, and `abs`) are static members on floating point types. A bare `sqrt(x)` call will not compile!

```
// ✓ CORRECT
let x: Double = .sqrt(a)
let y: Double = b + .sin(c)

// ✗ INCORRECT
let x: Double = sqrt(a)
let y: Double = b + sin(c)
```

### Testing

Use Swift Testing (`import Testing`), not XCTest, which is deprecated.

Always organize Swift Testing tests into suites (`@Suite`).


## Common mistakes

In the past, you have generated Swift code that does not compile. Below are some transcripts that describe mistakes to avoid!


### 6/24/2025

    Copilot said: Yes, I generated a few compilation errors.

    The main issues were:

    Incorrect string initialization syntax: I wrote .init(repeating: "-", count: columnWidths.value) but Swift requires the type name when using .init for static methods like String's repeating initializer. The correct form is String.init(repeating: "-", count: columnWidths.value).

    Invalid string interpolation: In the printTableRow function, I tried to use .init(value) inside string interpolation with "\(.init(value))" which is incorrect. String interpolation just needs the value directly: "\(value)".

    Type conversion confusion: In a few places I was overzealous with .init() usage for numeric casts, where simply using the value directly would have been more appropriate.

    The Style Guide states that we should use leading dot notation for initializers, but I incorrectly applied this in cases where it doesn't make sense, particularly with string interpolation and certain static initializers.

    These errors demonstrate how important it is to:

    Understand when and where to use the .init pattern correctly
    Test code before submitting it (which would have caught these issues)
    Recognize that Style Guide rules need to be applied with understanding of the language's constraints

    Thank you for fixing these errors. It's a good reminder that style guidelines need to be applied with careful attention to what's actually valid Swift code.

