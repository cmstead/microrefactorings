
# Microrefactorings #
#### The Language of Changing Code ####

## Table Of Contents ##

- [Section 1: Introduction](#user-content-introduction)
- [Chapter 1: What is Microrefactoring?](#user-content-what-is-microrefactoring?)

### Introduction ###

Several years back, I started working on writing a book. What the book was about -- a technical topic -- or how I planned to go about it -- without a plan -- really doesn't matter.  The important thing is, I never finished it. The primary reason I didn't finish the book, really, is because I realized I knew a lot of mechanics, but I really didn't know very much about communicating those mechanics to someone else.

Fast forward to today, I am starting again. This time, though mechanics are important, I am not particularly invested in how any one person decides they are going to solve a particular problem.  The possibilities are too numerous and it really doesn't matter on first approach anyway.  In all likelihood, something a developer writes today, they will hate a month from now.  This is the nature of programming -- it's not the programming that is hard, it's the learning.


    

## What is Microrefactoring? ##

Although the goal of our journey is to explore language we can use to talk about changing code, there are technical aspects we will need to explore. Microrefactorings are something we will discover as we work through the book, but there needs to be a grounding for the word itself.


### Refactoring ###

Refactoring, as defined by Martin Fowler, is "the process of restructuring existing computer code—changing the factoring—without changing its external behavior." In other words, we will change the way the code looks and is structured without actually changing the way it behaves.

One of the most common actions people take while refactoring is renaming.  The process of renaming can be rather complex depending on the language of choice.  Multiple scopes must be considered, and we must change all, but not more than, the appropriate references to the original name.  Let's take a look at what a renaming looks like:


#### Original Code ####

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function compact(values) {
    return values.filter(value => !isNullUndefinedOrNan(value));
}
```

#### Refactored Code ####

```javascript
function isCompactibleValue(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function compact(values) {
    return values.filter(value => !isCompactibleValue(value));
}
```

This renaming was rather straightforward, since the original function was renamed from `isNullUndefinedOrNan` to `isCompactible`.  However, if the original name were duplicated somewhere in a deeper scope (shadowed), it would be unsafe to change all of the names everywhere.  We will explore more of this later.

With an example of refactoring under our belt, we can explore what microrefactoring is.

    

### Microrefactoring ###

Microrefactoring is any refactoring work which can be completed safely in a single save cycle. Many of the programmers I work with have their editor set to autosave after a certain length of time without activity, usually a few seconds.  This means microrefactoring happens in a single active pass on the keyboard, without reaching for automated tools or anything else which could make large sweeps across the code.

Every large refactoring is simply a set of small refactorings completed safely in a predictable manner. This means the path through the code should always be done in steps which are small, safe and predictable.

If we consider the rename refactoring we looked at, we can either perform the work in a "big bang" style, breaking code and changing things throughout, then remembering all of the work which needs to be done in order to complete the refactoring, or it can be done as a series of very small steps.

Our initial code looked like this:

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function compact(values) {
    return values.filter(value => !isNullUndefinedOrNan(value));
}
```

This implementation is satisfactory, but it could be better.  The function name `isNullUndefinedOrNan` is really focused on the unterlying implementation and has the smell of primitive obsession.  It seems more sensible to focus on whether the value is "compactible," i.e. can the value be cast out.  If we encoded that in our source as a microrefactoring, it could look something like the following:

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function isCompactible() {}

function compact(values) {
    return values.filter(value => !isNullUndefinedOrNan(value));
}
```

We can see this is not going to impact the way our code works at all.  In fact all we did was introduce a function which takes no arguments and does nothing.  We can refer to this introduction of a function as an *identity modification*, which we will explore later.

This identity modification is the setup for our next step.  Let's introduce a new parameter into our isCompactible function.

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function isCompactible(value) {}

function compact(values) {
    return values.filter(value => !isNullUndefinedOrNan(value));
}
```

By adding a parameter, we have prepared our new function to work within the contract of the old function.  What we are aiming to do here is perform a *noop decoration* of our old function. Much like the identity modification we will explore this language later.  Just remember you saw the words for now.

Now that we have prepared our noop decorator, let's give it some guts.

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function isCompactible(value) {
    return isNullUndefinedOrNan(value);
}

function compact(values) {
    return values.filter(value => !isNullUndefinedOrNan(value));
}
```

With this one change, we have finished our decoration and `isCompactible` does exactly the same work as `isNullUndefinedOrNan`.  This is easy to see since one simply calls the other.  It's safe, now, to perform a *simple replacement* of `isNullUndefinedOrNan` where it is used.  Ideally, for this kind of work, the replacement would be done one identifier at a time so, in case something breaks, we know which change introduced the bug.

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function isCompactible(value) {
    return isNullUndefinedOrNan(value);
}

function compact(values) {
    return values.filter(value => !isCompactible(value));
}
```

The simple replacement got us to a point where we can verify our rename is going to work. Now that we are certain we haven't broken anything in our code, we are ready to start duplicating our original function.  Let's isolate our function from its call.

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function isCompactible(value) {
    return (isNullUndefinedOrNan)(value);
}

function compact(values) {
    return values.filter(value => !isCompactible(value));
}
```

By isolating the function and the call itself, we can perform a copy, wholesale, of the function being called.  Let's convert this into an immediately invoked function expression (IIFE) with a simple replacement.

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function isCompactible(value) {
    return (function isNullUndefinedOrNaN(value) {
                return value === null
                    || typeof value === 'undefined'
                    || isNaN(value);
            })(value);
}

function compact(values) {
    return values.filter(value => !isCompactible(value));
}
```

Since this IIFE rebinds the `value` argument, we can *remove rebinding* and let the internals consume `value` from the outer scope directly.

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function isCompactible(value) {
    return (function isNullUndefinedOrNaN() {
                return value === null
                    || typeof value === 'undefined'
                    || isNaN(value);
            })();
}

function compact(values) {
    return values.filter(value => !isCompactible(value));
}
```

This modification leaves us in a position where we can *remove outer call* within our function and leave just the body of the original.

```javascript
function isNullUndefinedOrNaN(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function isCompactible(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function compact(values) {
    return values.filter(value => !isCompactible(value));
}
```

Finally, our original function is no longer used at all, so we can simply discard it, leaving our renamed code instead.

```javascript
function isCompactible(value) {
    return value === null
        || typeof value === 'undefined'
        || isNaN(value);
}

function compact(values) {
    return values.filter(value => !isCompactible(value));
}
```

Although this seems like the long way around for performing something like a rename, but the exercise shows us there are lots of little, useful refactorings we can perform on our code to walk from one place to another safely, and without breaking behavior. At no point was this code unsafe to release into the wild.  Users would never have realized the changes which were ongoing.

Although it is very nice to know we can make small, safe steps at any point to our code, the more important aspect of all of these modifications is, it introduced a new language for how to discuss changes we want to make in our code. Each step in this refactoring introduced a small change which we can name and discuss and it introduced a new canonical form for a known refactoring to another state.
    

    

    