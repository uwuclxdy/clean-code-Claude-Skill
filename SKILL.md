---
name: clean-code
description: Clean code principles and best practices covering naming conventions, function design, error handling, comments, and architecture. Use this skill whenever the user asks you to write, review, or refactor code and wants it to follow clean code standards. Also trigger when the user mentions "clean code", "code quality", "readability", "refactor for clarity", "naming conventions", "function size", "code smells", or asks for a code review with best practices in mind. Apply these principles proactively when writing or reviewing code, even if the user doesn't explicitly ask for "clean code."
---

# Clean Code Principles

Apply these principles when writing, reviewing, or refactoring code. They are organized from the most fundamental (naming) to the most architectural (error handling and polymorphism).

---

## 1. Naming & Readability

### 1.1 Avoid Disinformation

Never use variable names that create false expectations, contain misleading technical terms, or use visually confusing characters.

**Do:**
- Use names that honestly reflect the data structure, like `accountsMap` or `accountsGroup`.
- Use distinct names for classes/variables so autocomplete doesn't trick you.

**Don't:**
- Name a map/object `accountList` just because it holds multiple items — to a programmer, "list" means an array/indexed structure.
- Create names that vary in tiny ways (e.g., `ABCManagerForEfficientProcessingOfUsers` vs `...PersistingOfUsers`).
- Use characters that look identical (e.g., lowercase `l` and number `1`, or uppercase `O` and number `0`).
  - Bad: `int a = l; if (O == l) a = O1; else l = O1;`

Disinformation creates "code lies" where developers' perceptions differ from actual behavior, causing confusion and bugs.

### 1.2 Make Meaningful Distinctions

If two things have different names, they must do different things. Avoid meaningless noise words and number series.

**Do:**
- Reveal the actual role of variables (e.g., `array`, `condition`, `transformation`).
- Use specific, role-based names for classes (e.g., `OrderValidator`, `OrderRepository`, `OrderCalculator`).

**Don't:**
- Use numbered variables that look identical but do different things.
  - Bad: `function filterAndMap(data1, data2, data3)`
- Add meaningless noise suffixes when you can't think of a better name (e.g., `OrderManager`, `OrderHandler`, `OrderController`, `OrderProcessor` all existing side by side).
- Add redundant prefixes/suffixes (e.g., `getUser()`, `getUserInfo()`, `getUserData()` with no clear distinction).

Meaningless distinctions force every developer to waste time reading the underlying implementation just to understand the difference between two functions or classes.

### 1.3 Use Pronounceable Names

Variable names should sound like natural human language so they can be easily read and spoken aloud.

**Do:**
- Use real words that describe the intent.
  - Good: `let generationTimestamp = new Date();`
  - Good: `class Customer { private generationTimestamp: Date; private productSerialNumber: string; }`

**Don't:**
- Mash abbreviations together into cryptic strings.
  - Bad: `let genymdhms = new Date();` (generation year month day hour minute second)
  - Bad: `let prcssr = new Processor(); let dtaRcrd = fetchData();`

Programming is a social activity. Unpronounceable names prevent teams from discussing code verbally and create a major onboarding bottleneck.

### 1.4 Use Searchable Names

The length of a variable name should match the size of its scope. Avoid single letters or raw numbers for anything outside a tiny local scope.

**Do:**
- Use single letters (`i`, `j`) *only* for local variables in very short scopes (like a tiny `for` loop).
- Create named, searchable constants for numbers.
  - Good: `MAX_CLASSES_PER_STUDENT = 7;`
  - Good: `taskDays = taskEstimate[j] * WORK_DAYS_PER_WEEK; sum += taskDays / NUMBER_OF_TASKS;`

**Don't:**
- Use single letters in larger scopes (searching for the letter `e` will return thousands of useless results).
- Hardcode "magic numbers" into business logic.
  - Bad: `for (let e = 0; e < 7; e++) { total += (data[e] * 4) / 5; }`

