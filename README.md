<!-- @format -->

# jotai-composer

A library for composing [Jotai](https://jotai.org/) atoms with ease.

## Installation

```bash
npm install jotai-composer
# or
yarn add jotai-composer
# or
pnpm add jotai-composer
```

## Usage

```tsx
import React from "react";
import { atom, useAtom, Atom } from "jotai";
import {
  composeAtoms,
  composeWritableAtoms,
  deriveFromAtoms,
} from "jotai-composer";

// Create basic atoms
const countAtom = atom(0);
const nameAtom = atom("John");

// 1. Compose multiple atoms into a single read-only atom
const userDataAtom = composeAtoms({
  count: countAtom,
  name: nameAtom,
});

// 2. Compose multiple atoms into a single writable atom
const editableUserDataAtom = composeWritableAtoms({
  count: countAtom,
  name: nameAtom,
});

// 3. Create a derived atom from multiple source atoms
const greetingAtom = deriveFromAtoms<number | string, string>(
  [countAtom, nameAtom] as Atom<number | string>[],
  ([count, name]) => `Hello ${name}, you clicked ${count} times!`
);

// Example component
function UserProfile() {
  // Use the composed atoms in your components
  const [userData] = useAtom(userDataAtom);
  const [editableUserData, setEditableUserData] = useAtom(editableUserDataAtom);
  const [greeting] = useAtom(greetingAtom);

  return (
    <div>
      <h1>User Profile</h1>

      {/* Read-only data */}
      <div>
        <h2>Profile Info</h2>
        <p>Name: {userData.name}</p>
        <p>Count: {userData.count}</p>
      </div>

      {/* Editable data */}
      <div>
        <h2>Edit Profile</h2>
        <input
          value={editableUserData.name}
          onChange={(e) => setEditableUserData({ name: e.target.value })}
        />
        <button
          onClick={() =>
            setEditableUserData({ count: editableUserData.count + 1 })
          }
        >
          Increment
        </button>
      </div>

      {/* Derived data */}
      <div>
        <h2>Greeting</h2>
        <p>{greeting}</p>
      </div>
    </div>
  );
}
```

## API

### `composeAtoms(atoms)`

Composes multiple atoms into a single read-only atom.

### `composeWritableAtoms(atoms)`

Composes multiple primitive atoms into a single writable atom.

### `deriveFromAtoms(sourceAtoms, deriveFn)`

Creates a derived atom that depends on multiple source atoms.

## License

MIT
