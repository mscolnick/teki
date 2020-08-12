<br />
<img src="./src/logo.png" width="200px" />
<br />

# Teki

A **tiny** TypeScript path parser (it's **226 bytes** gzipped!). [API](#api), [fiddle]().

#### Usage

```typescript

const userRoute =
  parse(`/user/:id/messages?page=:page`)

>> userRoute('/user/123/messages?page=3)')
{
  path: { id: '123' },
  query: { page: '3' },
  hash: {}
}
```

#### Reverse parsing

`teki` can *reverse parse* parameter dictionaries into URLs

```typescript

const reverseUserRoute =
  reverse(`/user/:id/messages?page=:page`)

>> reverseUserRoute({ path: { id: 456 }, query: { page: 9 } })
'/user/456?page=9'
```

#### Query Parameters

`teki` is smart about query parameters, and will parse them
independently of order

```typescript
const queryRoute =
  parse(/myRoute?foo=:foo&bar=:bar

>> queryRoute('/myRoute?bar=hello&foo=world')
{ 
  path: {},
  query: { bar: 'hello', foo: 'world' },
  hash: {}
}
```

#### Refining paths using regular expressions

`teki` even let's you refine named parameters using regular
expressions by writing a regex after the name in angle brackets

```typescript
// Only match routes where id is numeric
const userRoute =
  parse(`/user/:id<\\d+>`)
  
>> userRoute('/user/foo')
null

>> userRoute('/user/123')
{ 
  path: { id: '123' },
  query: {},
  hash: {}
}
```

#### How does it work?

`teki` achieves its *extremely* small size and high performance by using
the native [URL](https://developer.mozilla.org/en-US/docs/Web/API/URL)
API instead of a custom parser.

Keep in mind that this means that it will not work without a polyfill
for `URL` in Internet Explorer.


# API

#### `type Dict`

```typescript
type Dict = {
  path : Record<string, string>
  query : Record<string, string>
  hash : Record<string, string>
}
```

The structure of the object returned when successfully parsing a pattern.

#### `parse`

```java
parse :: (pattern : string) => (url: string) => null | Dict
```

Parse a pattern, then accept a url to match. Returns `null` on a
failed match, or a dictionary with parameters on success.

This function is *curried* so that its faster on repeated usages.

#### `reverse`

```java
reverse :: (pattern : string) => (dict: Dict) => string
```

Use a dictionary to reverse parse it back into a URL according to the
specified pattern.

This function will throw an `Error` when the dictionary has missing
parameters.

This function is *curried* so that its faster on repeated usages.
