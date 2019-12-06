# Acter

[**LIVE**](https://tomashubelbauer.github.io/acter)

Like React, but more like JavaScript.

`try`-`catch` instead of error boundaries.

`async`-`await` instead of suspense etc.

## Examples

Stateless:

```js
export default function Greeting(props: { name: string; }) {
  return (
    <h1>Hi, {props.name}</h1>;
  );
}
```

Fetch:

```js
export default async function* Greeting(props: { nameEndpoint: string; }) {
  yield <h1>Loading…</h1>;
  try {
    const response = await fetch(nameEndpoint);
    const json = await response.json();
    yield <h1>Hi, {json.name}</h1>;
  } catch (error) {
    yield <h1>Error loading the name! {error.toString()}</h1>;
  }
}
```

Counter:

```js
import { event } from 'acter';
export default function* Counter() {
  let counter = 0;
  const handleButtonClick = event(() => counter++);
  do {
    event.reset();
    yield <div>Counter: {counter}. <button onClick={handleButtonClick}>Click</button></div>;
  } while (await handleButtonClick);
}
```

Dice game:

```js
import { event } from 'acter';
export default function* DiceGame() {
  // { 'initial' } | { 'rolling' } | { 'rerolling', last } | { 'error' } | { 'rolled', value }
  let dice = { type: 'initial' };

  async function handleButtonClick() {
    try {
      // TODO: Handle initial roll vs reroll
      const response = await fetch('/dice');
      const json = await response.json();
      dice = { type: 'rolled', value: json.dice };
    } catch (error) {
      dice = { type: 'error' };
    }
  }

  const handleButtonClick = event(handleButtonClick);
  do {
    handleButtonClick.reset();
    yield (
      <div>
        {dice.type === 'rolled' && `Dice: ${dice}. `}
        <button
          disabled={dice.state ==== 'rolling' || dice.type === 'rerolling'}
          onClick={handleButtonClick}
        >
          {dice.type === 'initial' ? 'Roll' : 'Roll again'}
        </button>
        {dice.type === 'rolling' || dice.type === 'rerolling' && 'Rolling…'}
        {dice.type === 'error' && 'An error occured.'}
      </div>;
    );
  } while (awit handleButtonClick);
}
```

## To-Do

### Build a prototype 

### Consider using a Babel plugin for this

Would rewrite this to Hook based components.
