# Week 4 answers

## 1. Array index as `:key`
Vue uses the key to decide which component instance belongs to which item. With the index, the card in position 0 keeps its instance even when filtering puts a different event there, so its local state (like an open details panel) stays at that position and shows up on the wrong event. With `:key="e.id"`, each state follows its own event, and cards for filtered-out events are removed.

## 2. `visible` as computed, not a function in the template
A computed is cached: it only recalculates when something it reads (`search`, `category` or `events`) changes. A function in the template runs again on every re-render, even for unrelated changes. When you type in the search box, both give the right result, but the function would filter the whole list again on every re-render, and the computed does the work only when its inputs change.

## 3. Why EventCard emits `book`
First, props are read-only and data flows one way, down from the parent. If the card changed `event.booked`, Vue would warn, and it would be hard to tell where the data changed. Second, the card doesn't know what booking means: only `App` can check the capacity, update `myBookings` and save to `localStorage`. By emitting, the card stays reusable and simple, and the data has one owner.

## 4. `const { booked } = reactive(event); booked++`
Destructuring copies the number out of the reactive object into a plain variable. That variable is no longer connected to the reactive proxy, so changing it never triggers an update, and the page stays the same. (With `const`, the `++` would even throw an error.) To keep the connection, use `toRefs` or change `event.booked` directly.

## 5. Where `myBookings` lives
It lives in `App.vue`, because `App` owns the events and `onBook` is the only place that changes them. The header, which is outside the list and the cards, also needs the count. State belongs in the closest common parent of everything that uses it. If it lived in a card, each card would have its own copy, and the header could not see any of them.