Cryptic names and magic numbers are impossible to locate using search tools, making the codebase unnavigable and harder to maintain.

### 1.5 Avoid Encodings (Hungarian Notation)

Do not encode type or scope information into variable names. Let your IDE and compiler handle types.

**Do:**
- Use names that describe *intent*, stripping away type prefixes.
  - Good: `let firstName: string;`, `let age: number;`, `let isLoggedIn: boolean;`

**Don't:**
- Use "Hungarian Notation" to prefix variables with their type.
  - Bad: `let strFirstName: string;`, `let iAge: number;`, `let bIsLoggedIn: boolean;`, `let fAccountBalance: number;`

Modern IDEs show types on hover, and compilers catch type errors instantly. Encodings are redundant visual noise.

### 1.6 Avoid Mental Mapping

Don't force readers to mentally translate cryptic abbreviations into their actual meanings.

**Do:**
- Use obvious, clear names.
  - Good: `const apiUrl = 'https...'; const user = fetchUser(apiUrl); const permissions = user.permissions; const isAdmin = permissions.includes('admin');`
  - Good: `function calculateTotal(price, tax, quantity) { return (price + tax) * quantity; }`

**Don't:**
- Use single-letter abbreviations that require the reader to memorize a mental lookup table.
  - Bad: `const hp = Math.sqrt(a*a + b*b);`
  - Bad: `const r = '...'; const u = fetchUser(r); const p = u.permissions; const a = p.includes('admin');`
  - Bad: `for (let i=0; i<t.length; i++) { const tx = t[i]; validate(tx); }` — is `tx` time, tax, text, or transaction?

Cryptic names add entries to a developer's "mental stack." The reader's brain gets bogged down deciphering abbreviations instead of focusing on the actual logic.

---

## 2. Function Structure & Size

### 2.1 Keep Functions Small

Functions should be small, and blocks inside control structures should be exactly one line long.

**Do:**
- Extract complex math, loops, and logic into clearly named helper functions.
- Make blocks inside `if`, `else`, and `while` statements exactly one line long (a function call).
  - Good: `if (file.size > MAX) { return uploadFileInParts(file, id); } else { return uploadFileAsSinglePart(file, path); }`

**Don't:**
- Write long functions that mix high-level conditions with low-level mathematical implementation details and nested loops.

Long functions bury your logic under layers of noise. Small, clearly named functions tell a story of *what* is happening without forcing the reader to decipher *how*.

### 2.2 Do One Thing

A function should do exactly one thing. The litmus test: you cannot extract another meaningful function from it.

**Do:**
- Test your functions: Can you label sections of the code with comments like `// Validation`, `// Storage`, `// Notification`? If so, extract them into separate functions.
- Test your extractions: If you extract a function and the new name is just a restatement of the code (e.g., `uploadBasedOnSize`), it's doing one thing. If the extracted name represents a separate concept (e.g., `uploadFileInParts`), the original was doing too much.

**Don't:**
- Write "micromanager" functions that handle validation, calculation, database storage, and email notifications all at once.

A function doing multiple things has multiple reasons to change, multiple ways to break, and requires massive test suites.

### 2.3 Maintain One Level of Abstraction

A function should only contain code at a single, consistent level of abstraction, delegating deeper details to the next level down.

**Do:**
- Structure code like a top-down narrative. The "CEO" function delegates to "Manager" functions, which delegate to "Specialist" functions.
  - CEO level: `validateStock(); calculateTotalBill(); executeStripeCharge(); notifyWarehouse();`
  - Manager level (`calculateTotalBill`): `sumItems(); applyDiscount(); calculateTax();`

**Don't:**
- Mix pure intent (high-level logic) with raw syntax (low-level logic) in the same function.
  - Bad: `validateStockAvailability(); const finalBill = subtotal - discount + tax; Stripe.charges.create({ amount: finalBill * 100, source: order.token }); fetch("https://warehouse...");`

