---
section: Migrating
---

## Migrating from 0.x

### Stores

The stores for `data`, `errors`, `warnings` and `touched` are no longer Solid stores but accessors (similar to the ones returned by `createsignal`). Due to our previous implementation with stores, effects re-ran even if the property of the store you were listening to did not change. With our new accessors for said stores it's possible to "subscribe" to a specific property and have an effect only re-run when said property changes:

```javascript
const { data } = createForm({ /* ... */ });

createEffect(() => {
  // This will only run when the value of `email` changes.
  console.log(data(($data) => $data.email));
  // Alternative (not type safe way) to write this:
  console.log(data('email'));
});
```

The following changes will need to be done in your code:

* For accessing the value of `data`, `warnings`, `errors`, and `touched`, instead of using the `store` directly, you'll need to call it. For example, for obtaining the value of `data` you'll need to write `data()`.
* For accessing a specific property of a store you'll need to pass an argument to the `store` when calling it, either a selector or a string path. For example, `data.email` should now be `data((d) => d.email)` _or_ `data('email')`.

> *NOTE*: You _could_ do `data().email` but this would re-run effects when _any_ value of `data` changes, not only when the value of `email` changes.

The rest of the stores are still signals just like before, but now they can optionally accept a function as an argument to obtain a derived value.

### Observables

Although undocumented, we used to return a property `observables` from `createForm` that contained all stores in the form of observables. This is no longer the case since the new `setters` (mentioned in the next section) fulfill the role of `update` and `set`.

### Helpers

The returned helpers have changed.
* `setField` and `setFields` are now only one function: `setFields` that accepts both signatures (and more). For example:
```javascript
setField('email', 'zaphod@beeblebrox.com`)
```
should now be
```javascript
setField('email', 'zaphod@beeblebrox.com')
```
* `setFields` does not touch a field by default. It needs to be explicit and needs a string path. E.g. `setField(‘email’ , 'zaphod@beeblebrox.com')` now is `setFields('email', 'zaphod@beeblebrox.com', true)`. For example:
```javascript
setField('email' , 'zaphod@beeblebrox.com')
```
should now be
```javascript
setFields('email', 'zaphod@beeblebrox.com', true)
```
* `setError` is now `setErrors`. Accepts either the whole error object, an updater function, a path and a value, or a path and an updater function. For example:
```javascript
setError('email', 'Not an email')
```
should now be
```javascript
setErrors('email', 'Not an email')
```
*  `setWarning` is now `setWarnings`. Accepts either the whole warnings object, an updater function, a path and a value, or a path and an updater function. For example:
```javascript
setWarning('password', 'Not secure')
```
should now be
```javascript
setWarnings('password', 'Not secure')
```
* `setTouched` now accepts either the whole warnings object, an updater function, a path and a value, or a path and an updater function. For example:
```javascript
setTouched('email')
```
should now be
```javascript
setTouched('email', true)
```
* `setTouched` no longer accepts an index as second argument, since that can be interpolated in the path. For example:
```javascript
setTouched('tag', 1)
```
should now be
```javascript
setTouched('tag[1]', true)
```

> These functions now can do more than before. Be sure to check the [documentation on them](/docs/solid/helper-functions#setters).

* `getField` is no longer returned from `createForm`, since this is obsolete now due to the accessors returned by `createForm`.

## TypeScript

Some adjusting of your types might be needed due to the following changes:

* When a transform function is added, setters for  `data` and `fields` will have looser types. They’ll have an `unknown` argument and expect you to guarantee that the shape will follow your `Data` type on your `transform` function.
* `initialValues` will have a type of `unknown` when a `transform` function is used.

## Configuration

* `initialValues` now passes through transform functions. This was a bug previously but some people might have relied on this.