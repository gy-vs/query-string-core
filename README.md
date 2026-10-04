# qs

Run tests: `npx tape 'test/**/*.js'`

## Parsing values into types

By default `qs.parse` returns every value as a string:

```js
var qs = require('./lib');

qs.parse('page=2&active=true&deleted=null');
// { page: '2', active: 'true', deleted: 'null' }
```

Set the `parseTypes` option to `true` to restore values that are the text of a
number, `true`, `false`, or `null` to their JavaScript values:

```js
qs.parse('page=2&limit=20&active=true&deleted=null', { parseTypes: true });
// { page: 2, limit: 20, active: true, deleted: null }
```

A value is converted only when the conversion is reversible: turning the result
back into a string with `String` must reproduce the original text exactly.
Anything that does not round-trip stays a string, so no information is lost.
This keeps leading-zero values, hexadecimal text, numeric-looking identifiers,
and large integers that would lose precision untouched:

```js
qs.parse('page=08&id=9007199254740993&hex=0xff&x=1e3', { parseTypes: true });
// { page: '08', id: '9007199254740993', hex: '0xff', x: '1e3' }
```

`parseTypes` only affects values; keys always remain strings, and the existing
array-index handling is unchanged. When a custom `decoder` is provided, its
return value is what gets judged: non-string values it returns are left as-is.
The option defaults to `false`, and can be combined with every other parse
option. `stringify` needs no changes, so a value round-trips:

```js
qs.parse(
    qs.stringify({ page: 2, active: true, deleted: null }, { strictNullHandling: true }),
    { parseTypes: true, strictNullHandling: true }
);
// { page: 2, active: true, deleted: null }
```