Mixing levels creates messy "micromanager" functions. Keeping functions at one level of abstraction allows readers to understand the overarching narrative without getting bogged down in API syntax.

---

## 3. Arguments & Data Flow

### 3.1 Minimize Function Arguments

The ideal number of arguments is zero. If you hit three or more, wrap them in an object. Never use boolean flags or output arguments.

**Do:**
- Strive for zero arguments (`fetchData()`), one argument (`fileExists(path)`), or two if they are a natural pair (`moveTo(x, y)`).
- Group related arguments into objects.
  - Good: `saveUser(user)` (where `user` is an object containing name, email, age, etc.)

**Don't:**
- Pass 3, 4, or 5 arguments individually.
  - Bad: `saveUser(name, email, age, city, isPremium)`
- Use output arguments that modify their inputs instead of returning a value (`findMax(numbers, result)`).
- Use boolean flags (`createUser(true)`) that force different code paths — this explicitly violates the "Do One Thing" rule.
- Pass two unrelated arguments as peers (`sendEmail(message, smtp)`). Instead, make one the owner: `smtp.sendEmail(message)`.

Arguments carry mental weight, demand order memorization, and cause test cases to explode exponentially.

### 3.2 Command Query Separation (& No Hidden Side Effects)

A function should either perform an action (Command) or answer a question (Query), but never both. It should never harbor hidden side effects.

**Do:**
- Split functions into distinct queries and commands.
  - Good: `if (!attributeExists("role")) { setAttribute("role", "admin"); }`
- Ensure functions only do what their names say. Let side effects happen elsewhere.
  - Good: `if (checkPassword(password)) { initializeSession(); }`

**Don't:**
- Write functions that change state *and* return booleans.
  - Bad: `if (setAndCheckIfExists("role", "admin")) { ... }`
- Hide side effects inside seemingly harmless functions.
  - Bad: A `checkPassword()` function that invisibly calls `Session.initialize()` or resets user properties.

Mixing commands/queries creates ambiguity (did it return true because it *was* set, or because it *already* existed?). Hidden side effects create dangerous bugs, like emptying a user's shopping cart just because they verified their password mid-session.

---

## 4. Architecture & Error Handling

### 4.1 Handle Switch Statements with Polymorphism

Switch statements inherently do multiple things. Bury them in abstract factories and use polymorphism instead.

**Do:**
- Create an interface (e.g., `Employee`).
- Bury the switch statement in a Factory (`EmployeeFactory.create(type)`).
- Use polymorphism to let individual classes handle their own logic.
  - Good: `const employee = factory.create('fullTime', data); employee.calculatePay(); employee.getBenefits();`

**Don't:**
- Copy and paste the same `switch(employee.type)` block across multiple functions (`calculatePay`, `getBenefits`, `getSchedule`).

Scattered switch statements mean that adding a new type requires hunting through the entire codebase to update every single switch. Polymorphism eliminates this.

### 4.2 Prefer Exceptions Over Error Codes (& DRY)

Throw exceptions instead of returning error codes. Isolate try/catch blocks into their own functions. Never duplicate logic.

**Do:**
- Use `try/catch` blocks for a clean list of steps.
  - Good: `try { createAccount(); createProfile(); } catch (error) { showError(); }`
- Extract try/catch logic away from normal processing logic (one function for the try/catch, which calls a separate function containing the actual logic).
- Extract repeated code (like API `fetch` boilerplates) into single, authoritative functions.

**Don't:**
- Return error codes that force the caller to write deeply nested `if/else` checks.
- Share `ErrorCode` enums globally, tying your entire codebase together.
- Mix error handling with business logic.
- Repeat yourself — don't copy/paste `timeout: 5000` and `if (!res.ok) throw error` across twenty API calls.

