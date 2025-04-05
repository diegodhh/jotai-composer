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
import { atom, useAtom } from "jotai";
import { extendStateAndDeriveFromDecorator } from "jotai-composer";

// Create a base atom
const baseAtom = atom({ count: 0, name: "John" });

// Create multiple decorators
const withGreeting = extendStateAndDeriveFromDecorator({
  getter: ({ last }) => ({
    greeting: `Hello, ${last.name}!`,
  }),
  setter: ({ stateHelper, update }) => {
    const { last, set } = stateHelper;
    if (update.name !== undefined) {
      set(baseAtom, { ...last, name: update.name });
    }
  },
});

const withCounter = extendStateAndDeriveFromDecorator({
  getter: ({ last }) => ({
    doubleCount: last.count * 2,
    isEven: last.count % 2 === 0,
  }),
  setter: ({ stateHelper, update }) => {
    const { last, set } = stateHelper;
    if (update.count !== undefined) {
      set(baseAtom, { ...last, count: update.count });
    }
  },
});

const withHistory = extendStateAndDeriveFromDecorator({
  getter: ({ last }) => ({
    history: Array(5)
      .fill(0)
      .map((_, i) => last.count - i),
  }),
});

// Pipe decorators together
const enhancedAtom = withHistory(withCounter(withGreeting(baseAtom)));

// Example component
function UserProfile() {
  const [state, setState] = useAtom(enhancedAtom);

  return (
    <div>
      <h1>User Profile</h1>

      {/* Basic state */}
      <div>
        <h2>Basic Info</h2>
        <p>Name: {state.name}</p>
        <p>Count: {state.count}</p>
      </div>

      {/* Greeting decorator */}
      <div>
        <h2>Greeting</h2>
        <p>{state.greeting}</p>
        <input
          value={state.name}
          onChange={(e) => setState({ name: e.target.value })}
        />
      </div>

      {/* Counter decorator */}
      <div>
        <h2>Counter</h2>
        <p>Double Count: {state.doubleCount}</p>
        <p>Is Even: {state.isEven ? "Yes" : "No"}</p>
        <button onClick={() => setState({ count: state.count + 1 })}>
          Increment
        </button>
      </div>

      {/* History decorator */}
      <div>
        <h2>History</h2>
        <ul>
          {state.history.map((count, index) => (
            <li key={index}>
              Count {index + 1} steps ago: {count}
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
}
```

## API

### `extendStateAndDeriveFromDecorator`

A decorator function that extends a base atom with additional state and behavior.

#### Parameters

- `getter`: A function that derives additional state from the base atom's state.
- `setter`: A function that handles state updates and can modify the base atom's state.

#### Returns

A new atom that combines the base atom's state with the derived state.

## Advanced Usage

### Creating Custom Decorators

You can create custom decorators for specific functionality:

```tsx
// Create a validation decorator
const withValidation = extendStateAndDeriveFromDecorator({
  getter: ({ last }) => ({
    isValid: last.name.length > 0 && last.count >= 0,
  }),
});

// Create a formatting decorator
const withFormatting = extendStateAndDeriveFromDecorator({
  getter: ({ last }) => ({
    formattedCount: `Count: ${last.count}`,
    formattedName: `Name: ${last.name}`,
  }),
});

// Combine multiple decorators
const finalAtom = withFormatting(withValidation(enhancedAtom));
```

### Composing Decorators

You can also create higher-order decorators that combine multiple decorators:

```tsx
const composeDecorators =
  (...decorators) =>
  (baseAtom) =>
    decorators.reduce((atom, decorator) => decorator(atom), baseAtom);

// Usage
const combinedDecorator = composeDecorators(
  withGreeting,
  withCounter,
  withHistory,
  withValidation,
  withFormatting
);

const finalAtom = combinedDecorator(baseAtom);
```

## License

MIT
