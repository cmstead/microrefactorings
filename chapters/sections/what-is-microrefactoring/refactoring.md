<!--bl
(filemeta
    (title "Refactoring"))
/bl-->

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