<!--bl
(filemeta
    (title "Assign Unused Value"))
/bl-->

This builds on "introduce variable."

```javascript
function (a) {
    const b = 2; // unused
    return a * 2;
}
```

```javascript
function (a) {
    const b = a; // unused
    return a * 2;
}
```