# Clean Code Summary

*Based on "Clean Code" by Robert C. Martin.*



## Table of Contents

1. [Chapter 2: Meaningful Names](#chapter-2-meaningful-names)
2. [Chapter 3: Functions](#chapter-3-functions)
3. [Chapter 4: Comments](#chapter-4-comments)
4. [Chapter 6: Objects and Data Structures](#chapter-6-objects-and-data-structures)



# Chapter 2: Meaningful Names

Good names make code easier to read and maintain. If you need a comment to explain a name, it's not good enough.

## Key Principles

### 1. Names Should Reveal Intent

**Bad:**
```javascript
let n; // array dimension
```

**Good:**
```javascript
let arrayDimension;
let isActive;
let paidAmount;
```

### 2. Avoid Confusing Names

Don't use names that look alike (`O` vs `0`, `l` vs `1`) or are too short (`ds`, `tmp`).

### 3. Make Names Pronounceable

**Bad:**
```javascript
class Rcrd2 {
    constructor() {
        this.insertymdhms = null;
    }
}
```

**Good:**
```javascript
class Person {
    constructor() {
        this.insertTimestamp = null;
    }
}
```

### 4. Make Names Searchable

Use named constants instead of magic numbers.

```javascript
const MAX_ITEMS_TO_DISPLAY = 5;

if (items.length > MAX_ITEMS_TO_DISPLAY) {
    // ...
}
```

### 5. Naming Conventions

- **Classes:** Use nouns (`User`, `Person`), not vague names like `Manager`
- **Methods:** Use verbs (`getUser()`, `calculateTotal()`)
- **Be consistent:** Pick one word per concept (don't mix `fetch`, `get`, `retrieve`)



# Chapter 3: Functions

Functions are the building blocks of your program. Keep them small and focused.

## Key Principles

### 1. Keep Functions Small

A function should be no more than 20 lines. Smaller is better.

### 2. Do One Thing

Each function should have one job.

**Bad:**
```javascript
function processUser(user) {
    if (!user.email || !user.name) throw new Error('Invalid');
    user.name = user.name.trim().toLowerCase();
    database.save(user);
    emailService.sendWelcome(user.email);
}
```

**Good:**
```javascript
function processUser(user) {
    validateUser(user);
    const formattedUser = formatUserData(user);
    saveUser(formattedUser);
    sendWelcomeEmail(formattedUser.email);
}
```

### 3. Use Descriptive Names

Code should read like prose. A good function name tells you exactly what it does.

### 4. Minimize Arguments

- 0 arguments = best
- 1-2 arguments = fine
- 3+ arguments = avoid if possible

Group related arguments into objects:

```javascript
// Instead of this
createCircle(x, y, radius, color, borderWidth);

// Do this
createCircle({ x: 10, y: 20 }, { radius: 5, color: 'red' });
```

### 5. Avoid Flag Arguments

**Bad:**
```javascript
createUser(true); // What does true mean?
```

**Good:**
```javascript
createAdminUser();
createRegularUser();
```

### 6. No Side Effects

A function should only do what its name says.

**Bad:**
```javascript
function isValueMissing(value) {
    if (this.values.includes(value)) return false;
    this.values.push(value); // Hidden side effect!
    return true;
}
```

### 7. Use Exceptions, Not Error Codes

```javascript
try {
    service.start();
} catch (error) {
    logger.log(error.message);
}
```

### 8. Extract Try/Catch Blocks

Keep error handling separate from main logic.

```javascript
function exportData() {
    copyFile(sourcePath, destPath);
}

function copyFile(source, dest) {
    try {
        fs.copyFileSync(source, dest);
    } catch (error) {
        logError(error);
    }
}
```



# Chapter 4: Comments

The best code doesn't need comments. If you feel the need to write one, first try to make the code clearer.

## When Comments Are Okay

### Legal Comments
```javascript
// Copyright (c) 2025 John Doe. MIT License.
```

### Explaining Intent (the "why")
```javascript
// Subtract 1 because array is zero-indexed
const lastItem = items[count - 1];
```

### Warnings
```javascript
// WARNING: Not thread-safe
function updateConfig() { }
```

### TODOs
```javascript
// TODO: Refactor for better performance
```

### API Documentation
```javascript
/**
 * Calculates area of a circle.
 * @param {number} radius
 * @returns {number}
 */
function calculateArea(radius) {
    return Math.PI * radius * radius;
}
```

## Bad Comments to Avoid

### Redundant Comments
```javascript
// Returns true if email is valid
function isValidEmail(email) { } // Name already says this!
```

### Noise Comments
```javascript
// constructor
constructor() { } // Obviously!
```

### Commented-Out Code
```javascript
// if (value === 100) {
//     doSomething();
// }
```
Just delete it. Git remembers everything.

### Section Markers
```javascript
// ========== EXPORTS ==========
```
If you need these, your code needs  to be organised better.

### Closing Brace Comments
```javascript
} // end if
} // end for
```
If you need these, your function is too long.



# Chapter 6: Objects and Data Structures

Objects and data structures are opposites. Know when to use each.

## The Difference

**Objects:**
- Hide data, expose behavior
- Easy to add new types
- Hard to add new functions

**Data Structures:**
- Expose data, no behavior
- Easy to add new functions
- Hard to add new types

### Object Example

```javascript
class Car {
    #speed = 0;

    accelerate() {
        this.#speed += 10;
    }

    brake() {
        this.#speed = Math.max(0, this.#speed - 10);
    }

    getSpeed() {
        return this.#speed;
    }
}
```

### Data Structure Example

```javascript
const rectangle = { width: 10, height: 20 };

function calculateArea(rect) {
    return rect.width * rect.height;
}
```

## When to Use Each

| You need to... | Use |
|----------------|-----|
| Add new types often | Objects |
| Add new functions often | Data Structures |

## The Law of Demeter

Don't dig into objects. Only talk to your immediate friends.

**Bad:**

```javascript
const dir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

**Good:**
```javascript
const dir = ctxt.getScratchDirectoryPath();
```

## Avoid Hybrids

Don't mix objects and data structures. Pick one.

**Bad:**
```javascript
class User {
    name = '';      // Public data (data structure)
    email = '';
    
    sendEmail() { } // Behavior (object)
}
```

## Data Transfer Objects (DTOs)

DTOs are pure data containers. They're fine for:
- API responses
- Database results
- Moving data between layers

```javascript
class UserDTO {
    constructor(data) {
        this.id = data.id;
        this.name = data.name;
        this.email = data.email;
    }
}
```

Don't put business logic in DTOs.



## Quick Reference

| Topic | Rule |
|-------|------|
| Names | Reveal intent, be pronounceable, be searchable |
| Functions | Small, do one thing, few arguments, no side effects |
| Comments | Avoid if possible, explain "why" not "what" |
| Objects | Hide data, expose behavior |
| Data Structures | Expose data, external functions |



## Reference

Martin, R. C. (2008). *Clean Code: A Handbook of Agile Software Craftsmanship*. Prentice Hall.
