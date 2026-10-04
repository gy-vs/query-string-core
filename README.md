# qs

Run tests: `npx tape 'test/**/*.js'`

## Parsing types

By default `qs.parse` returns every value as a string. Pass `parseTypes: true`
to restore values whose text is the text of a JavaScript number, `true`,
`false`, or `null`:

```js
var qs = require('./');

qs.parse('page=2&limit=20&active=true&deleted=null', { parseTypes: true });
// { page: 2, limit: 20, active: true, deleted: null }
```

A value is converted only when the conversion is reversible: turning the
result back into a string with `String` must produce the original text
exactly. Values that fail the round trip stay strings, so no information is
silently lost:

```js
qs.parse('a=08&b=1.0&c=0x10&d=1_000&e=&f=hello', { parseTypes: true });
// { a: '08', b: '1.0', c: '0x10', d: '1_000', e: '', f: 'hello' }
```

`parseTypes` is off by default and only affects values; keys are never
converted, and the existing numeric array-index handling is unchanged. When a
custom `decoder` is supplied, the decoder's output is judged by the same rule,
and any non-string value a decoder returns is kept exactly as-is. Values
restored this way round-trip through `qs.stringify`, which already writes
numbers and booleans as text (use `strictNullHandling` to round-trip `null`).