Error codes bury the "happy path" under nested error checking. Shared enums require massive recompilations when changed. Violating DRY (Don't Repeat Yourself) guarantees silent bugs when you update logic in one place but forget the others.

---

## 5. Comments

### 5.1 Comments Lie — Use Them Sparingly and Intentionally

Avoid comments that explain *what* code does, as they eventually become outdated lies. Use comments only to explain *why* or to provide necessary warnings.

**Do:**
- Write code so clear it doesn't need comments.
- Explain business intent: `// prevents abuse while keeping free tier viable`
- Write warnings: `// WARNING: Takes too long to run. Skip during quick test cycles.`
- Clarify complex math/regex: `// 3-20 alphanumeric chars`
- Mark known tech debt: `// TODO: Remove workaround when...`
- Use standard doc comments (JSDoc, rustdoc, etc.) for public APIs.

**Don't:**
- Write comments explaining basic code behavior.
- Leave old comments behind when refactoring logic.
  - Bad: Leaving `// User must be 18 or older` above code that was updated to `if (user.age >= 21)`.

Code changes, but comments often don't. When comments get left behind, they actively deceive developers, leading them to introduce bugs.

### 5.2 Avoid Redundant and Noise Comments

Don't write comments that restate what the code already clearly communicates; only use comments to explain things the code cannot say itself.

**Do:**
- Write clear, self-explanatory code (using good variable and function names) that can be read without extra explanation.
- Respect the reader's time by only interrupting them with a comment when necessary.
- Write comments that provide important context or constraints that aren't visible in the code itself.
  - Good: `// DO NOT UPDATE - AWS Lambda limit` above `const MAX_SIZE = 5242880;`

**Don't:**
- Write redundant comments that literally translate the code into English.
  - Bad: `// if there are no items in the cart, return early` above `if (cart.items.length === 0) { return; }`
- Add "noise" comments that just act as obvious labels for variables, constructors, or simple getters.
  - Bad: `// The name` above `let name;`, or `// Default constructor` above `constructor() {}`
- Force documentation blocks (like JSDoc) on every single function just to satisfy a team rule, especially when they add no new information.
  - Bad: A full JSDoc block on `setUsername(username)` that only says "Sets the username" — adding zero information beyond what the function signature already provides.

Redundant comments cause three major problems. **Clutter:** they double the text a developer must scan without adding understanding. **Maintenance:** every code change requires updating the useless comment alongside it — forget once and the comment becomes a lie (e.g., `// Check if user is over 13` above code updated to `if (user.age >= 18)`). **Blindness:** when most comments say nothing new, developers learn to skip all of them, so the one comment that actually matters goes unread.

---

## Quick Reference Cheat Sheet

| Category | Do | Don't |
|---|---|---|
| **Naming** | Names that reveal intent, are pronounceable, and match scope length | Cryptic abbreviations, identical-looking chars, noise suffixes (`Manager`/`Data`), Hungarian encodings (`strName`) |
| **Functions** | Small functions, one thing each, single level of abstraction | Massive functions mixing high-level decisions with low-level API calls |
| **Arguments** | Zero or one arg; group 3+ into an object | Long arg lists, boolean flags, output arguments, hidden side effects |
| **Control flow** | Commands (do things) or Queries (answer things), never both | Functions that mutate state and return values simultaneously |
| **Errors** | Throw exceptions; catch in dedicated handler functions | Return error codes forcing nested `if/else`; global error enums |
| **Duplication** | Extract shared logic into a single authoritative place (DRY) | Copy-pasting switch statements or API boilerplate across files |
| **Types** | Polymorphism via interfaces/factories for type-dependent behavior | Repeated `switch(type)` blocks scattered across functions |
| **Comments** | Explain *why*, warnings, TODOs, public API docs | Explain *what* code does — those comments rot into lies |
| **Noise comments** | Let names explain themselves; add doc comments only where they provide real value | Redundant English translations of code, obvious labels, forced valueless JSDoc on every function |
