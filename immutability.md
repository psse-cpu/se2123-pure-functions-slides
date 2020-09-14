Immutability
------------



### Immutability

* No modifications to:
  - a primitive variable value, or 
  - an object's state

![mutate](images/mutate.jpeg)



### Tale of two Array methods

```js
// Array#push is mutable
const scores = [50, 99, 23];
scores.push(81);
console.log('scores ', scores);

// Array#concat is immutable
const temperatures = [37.5, 38, 36.9, 38.4];
const latest = temperatures.concat([39.1]);
console.log('previous temps: ', temperatures);
console.log('latest temps:   ', latest);
```

```txt
scores  [ 50, 99, 23, 81 ]
previous temps:  [ 37.5, 38, 36.9, 38.4 ]
latest temps:    [ 37.5, 38, 36.9, 38.4, 39.1 ]
```

`concat` does not modify the original array



#### Native patterns for immutable updates (1/2)

```js
// array spread operator
const scores = [50, 43, 19];
const newScores = [...scores, 31];
console.log('prev scores ', scores);
console.log('new  scores ', newScores);

// change middle item, use slice
const wrongScores = [50, 43, 19, 31, 100, 99, 7];
const corrected = [
  ...wrongScores.slice(0, 2), 
  91, 
  ...wrongScores.slice(3)
]
```

```js
> console.log('wrong', wrongScores)
// wrong [50, 43, 19, 31, 100, 99,  7]
> console.log('right', corrected)
// right [50, 43, 91, 31, 100, 99,  7]
```



#### Native patterns for immutable updates (2/2)

```js
// object spread operator
const point = { x: 37, y: -20 };
const newPoint = { ...point, x: point.x + 5, y: point.y + 3 };
console.log('old point', point);
console.log('new point', newPoint);
```

```text
old point { x: 37, y: -20 }
new point { x: 42, y: -17 }
```

* Remember, nested objects are shallow-copied

```js
const prevOwner = { name: 'Jeff' };
const dog = { name: 'Mike', owner: prevOwner };
const newPawrent = { ...dog }; // make a copy first
newPawrent.owner.name = 'Peter';
console.log('prev owner', prevOwner);
// prev owner { name: 'Peter' }  👈👈👈 NNAAANNNIII??
```



#### Did we just try to copy the dog and mutate?

* Yes, a pragmatic approach in most cases
  - unless you prefer

```js
function updateVeryNestedField(state, action) {
  return {
    ...state,
    first: {
      ...state.first,
      second: {
        ...state.first.second,
        [action.someId]: {
          ...state.first.second[action.someId],
          fourth: action.someValue
        }
      }
    }
  }
}
```



### A principle from [Clojure](https://clojure.org/reference/transients)

> If a tree falls in the woods, does it make a sound?<br><br>
> If a pure function mutates some local data in order to produce an immutable return value, is that ok?

* mutable local data is 👌
* mutable shared state NOT 👌



### What about `eslint-plugin-fp`'s `no-mutation` rule?

* If you insist on doing some hardcore FP
  - you can use libs like Ramda (as in _"lambda"_ calculus)

```js
R.mergeDeepRight({ name: 'fred', 
                   age: 10, 
                   contact: { email: 'moo@example.com' }},

                 { age: 40, 
                   contact: { email: 'baa@example.com' }});
//=> { name: 'fred', age: 40, 
//     contact: { email: 'baa@example.com' }}

// Warning:  shallow-copy
R.assocPath(['a', 'b', 'c'], 42, {a: {b: {c: 0}}}); 
//=> {a: {b: {c: 42}}}
``` 