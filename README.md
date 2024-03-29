# Acter

**Note:** This repository only captures an idea I've had. I have not implemented
it exactly, but I have implemented a spiritual successor to the ideas here in
https://github.com/tomashubelbauer/paper. It doesn't use JSX/TSX, but it does do
the rest the same way.

---

Like React, but more like JavaScript.

`try`-`catch` instead of error boundaries.

`async`-`await` instead of suspense etc.

## Status

Holy shit! Someone actually implemented this for me:

https://github.com/bikeshaving/crank

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

### Demonstrate the ideas captured here in Paper test cases / demos and retire

The ideas captured here should by and large be implemented in Paper. If not, I
will adjust Paper to be able to express them. After I've proven this, there is
no reason to keep this repository around, so I'll delete it.
