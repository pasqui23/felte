---
section: Stores
subsections:
  - data
  - errors
  - warnings
  - touched
  - isValid
  - isSubmitting
  - isDirty
---

## Stores

Felte keeps stores internally to keep track of all of your data, these can be read by using accessors returned by `createForm`. These accessors are functions that when called return the current value of the store and trigger re-renders of your component whenever the value of the store changes. These accessors also accept a function as a first argument which can be used to obtain derived values from the stores, or to obtain a specific property of the store (in the case of stores that contain objects). The latter can also receive a string path to a property as well, but this is not type safe if using TypeScript.

If you use the accessor to get only a specific property of a store, then Felte will _only_ trigger re-renders when that specific property has changed.

### data

A store that contains the form's values. Depending on the field type, the values can be either a `string`, an array of `string`s, a `number`, a `boolean`, a `File`, an array of `File`s or `undefined` if no value has been set.

```jsx
import { createForm } from '@felte/react';

function Form() {
  const { form, data } = createForm({ onSubmit: values => console.log(values) });

  return (
    <form ref={form}>
      <input name="email" />
      <input name="password" type="password" />
      {/* The component will only re-render when the length of the password changes */}
      <span>Your password is {data(($data) => $data.password.length)} characters long</span>
      <button>Submit</button>
    </form>
  );
}
```

### errors

A store that contains the validation errors in the form. It will have the same shape as `data` but containing either a `string` or an array of `string`s with each validation message per field.

### warnings

A store that contains warnings on the form fields set by the `warn` function. Unlike the `errors` store, this store will have validation messages immediately and not only when a field is touched.

### touched

A store with the same shape as `data` but containing `boolean`s as values, defining if the field has been touched or not.

### isValid

A store containing a single boolean that tells if the form is valid or not.

### isSubmitting

A store containing a single boolean that tells if the form is submitting or not.

### isDirty

A store containing a single boolean that tells if the form is dirty or not.