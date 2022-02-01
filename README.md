# forgette.js (preview)
Forgette about state management.
  
Why?
+ NOT a proxy
+ track only parts of object
+ it is safe for `const { property } = store`
+ `get` is safe from `set`: `const [store, get, set] = ....`
  
## Basics
1. Creating a store
```js
const nested_object = { favorite: { color: "red" } }

const [store, get, set] = lets_forgette(nested_object)
```
2. Get a nested property
```js
// simple case
console.log( get(store.favorite.color) )  // "red"

// complex case
const { color } = store.favorite
console.log( get(color) )                 // "red"
```
3. Updating a nested property
```js
set(color, "blue")

console.log(get(color), get(store.favorite.color))      // "blue" "blue"
```
  
## Using Arrays
1. Create an array inside store
```js
const array = ["one","two","three"]

const [store, get, set] = lets_forgette({array})
```
2. Use array functions
```js
store.array.forEach(num => console.log( get(num) ))         // "one",, "two",, "three"

const vals = store.array.map(get)

console.log(vals)                                           // ["one", "two", "three"]
```
3. Get items at index
```js
console.log( get( store.array[0] ) )                        // "one"

const [first, second] = store.array

console.log( get(second) )                                  // "two"
```
4. Some functions no
```js
store.array.push // no it will break

store.array.splice // no 
```
  
## Create new property or add item to array
1. Create new property
```js
const [obj_store, get_obj, set_obj] = lets_forgette({ lang: "JS" })

set_obj(obj_store.favorite, "maybe")
```
2. Add to array
```js
const [arr_store, get_arr, set_arr] = lets_forgette({ langs: ["CSS"] })

set_arr(arr_store.langs[1], "HTML")
```
  
## How it works
1. Stores are just the path to the stored data
```js
const [store, get, set] = lets_forgette({ is_online: true })

console.log( get( store.is_online ) )       // true

console.log( store.is_online )              // { path: "/is_online" }
```
2. Some stores break if it is a Proxy
```js
const { is_online } = store

console.log(is_online)              // { path: "/is_online" }

set(store.is_online, false) 

console.log( get(is_online) )       // false
```
3. Getting a property that is undefined is okay
```js
console.log(store.whatever)         // { path: "/whatever" }
```
4. Property undefined is how new properties are created
```
const { anything } = store
console.log( get(anything) )        // undefined

set(store.anything, "new thing!")

console.log( get(anything) )        // "new thing!"
```
5. Array functions are called on the store!
```js
const [store_array, get, set] = lets_forgette({ list: [1,2,3] })

store_array.forEach(n => {
  console.log(n)              // { path: "/list/0" },, { path: "/list/1" },, { path: "/list/2" }
})
```
