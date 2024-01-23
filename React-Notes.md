https://github.com/AsjadMahmood/revision-notes/blob/main/React-Notes.md
--------------------------------------------------------------------------------------------------------------------------------

### Adding Interactivity (new-docs) 

## 1. Responding To Events 

By convention, it is common to name event handlers as handle followed by the event name. You’ll often see onClick={handleClick}, onMouseEnter={handleMouseEnter}, and so on.

Functions passed to event handlers must be passed, not called. For example:

```
<button onClick={handleClick}>	//passing a function (correct)	
<button onClick={handleClick()}> calling a function (incorrect)
```
The difference is subtle. In the first example, the handleClick function is passed as an onClick event handler. This tells React to remember it and only call your function when the user clicks the button.

In the second example, the () at the end of handleClick() fires the function immediately during rendering, without any clicks. This is because JavaScript inside the JSX { and } executes right away. Same is true for inline function

```
<button onClick={() => alert('...')}> //passing a function (correct)
<button onClick={alert('...')}> // calling a function (incorrect)
```

- Can also pass event handlers as props (for child to parent communication).By convention, event handler props should start with on, followed by a capital letter.

- Event propagation: Event handlers will also catch events from any children your component might have. We say that an event “bubbles” or “propagates” up the tree: it starts with where the event happened, and then goes up the tree.

- Events propagate upwards. Call e.stopPropagation() on the first argument to prevent that.

- Explicitly calling an event handler prop from a child handler is a good alternative to propagation.
```
<button onClick={e => {
      e.stopPropagation();
      onClick();
}}>
```

- Event handlers receive an event object as their only argument. By convention, it’s usually called e, which stands for “event”. You can use this object to read information about the event. for example ```e.target.value``` or can stop propgation to parent like 

```e.stopPropagation();```

Preventing default behavior: ```e.preventDefault()``` prevents the default browser behavior for the few events that have it.

Event Handlers produce side effects because event handlers don’t need to be pure, so it’s a great place to change something —for example, change an input’s value, or change a list in response to a button press. So they are a place where we can setState.

## 2. State: A Component's Memory 

- Where there's a need for a component to remember something, (for ex: interaction) in between rerendering, we can make that part
state. Components need to “remember” things (ex: the current input value, the current image, the shopping cart). In React, this kind of component-specific memory is called state.

- **Question:** Why cant we use local variable instead of State ?

1. Local variables don’t persist between renders. When React renders this component a second time, it renders it from scratch—it doesn’t consider any changes to the local variables.
2. Changes to local variables won’t trigger renders. React doesn’t realize it needs to render the component again with the new data.

- Two update state two need to happend **Retain** (state variable) **Trigger** (state setter func)

- **Hooks** are special functions that are only available while React is rendering. They let you “hook into” different React features.

-  **Rules of Hooks** 
1. Don’t call Hooks inside loops, conditions, or nested functions
2. Only Call Hooks from React Functions(i think it means set state variable using it's setter.)
3. Call hook from top level of the component function.

- Every time your component renders, useState gives you an array containing two values:
1. The state variable ('index' just w.r.t below ex.) with the value you stored.
2. The state setter function ('setIndex') which can update the state variable and trigger React to render the component again.

- **Behind the scene** when you set a state ```const [index, setIndex] = useState(0);```

1. Your component renders the first time. Because you passed 0 to useState as the initial value for index, it will return [0, setIndex]. React remembers 0 is the latest state value.
2. You update the state. When a user clicks the button, it calls setIndex(index + 1). index is 0, so it’s setIndex(1). This tells React to remember index is 1 now and triggers another render.
3. Your component’s second render. React still sees useState(0), but because React remembers that you set index to 1, it returns [1, setIndex] instead.
4. And so on!

- If you find that you often change two state variables together, it might be better to combine them into a single one. For example, if you have a form with many fields, it’s more convenient to have a single state variable that holds an object than state variable per field.

- You can have more than one state variable. Internally, React matches them up by their order.

- State is private to the component. If you render it in two places, each copy gets its own state.

- **Question:** How does React know which state to return? / There is no “identifier” that is passed to useState, so how does it know which of the state variables to return?

- **Ans**The order in which the state is declared (on the top of the component, not within consitions or loops) is what that helps it to set it correctly. Because it's not magic it's an array after all.

- If hooks are declared in conditional statement/ not declared in top level of the component, then we might get this error : 'Rendered fewer hooks than expected.'

- A state variable is only necessary to keep information between re-renders of a component. Within a single event handler, a regular variable will do fine. Don’t introduce state variables when a regular variable works well.

## 3. Render and Commit 

**Step 1:** 'Triggering' a Render

**Step 2:** React 'Renders' your components
“Rendering” is React calling your components.

1. On initial render, React will call the root component. ```root.render(<Image />); //in index.js``` (Image is a component)
2. For subsequent renders, React will call the function component whose **state update** triggered the render.

- This process is recursive: if the updated component returns some other component, React will render that component next, and if that component also returns something, it will render that component next, and so on. The process will continue until there are no more nested components and React knows exactly what should be displayed on screen.

- During the initial render: React will create the DOM nodes for ```<section>, <h1>, and three <img> tags``` (example)
- During a re-render: React will calculate which of their properties, if any, have changed since the previous render. It won’t do anything with that information until the next step, the commit phase.

- Rendering must always be a pure calculation. (react is doing rendering btw)

- **Optimizing performance :** The default behavior of rendering all components nested within the updated component is not optimal for performance if the updated component is very high in the tree. If you run into a performance issue, there are several opt-in ways to solve it described in the Performance section. Don’t optimize prematurely!

**Step 3:** React 'Commits' changes to the DOM: 

After rendering (calling) your components, React will modify the DOM.

- For the initial render, React will use the appendChild() DOM API to put all the DOM nodes it has created on screen.
- For re-renders, React will apply the minimal necessary operations (calculated while rendering, changes etc) to make the DOM match the latest rendering output.

- React does not touch the DOM if the rendering result is the same as last time

- After rendering is done and React updated the DOM, the browser will repaint the screen. Although this process is known as “browser rendering”, we’ll refer to it as “painting” 

## 4. State: As a Snapshot

***“Rendering”*** means that React is calling your component, which is a function. The JSX you return from that function is like a ***snapshot*** of the UI in time. Its props, event handlers, and local variables were all calculated using its state at the time of the render.

When React re-renders (when a state is changed) a component:

1. React calls your function again.
2. Your function returns a new JSX snapshot. (react calculates if there are changes)
3. React then updates the screen to match the snapshot you’ve returned.

As a component’s memory, state is not like a regular variable that disappears after your function returns. State actually “lives” in React itself—as if on a shelf!—outside of your function. When React calls your component, it gives you a snapshot of the state for that particular render. Your component returns a snapshot of the UI with a fresh set of props and event handlers in its JSX, all calculated using the state values from that render!

1. You tell React to update the state

2. React updates the state value

3. React passes a snapshot of the state value (meaning original state is in react) into the component

- **Question:** 
What's the output of this? 

```
  setNumber(0 + 5);
  setTimeout(() => {
    alert(0);
  }, 3000)
 ```

output = 0;
The state stored in React may have changed by the time the alert runs, but it was scheduled using a snapshot of the state at the time the user interacted with it!

- **A state variable’s value never changes within a render** (because you have a old snapshot), even if its event handler’s code is asynchronous. Inside that render’s onClick, the value of number continues to be 0 even after setNumber(number + 5) was called. Its value was **fixed** when React **took the snapshot** of the UI by calling your component.

- **React keeps the state values “fixed” within one render’s event handlers.** You don’t need to worry whether the state has changed while the code is running.
- If you need a updated state before the next render use the state **updater function** ```setNumber(number=> number+3)```

- When you call useState, React gives you a snapshot of the state for that render.
- Variables and event handlers don’t “survive” re-renders (they have previous state value, given to them in snaphsot). Every render has its own event handlers.
- Every render (and functions inside it) will always “see” the snapshot of the state that React gave to that render.
- Event handlers created in the past (before render, like in case of setTimeOut of 5 sec) have the state values from the render in which they were created.

https://beta.reactjs.org/learn/state-as-a-snapshot (quickly do the challenge at the last)

## 5.Queueing a Series of State Updates

- Setting a state variable will queue another render. But sometimes you might want to perform multiple operations on the value before queueing the next render. To do this, it helps to understand how React **batches** (group of things produced at once) state updates.

-  Each render’s state values are fixed (a state cannot have multiple values in one render (function call that return JSX))

-  React waits until all code in the event handlers has run before processing your state updates (before re rendering). This lets you update multiple state variables—even from multiple components—without triggering too many re-renders.(because react wait for the code to run in the event handler and in that handler we can set multiple states). But this also means that the UI won’t be updated until after your event handler, and any code in it, completes. This behavior, also known as **batching**, makes your React app run much faster. 

- React does not batch across multiple intentional events like clicks—each click is handled separately. Rest assured that React only does batching when it’s generally safe to do. (react does it on it's end)

- For updating the same state variable multiple times before the next render (uncommon use case) we use **updator function** in setState which is a way to tell react “do something with the state value” instead of just replacing it.

- Behind the scene what updator function does is:
1. React queues this function to be processed after all the other code in the event handler has run.
2. During the next render, React goes through the queue and gives you the final updated state.

```
setNumber(n => n + 1): n => n + 1 // is a function. React adds it to a queue.
setNumber(n => n + 1): n => n + 1 // is a function. React adds it to a queue.
setNumber(n => n + 1): n => n + 1 // is a function. React adds it to a queue.
```
- When you call useState during the next render, React goes through the queue (so, the 2nd step). The previous number state was 0, so that’s what React passes to the first updater function as the n argument. Then React takes the return value of your previous updater function and passes it to the next updater as n, and so on:

- What happens if you update state after replacing it ?
```
<button onClick={() => {
  setNumber(number + 5); //replacing
  setNumber(n => n + 1); //updating
}}>
```
Here’s what this event handler tells React to do:
1. setNumber(number + 5): number is 0, so setNumber(0 + 5). React adds “replace with 5” to its queue.
2. setNumber(n => n + 1): n => n + 1 is an updater function. React adds that function to its queue.

**Deep** You may have noticed that setState(x) actually works like setState(n => x), but n is unused! (in case of replacing)

```     
setNumber(n => n + 1);
setNumber(number + 5);
```
Output = ?
```
setNumber(number + 5);
setNumber(n => n + 1);
setNumber(42);
```
Output = ?

| queued update	| n             | Return|
| ------------- |:-------------:| -----:|
|“replace with 5| (0) unused    | 5     |
| n => n + 1    | 5             | 5 + 1 = 6|
|“replace with 42”| 6 (unused)  |    42 |

To summarize, here’s how you can think of what you’re passing to the setNumber state setter:

1. **An updater function** (e.g. n => n + 1) gets added to the queue.
2. **Any other value** (e.g. number 5) adds “replace with 5” to the queue, ignoring what’s already queued.

- Updater functions must be pure and only return the result. Don’t try to set state from inside of them or run other side effects
- It’s common to name the updater function argument by the first letters of the corresponding state variable. (naming convention) ```setEnabled(e => !e); //state name = enable```

- React processes state updates after event handlers have finished running. This is called batching.

Solve Challenges for Practice: 

## 6. Updating Objects in State

- Technically, it is possible to change the contents of the object itself. This is called a **mutation**:
```
const [position, setPosition] = useState({ x: 0, y: 0 });
position.x = 5;
```

- Although objects in React state are technically mutable, you should treat them as if they were immutable—like numbers, booleans, and strings. Instead of **mutating** them, you should always replace them.

- In other words, you should **treat any JavaScript object that you put into state as read-only**

```
  const [position, setPosition] = useState({ x: 0, y: 0 }); // treat 'position' as read only. don't do position.x = 4 etc;
```

- Remember: Local mutation is fine. Mutation is only a problem when you change existing objects that are already in state. Mutating an object you’ve just created is okay because no other code references it yet. Changing it isn’t going to accidentally impact something that depends on it. This is called a **“local mutation”**. You can even do local mutation while rendering. Very convenient and completely okay!

- Copying objects with the spread syntax. For example, you may want to update only one field in a form, but keep the previous values for all other fields.

```
//use the ... object spread syntax so that you don’t need to copy every property separately.
    setPerson((per)=>{
     return {
        ...per,  
      firstName:e.target.value,
    }
    })
```    

*Note that the ... spread syntax is “shallow”—it only copies things one level deep. This makes it fast, but it also means that if you want to update a nested property, you’ll have to use it more than once.*

```

const [person, setPerson] = useState({
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg',
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
});

    setPerson((per)=>{
     return 
     setPerson({
      ...person, // Copy other fields
      artwork: { // but replace the artwork
      ...person.artwork, // with the same one
      city: 'New Delhi' // but in New Delhi!
  }
});
    })

```

- For deleting a key from state object :

```
setData((prevData) => {
  const newData = {...prevData}
  delete newData["key_here"]
  return newData;
})
```
- **Objects are not really nestedHowever**, “nesting” is an inaccurate way to think about how objects behave. When the code executes, there is no such thing as a “nested” object. You are really looking at two different objects:

```
let obj1 = {
  title: 'Blue Nana',
  city: 'Hamburg',
  image: 'https://i.imgur.com/Sd1AgUOm.jpg',
};

let obj2 = {
  name: 'Niki de Saint Phalle',
  artwork: obj1
};
```

- Can use **Immer** to write precise update logic, in case your state is deeply nested (just to improve readability)
- With Immer, the code you write looks like you are “breaking the rules” and mutating an object.But unlike a regular mutation, it doesn’t overwrite the past state!

```
updatePerson(draft => {
  draft.artwork.city = 'Lagos';
});

```

- *Immer usage*: Install 'use-Immer' package, replace import { useState } from 'react' with import { useImmer } from 'use-immer'. Then in the component

```
  const [person, updatePerson] = useImmer({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    updatePerson(draft => {
      draft.name = e.target.value;
    });
  }
```

- **In Short Treat all state in React as immutable.**

## 7. Updating Arrays in State

- In JavaScript, arrays are just another kind of object. Like with objects, you should treat arrays in React state as read-only. This means that you shouldn’t reassign items inside an array like ```arr[0] = 'bird'```, and you also shouldn’t use methods that mutate the array, such as push() and pop().

| 	            |avoid (mutates the array) | prefer (returns a new array)|
| ------------- |:-------------:           | ---------------------------:|
|adding         |push, unshift             | concat, [...arr] spread syntax|
|removing       |pop, shift, splice        |filter, slice  |
|replacing      |splice, arr[i] = ... assignment  |  map   |
|sorting        |reverse, sort             |copy the array first |

*Note*: slice and splice are two diferent methods. In react we use slice
- slice lets you copy an array or a part of it. ( The slice() method lets you cut a “slice” of the array)
- splice mutates the array (to insert or delete items).

in react we use slice.

Alternatively, you can use **Immer** which lets you use methods from both columns.

```
instead of push use below approach (spread oprator) to add values.

setArtists( // Replace the state
  [ // with a new array
    ...artists, // that contains all the old items
    { id: nextId++, name: name } // and one new item at the end
  ]
);
```

- For inserting/adding from start or end use **spread** oprator
- For removing use **filter** operator
- For transforming or replacing use **map** 
- For inserting/adding at a specific index use **slice** together with **spread** operator. example below
```
    const insertAt = 1; // Could be any index
    const nextArtists = [
      // Items before the insertion point:
      ...artists.slice(0, insertAt),
      // New item:
      { id: nextId++, name: name },
      // Items after the insertion point:
      ...artists.slice(insertAt)
    ];
    setArtists(nextArtists);
```
- To reverse and sort copy array first using spread operator and than use reverse/sort (whihc originally mutate an array)
```
    const nextList = [...list];
    nextList.reverse();
```
*Note:*  copying here is one level shallow copy. So even if you copy an array, you can’t mutate existing items inside of it directlys

- Be careful when **Updating objects inside arrays** When updating nested state, you need to create copies from the point where you want to update, and all the way up to the top level. So create copy of object (yu want to change) inside the array and also create copy of the array. 

- In general, you should only mutate objects that you have just created (so mutation allowed here). If you were inserting a new artwork, you could mutate it, but if you’re dealing with something that’s already in state, you need to make a copy.

- Generally, you shouldn’t need to update state more than a couple of levels deep. If your state is more then couple level deep
restructure it to flaten it up.
- If you don’t want to change your state structure, you might prefer to use **Immer**, which lets you write using the convenient but mutating syntax and takes care of producing the copies for you.

*Also note immer has a convention of update followed by state name, insted of set* ```const [myList, updateMyList] = useImmer(initialList);```

- Behind the scenes, Immer always constructs the next state from scratch according to the changes that you’ve done to the draft. Benefit: This keeps your event handlers very concise without ever mutating state.

- With Immer, you can write code in the mutative fashion, as long as you’re only mutating parts of the draft that Immer gives you. For example

```
  function handleDeleteTodo(todoId) {
    updateTodos(draft => {
      const index = draft.findIndex(t =>
        t.id === todoId
      );
      draft.splice(index, 1);
    });
  }

```

### Managing State (new docs)

## 1. Reacting to Input with State
- Manipulating the UI imperatively works well enough for isolated examples, but it gets exponentially more difficult to manage in more complex systems. React was built to solve this problem.

- Implementing UI in React have following Steps 

1. Step 1: **Identify your component’s different visual states**

for ex. in case of Form Submission we usually have these visual states 
- Empty: Form has a disabled “Submit” button.
- Typing: Form has an enabled “Submit” button.
- Submitting: Form is completely disabled. Spinner is shown.
- Success: “Thank you” message is shown instead of a form.
- Error: Same as Typing state, but with an extra error message. 

2. Step 2: **Determine what triggers those state changes**

- Mainly two things trigger state updates
1. Human inputs, like clicking a button, typing in a field, navigating a link.(require event handlers)
2. Computer inputs, like a network response arriving, a timeout completing, an image loading.
- In both cases, you **must set state variables** to update the UI.

3. Step 3: **Represent the state in memory with useState**
- Create State by using **useState** for your visual state.
- Remember, Simplicity is key: each piece of state is a “moving piece”, and you want as **few moving pieces”** as possible. More complexity leads to more bugs!

4. Step 4: **Remove any non-essential state variables**

- Refactoring the states. Remove non-essential state to avoid bugs and paradoxes (things causing contradictions among themselves). Only tracking what is essential.
- Your goal is to **prevent the cases where the state in memory doesn’t represent any valid UI** that you’d want a user to see. for ex. showing error messsage (it's a state) and disabling input (also stored in state) at the same time
- Questions Needed to be asked to yourself while creating State.
1. **Does this state cause a paradox?**
- For example, isTyping and isSubmitting can’t both be true. There are four possible combinations of two booleans, but only three correspond to valid states. To remove the “impossible” state, you can combine these into a status that must be one of three values: 'typing', 'submitting', or 'success'.
See code example at the last.

2. **Is the same information available in another state variable already?** 
- Meaning can a state be derived from another state. Common ex.  ```isEmpty``` and ```isTyping``` can’t be true at the same time. By making them separate state variables, you risk them going out of sync and causing bugs. Fortunately, you can remove ```isEmpty``` and instead check ```answer.length === 0```.

3. Can you get the same information from the inverse of another state variable? **isError** is not needed because you can check **error !== null** instead.

- Before Clean up
```
const [isEmpty, setIsEmpty] = useState(true);
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isError, setIsError] = useState(false);

```
- After Cleanup
```
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null); // can also be removed because when status is success there's no error
const [status, setStatus] = useState('typing'); // 'typing', 'submitting', or 'success'

```
- **Deep Dive** Eliminating “impossible” states with a reducer. 
To model the state more precisely, you can extract it into a reducer. **Reducers** let you unify multiple state variables into a single object and consolidate all the related logic!

5. Step 5: **Connect the event handlers to set state.**

- To set (update) the state in the event handlers. 

- Do Solve the challenges

## 2. Choosing the State Structure

- Principles for structuring state 
1. **Group related state**. If you always update two or more state variables at the same time, consider merging them into a single state variable. If some two state variables always change together, it is good idea to unify them into a single state variable. Also in case of 'Forms' it's good to group.

2. **Avoid contradictions in state**. When the state is structured in a way that several pieces of state may contradict and “disagree” with each other. for ex. 
In a form with two states ```isSending``` & ```isSent``` can   never be true at the same time, it is better to replace them with one status state variable that may take one of three valid states: 'typing' (initial), 'sending', and 'sent'.

3. **Avoid redundant state**. If you can calculate some information from the component’s props or its existing state variables during rendering, you should not put that information into that component’s state. For ex. 
In a form for setting ```firstName``` ```lastName``` ```fullName```, fullName is reduntant. Can be calculated by.

*Here, fullName is not a state variable. Instead, it’s calculated during render*

```const fullName = firstName + ' ' + lastName;```;
Also don't mirror props in state. i.e. dont declare state which is already present in props

4. **Avoid duplication in state**. When the same data is duplicated between multiple state variables, or within nested objects, it is difficult to keep them in sync. Reduce duplication when you can. For ex In below code. 

```
const initialItems = [{ id: 0, title: 'pretzels'}, ...]
  const [items, setItems] = useState(initialItems);
  const [selectedItem, setSelectedItem] = useState(
    items[0]
  );
  ```
Here ```selectedItem``` is duplicated. It will work in this scenrio but if we add the feature to update the name of th item then we would need to update the ```selectedItem``` as well. So a better thing will be to store ```itemId``` in state as ```selectedItemId```. And declare a variable which after searching the item in the arr gets updated at every render. like this   ```const selectedItem = items.find(item => item.id === selectedId );```. BTW can also store index instead of Id in state.

5. **Avoid deeply nested state**. Deeply hierarchical state is not very convenient to update. When possible, prefer to structure state in a flat way. Because updating nested state involves making copies of objects all the way Up from the part that changed.
See example for this in docs https://beta.reactjs.org/learn/choosing-the-state-structure#avoid-deeply-nested-state


- Structuring state efficiently keeps everything in sync and reduce bugs

## 3. Sharing State Between Components

**Lifting State Up**

Sometimes, you want the state of two components to always change together. To do it, remove state from both of them, move it to their closest common parent, pass it down to them via props and event handler to set the state. For ex.
In case of ```Accordion``` component with multiple ```Panels``` components, you want to expand one panel at a time, and close all others, you can lift state up by following 2 steps

1. Remove state from the child components 
2. Pass  hardcoded data (for now because no state is created in parent) from the common parent
3. Add state to the common parent. 
Sometimes lifting state up to parent changes it's type as well. Like when the state was in panel it was boolean and now it's number. The state in parent would be.
```
const [activeIndex, setActiveIndex] = useState(null);
```
The Accordion component needs to explicitly allow the Panel component to change its state by passing an event handler down as a prop.
```
    <Panel
      title="About"
      isActive={activeIndex === 0}
      onShow={() => setActiveIndex(0)}
    >
```
 Instead of duplicating shared state between components, you will lift it up to their common shared parent, and pass it down to the children that need it. This is **a single source of truth for each state**.

**Controlled and uncontrolled components**

- It is common to call a component with some local state **“uncontrolled”**. For ex, the original ```Panel``` component with an ```isActive``` state variable is uncontrolled because its parent cannot influence whether the panel is active or not.

- In contrast, you might say a component is **“controlled”** when the important information in it is driven by props rather than its own local state. This lets the parent component fully specify its behavior. The final ```Panel``` component with the ```isActive``` prop is controlled by the Accordion component, hence called controlled component.

- Uncontrolled components are easier to use within their parents because they require less configuration.
But they’re less flexible when you want to coordinate them together. 

- Controlled components (controlled by their parent via props) are maximally flexible, but they require the parent components to fully configure them with props.

- In practice, “controlled” and “uncontrolled” aren’t strict technical terms—each component usually has some mix of both local state and props

- When writing a component, consider which information in it should be controlled (via props), and which information should be uncontrolled (via state). But you can always change your mind and refactor later.

- 

## 4. Preserving and Setting State

State is isolated between components. React keeps track of which state belongs to which component based on their place in the UI tree. You can control when to **preserve** state and when to **reset** it between re-renders.

- Browsers use many tree structures to model UI. The DOM represents HTML elements, the CSSOM does the same for CSS.

- React also uses tree structure to manage and model the UI you make. React makes UI trees from your JSX. Then React DOM updates the browser DOM elements to match that UI tree.

**State is tied to a position in the tree** : 

- Meaning doesn't lives inside a component rather it is held inside React. React associates each piece of state it’s holding with the correct component by where that component **sits in the UI tree**. for ex. render two ```<counter/>``` components. These are two separate counters because each is rendered at its **own position in the tree**. Each of them (components) will get its own, independent, score and hover states.

- React will keep the state around for as long as you render the same component at the same position. for ex. by removing the component and adding it again in the UI it losts it's state, because React **preserves** a component’s state for as long as it’s being rendered at its **position in the UI tree**. If it gets removed, or a different component gets rendered at the same position, React discards its state.

- **Same component at the same position -> Preserves State**
- In the following ex: Updating the ```App``` state does not reset the ```Counter``` because ```Counter``` stays in the same position. It’s the same component at the same position, so from React’s perspective, it’s the same counter.
```
// consider the boilerplate of App component
    {isFancy ? (
      <Counter isFancy={true} /> 
    ) : (
      <Counter isFancy={false} /> 
    )}
```

**Pitfall** 

- Remember that it’s the position in the UI tree—not in the JSX markup—that matters to React!

-  React doesn’t know where you place the conditions in your function. All it “sees” is the tree you return. In both cases, the App component returns a ```<div>``` with ```<Counter />```as a first child. This is why React considers them as the same ```<Counter />```. You can think of them as having the same “address”: the first child of the first child of the root. This is how React matches them up between the previous and next renders, regardless of how you structure your logic.

**Different components at the same position -> Reset State**

- In the following ex: You switch between different component types at the same position. Initially, the first child of the ```<div>``` contained a ```Counter```. But when you swapped in a ```p```, React removed the ```Counter``` from the UI tree and destroyed its state. When you render a different component in the same position, it resets the state of its entire subtree

```
      {isPaused ? (
        <p>See you later!</p> 
      ) : (
        <Counter /> 
      )}
```      
- In the following ex: Will the state be preserved? No. Because the structure of your tree needs to “match up”. 
1. When ```fancy = false```. It contain ```<section>``` tag. 
2. When ```fancy = true```, now ```<div>``` gets added. So,
3. When the child ```div``` was removed from the DOM (```section```), the whole tree below it (including the Counter and its state) was destroyed as well. i.e. structure was changed.

- As a **rule of thumb**, if you want to preserve the state between re-renders, the structure of your tree needs to “match up” from one render to another. If the structure is different, the state gets destroyed because React destroys state when it removes a component from the tree.

```
<div>
      {isFancy ? (
        <div>
          <Counter isFancy={true} /> 
        </div>
      ) : (
        <section>
          <Counter isFancy={false} />
        </section>
      )}
</div>      
// checkbox to add/remove fancy styles
```
**Pitfall** Don't next components, i.e. do not declare a function defination inside another function. Whenever main component (MyComponent) state changes, the other component (defined inside) state resets. This is because a different MyTextField function is created for every render of MyComponent. You’re rendering a different component in the same position, so React resets all state below. for ex:

```
export default function MyComponent() {
  function MyTextField() {
    //retun input field
  }
  return (
    <>
      <MyTextField />
    </>
  );
}
```
To avoid this problem, always declare component functions at the top level (like we normally do), and don’t nest their definitions.

**Resetting state at the same position**

- By default, React preserves state of a component while it stays at the same position. Usually, this is exactly what you want, so it makes sense as the default behavior.

But sometimes, you may want to reset a component’s state. Which (resetting of state) we can acheive by two methods

1. Render components in different positions
2. Give each component an explicit identity with key.

For example: The first chunk of code preserve the code, and the second chunk of code reset the code (option 1) because we reset the code.

Option 1 : Here we preserve states. because they are rendered in same position.
```
//consider a state isPlayerA with boolean value
    {isPlayerA ? (
      <div><Counter person="Taylor" /></div>
    ) : (
      <Counter person="Sarah" />
    )}
```
Here we are Render components in different positions so it reset the state
```
  {isPlayerA &&
    <Counter person="Taylor" />
  }
  {!isPlayerA &&
    <Counter person="Sarah" />
  }
```

Option 2: More generic way is to assign keys to components which help React distinguish between different components. By default, React uses order within the parent (“first counter”, “second counter”) to discern between components. But keys let you tell React that this is not just a first counter, or a second counter, but a specific counter. For ex:

```
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
```
Remember that keys are not globally unique. They only specify the position within the parent.

**Resetting a form with a key** 
```
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat contact={to} key={to.id}/>
    </div>
  )
```
- Here the message is preserved in the ```Chat``` component (which contains, textarea). Which we don't want in Chat App. To fix it
add a key like ```<Chat key={to.id} contact={to} /> ```. Adding a key inside chat ```text-area``` wont fix it (currently don't know why)

**Preserving state for removed components**

1. You could render all chats instead of just the current one, but hide all the others with CSS. The chats would not get removed from the tree, so their local state would be preserved. Work good for **small/simple** UI's but it can get very slow if the hidden trees are large and contain a lot of DOM nodes.
2. You could lift the state up and hold the pending message for each recipient in the parent component. This way, when the child components get removed, it doesn’t matter, because it’s the parent that keeps the important information. This is the most common / **best** solution. ```{ id: 0, name: 'Taylor', email: 'taylor@mail.com', message:'' }```
3. You might also use a different source in addition to React state. For example, you probably want a message draft to persist even if the user accidentally closes the page. To implement this, you could have the Chat component initialize its state by reading from the localStorage, and save the drafts there too. Least common solution.

**To Summarise All**
- React keeps state for as long as the same component is rendered at the same position.
- State is not kept in JSX tags. It’s associated with the tree position in which you put that JSX.
- You can force a subtree to reset its state by giving it a different key.
- Don’t nest component definitions (creating component inside other component), or you’ll reset state by accident.

Do Challenged for Practice: https://beta.reactjs.org/learn/preserving-and-resetting-state


## 5. Extracting State Logic into a Reducer

- Components with many state updates spread across many event handlers can get overwhelming. For these cases, you can consolidate (combine (a number of things) into a single more effective, coherant whole) all the state update logic outside your component in a single function, called a reducer.

- As the component grows, so does the amount of state logic sprinkled throughout it. To reduce this complexity and keep all your logic in one easy-to-access place, you can move that state logic into a single function outside your component, called a “reducer”.

- Reducers are a different way to handle state. You can migrate from useState to useReducer in three steps:

1. Move from setting state to dispatching actions.
2. Write a reducer function.
3. Use the reducer from your component.

**Step 1: Move from setting state to dispatching actions**

- In managing state with reducers instead of telling React “what to do” by setting state, you specify “what the user just did” by dispatching “actions” from your event handlers. So instead of “setting tasks” via an event handler, you’re dispatching an “added/changed/deleted a task” action. for ex. 
The object you pass to dispatch is called an “action”. By convention, it is common to give it a string ```type``` that describes what happened.  The type is specific to a component, so in this example either ```added``` or ```added_task``` would be fine.

```
function handleAddTask(text) {
  // "action" object:
  dispatch({
    type: 'added', //'what_happened'
    id: nextId++,
    text: text,
  });
}
```
**Step 2: Write a reducer function**

A reducer function is where you will put your state logic. It takes two arguments, the **current state** and the **action object**, and it returns the next state. React will set the state to what you return from the reducer.

for ex.
```
function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {...});
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```
**Step 3: Use the reducer from your component** 

Finally, you need to hook up the tasksReducer to your component.Import ```useReducer``` hook and replace with ```useState```. for ex.

```
const [tasks, setTasks] = useState(initialTasks); //from
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks); //to
```
The useReducer Hook takes two arguments:

1. A reducer function
2. An initial state

And it returns:

1. A stateful value
2. A dispatch function (to “dispatch” user actions to the reducer)

Component logic can be easier to read when you separate concerns like this.

**Comparing useState and useReducer**

Code size: ```useState``` has less boiler plate code than ```useReducer```.  However, ```useReducer``` can help cut down on the code if many event handlers modify state in a similar way.

Readability: when state updates are simple ```useState``` is readable.When they get more complex, they can bloat your component’s code and make it difficult to scan. In that case ```useReducer``` lets you cleanly separate the how of update logic from the what happened of event handlers.

Debugging: When you have a bug with useState, it can be difficult to tell where the state was set incorrectly, and why. With useReducer, you can add a console log into your reducer to see every state update, and why it happened (due to which action). If each action is correct, you’ll know that the mistake is in the reducer logic itself. However, you have to step through more code than with useState.

Testing: A reducer is a pure function that doesn’t depend on your component. This means that you can export and test it separately in isolation. While generally it’s best to test components in a more realistic environment, for complex state update logic it can be useful to assert that your reducer returns a particular state for a particular initial state and action.

Personal preference: Some people like reducers, others don’t. That’s okay. It’s a matter of preference. You can always convert between useState and useReducer back and forth: they are equivalent!

We recommend using a reducer if you often encounter bugs due to incorrect state updates in some component, and want to introduce more structure to its code. In General when you component has a lot of event handlers and complex state updates it's good to separate concerns and use reducer. So for simple compoennts it's good to stick to ```useState``` but when things get's complex ```useReducer```. Feel free to mix and match!

**Writing reducers well**

Two tips

1. Reducers must be pure: Similar to state updater functions, reducers run during rendering! (Actions are queued until the next render.) so no, send requests, schedule timeouts, or perform any side effects (operations that impact things outside the component). 
2. Each action describes a single user interaction, even if that leads to multiple changes in the data. For example, if a user presses “Reset” on a form with five fields managed by a reducer, it makes more sense to dispatch one reset_form action rather than five separate set_field actions.

**Writing concise reducers with Immer**
Use Immer if you want to write reducers in a mutating style.

Solve Challenges for Practice: https://beta.reactjs.org/learn/extracting-state-logic-into-a-reducer

## 6. Passing Data Deeply with Context

**The problem with passing prop**

- Usually, you will pass information from a parent component to a child component via props. But passing props can become verbose and inconvenient if you have to pass them through many components in the middle, or if many components in your app need the same information. Context lets the parent component make some information available to any component in the tree below it—no matter how **deep-without passing it explicitly through props**.

- We can **lift state** high by removing the nearest common ancestor but that will lead to a situation sometimes called “prop drilling”.

**Context: an alternative to passing props**

Context lets a parent component provide data to the entire tree below it without passing props. Passing data through context invloves 3 steps. Which are.

1. **Create a context**.Create conetext in a separate file and export it so that your components can use it. The only argument to createContext is the default value, can be an object also for ex. 

```
import { createContext } from 'react';
export const LevelContext = createContext(1); //
```

2.**Use that context** from the component that needs the data. (```Heading``` will use ```LevelContext```.)
Import the useContext Hook from React and your context (```LevelContext```). for ex.

```
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';
export default function Heading({ children }) {
  const level = useContext(LevelContext);
  // ...
}
```
```useContext``` is a Hook. Just like ```useState``` and ```useReducer```, you can only call a Hook at the top level of a React component. ```useContext``` tells React that the ```Heading``` component wants to read the ```LevelContext```. Remove props from
```Heading``` component and add it just to Parent componet ```Section``` in this case. for ex.

```
<Section level={4}>
  <Heading>Sub-sub-heading</Heading>
  <Heading>Sub-sub-heading</Heading>
  <Heading>Sub-sub-heading</Heading>
</Section>
```
Until here the data is not passed because even though you’re using the context, you have not provided it yet. React doesn’t know where to get it!. Currently using default value (1). Which leads to last step i.e.

3. **Provide that context** from the component that specifies the data. (```Section``` will provide ```LevelContext```.)
Wrap ```Section``` with a context provider to provide the LevelContext to them. for ex.

```
- App.js (to give overview)

    <Section level={1}>
      <Heading>Title</Heading>
      <Section level={2}>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Section level={3}> 
        //...

- Section.js (uses LevelContext.Provider)

import { LevelContext } from './LevelContext.js';

export default function Section({ level, children }) {
  return (
    <section className="section">
      <LevelContext.Provider value={level}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}
```
This tells React: “if any component inside this ```<Section>``` asks for ```LevelContext```, give them this level.” The component will use the value of the nearest ```<LevelContext.Provider>``` in the UI tree above it.

**Using and providing context from the same component**

- Since context lets you read information from a component above. ```useContext``` hook returns the information. So in our example
each ```Section``` could read the level from the ```Section``` above, and pass level + 1 down automatically. for ex.

```
export default function Section({ children }) {
  const level = useContext(LevelContext); ->here we get the value from the component above
  return (
    <section className="section">
      <LevelContext.Provider value={level + 1}>
        {children}
      </LevelContext.Provider>
    </section>
  );
```
**Context passes through intermediate components**

- see ex. https://beta.reactjs.org/learn/passing-data-deeply-with-context (under this heading)
- Context lets you write components that “adapt to their surroundings” and display themselves differently depending on where (or, in other words, in which context) they are being rendered.

- How context works might remind you of **CSS property inheritance**. In CSS, you can specify ```color: blue``` for a ```<div>```, and any DOM node inside of it, no matter how deep, will inherit that color unless some other DOM node in the middle overrides it with ```color: green```. Similarly, in React, the only way to override some context coming from above is to "wrap children into a context provider with a different value".

- In CSS, different properties like color and background-color don’t override each other. You can set all  <div>’s color to red without impacting background-color. Similarly, different React contexts don’t override each other. Each context that you make with createContext() is **completely separate from other ones**, and ties together components using and providing that particular context. One component may use or provide many different contexts without a problem.

**Before you use context**

 - Just because you need to pass some props several levels deep doesn’t mean you should put that information into context.

 Here’s a few alternatives you should consider before using context:

 1. **Start by passing props**. If your components are not trivial, it’s not unusual to pass a dozen props down through a dozen components. It may feel like a slog, but it makes it very clear which components use which data! The person maintaining your code will be glad you’ve made the data flow explicit with props.

 2. Extract components and pass JSX as children to them. If you pass some data through many layers of intermediate components that don’t use that data (and only pass it further down), this often means that you forgot to extract some components along the way. For example, maybe you pass data props like posts to visual components that don’t use them directly, like 
 ```<Layout posts={posts} />.``` Instead, make Layout take children as a prop, and render 
 ```<Layout><Posts posts={posts} /></Layout>```. This reduces the number of layers between the component specifying the data and the one that needs it.

 If this does not work useContext

**Use cases for context**

- **Theming:** If your app lets the user change its appearance (e.g. dark mode), you can put a context provider at the top of your app, and use that context in components that need to adjust their visual look.

- **Current account**: Many components might need to know the currently logged in user. Putting it in context makes it convenient to read it anywhere in the tree. Some apps also let you operate multiple accounts at the same time (e.g. to leave a comment as a different user). In those cases, it can be convenient to wrap a part of the UI into a nested provider with a different current account value.

- **Routing:** Most routing solutions use context internally to hold the current route. This is how every link “knows” whether it’s active or not. If you build your own router, you might want to do it too.

- **Managing state:** As your app grows, you might end up with a lot of state closer to the top of your app. Many distant components below may want to change it. It is common to use a reducer together with context to manage complex state and pass it down to distant components without too much hassle.

- Context is not limited to static values. If you pass a different value on the next render, React will update all the components reading it below! This is why context is often used in combination with state.

- In general, if some information is needed by distant components in different parts of the tree, it’s a good indication that context will help you.

- Context passes through any components in the middle.
- Before you use context, try passing props or passing JSX as children.

Solve Challenge for Practice: https://beta.reactjs.org/learn/passing-data-deeply-with-context


## 7. Scaling Up with Reducer and Context

- Remember use case of Reducer is different from Context. Reducer is used to make **state updates** (consolidate) more structured and easy to maitain and test when state updates get's complex. While Context is used to remove "Prop Drilling" i.e. pass information deep down to other components.

- A reducer helps keep the event handlers short and concise. However, as your app grows, you might run into another difficulty. Currently, the tasks state and the dispatch function are only available in the top-level ```TaskApp``` component. To let other components read the list of tasks or change it, you have to explicitly pass down the **current state** and the **event handlers** that change it as props. for ex.

```
<TaskList
  tasks={tasks}
  onChangeTask={handleChangeTask}
  onDeleteTask={handleDeleteTask}
/>
```
And ```TaskList``` passes the event handlers to ```Task```:

```
<Task
  task={task}
  onChange={onChangeTask}
  onDelete={onDeleteTask}
/>
```

- In a small example like this, this works well, but if you have tens or hundreds of components in the middle, passing down all state and functions can be quite frustrating!

- This is why, as an alternative to passing them through props, you might want to put both the tasks state and the dispatch function into context. This way, any component below TaskApp in the tree can **read the tasks and dispatch actions without the repetitive “prop drilling”**.

- Here is how you can combine a reducer with context:

**Step 1: Create the context**

- The useReducer Hook returns the current tasks and the dispatch function that lets you update them:
```const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);```

- To pass them down the tree, you will create two separate contexts: ```TasksContext``` provides the current list of tasks.
```TasksDispatchContext``` provides the function that lets components dispatch actions.

```
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```
Giving null by deault here.

**Step 2: Put state and dispatch into context**

- After creating context now we need to provide it. You can import both contexts in your ```TaskApp``` component. Take the ```tasks``` and ```dispatch``` returned by ```useReducer()``` and provide them to the entire tree below:

```
export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks); //reducer 
  // ...
  return (

    <TasksContext.Provider value={tasks}> //this is the context provider here
      <TasksDispatchContext.Provider value={dispatch}> //this is the context provider here
        ...
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```
Since there two contexts created, so it needes two ```Provider```

**Step 3: Use context anywhere in the tree**

- Now you don’t need to pass the list of ```tasks``` or the **event handlers** down the tree. 

```
<TasksContext.Provider value={tasks}>
  <TasksDispatchContext.Provider value={dispatch}>
    <h1>Day off in Kyoto</h1>
    <AddTask /> //removed both the prop and eventhandler from here
    <TaskList />
  </TasksDispatchContext.Provider>
</TasksContext.Provider>
```

Instead, any component that needs the task list can read it from the ```TaskContext```. like:
``` const tasks = useContext(TasksContext);```


```
//...
const dispatch = useContext(TasksDispatchContext); // here we get dispatch function
//...
return (
    // ...
    <button onClick={() => {
      setText('');
      dispatch({
        type: 'added',
        id: nextId++,
        text: text,
      });
    }}>Add</button>
    // ...
```

- The ```TaskApp``` component does not pass any event handlers down, and the TaskList does not pass any event handlers to the Task component either. Each component reads the context that it needs.

- The state still “lives” in the top-level TaskApp component, managed with useReducer. But its tasks and dispatch are now available to every component below in the tree by importing and using these contexts.

**Moving all wiring into a single file**

- The below structure is very important for "maintainance" when your app "scales up". This helps declutter (clean) the components by moving both reducer and context into a single file.

-  You’ll move the reducer into that same file. Then you’ll declare a new ```TasksProvider``` **component** in the same file. This component will tie all the pieces together. It does three things.
1. It will manage the state with a reducer.
2. It will provide both contexts to components below.
3. It will take children as a prop so you can pass JSX to it.
```
export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks); //first 

  return (
    <TasksContext.Provider value={tasks}> //second 
      <TasksDispatchContext.Provider value={dispatch}>
        {children} //third
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```
- This removes all the complexity and wiring from your ``TaskApp`` component. Which becomes clean.
```
//... imports
export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}
```
- You can also export functions that use the context from TasksContext.js:

```
export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

```
- When a component needs to read context, it can do it through these functions:

```
const tasks = useTasks();
const dispatch = useTasksDispatch();
```

- This doesn’t change the behavior in any way, but it lets you later split these contexts further or add some logic to these functions. Now all of the context and reducer wiring is in ```TasksContext.js```. This keeps the **components clean and uncluttered, focused** on what they display rather than where they get the data:

- ``TasksContext.js`` contains reducers, contexts, TasksProvider (component + exported), and hooks we created i.e. useTasks and useTasksDispatch (exported)

- You can think of ```TasksProvider``` component as a part of the screen that knows how to deal with tasks, ```useTasks``` as a way to read them, and ```useTasksDispatch``` as a way to update them from any component below in the tree.

- Functions like ```useTasks``` and ```useTasksDispatch``` are called **Custom Hooks**. Your function is considered a custom Hook if its name starts with use (it's a convention). This lets you use other Hooks, like useContext, inside it.

- As your app grows, you may have many **context-reducer pairs** like this. This is a powerful way to scale your app and lift state up without too much work whenever you want to access the data deep in the tree.

- So to summarise cleaning in components is done via ```TasksProvider``` (that provides context) and  custom Hooks like ```useTasks``` and ```useTasksDispatch``` to read it.

No Challenge but good to see example: https://beta.reactjs.org/learn/scaling-up-with-reducer-and-context

### ESCAPE HATCHES

- Some of your components may need to control and synchronize with systems outside of React. For example, you might need to focus an input using the browser API, play and pause a video player implemented without React, or connect and listen to messages from a remote server. In this chapter, you’ll learn the escape hatches (devise/invent a plan) that let you **“step outside” React and connect to external systems**. Most of your application logic and data flow should not rely on these features.

## 1. Referencing Values with Refs

- When you want a component to “remember” some information, but you don’t want that information to trigger **new renders**, you can use a ref. meaning storing without state (which causes re-renders)

**Adding a ref to your component**

- After importing inside your component, call the ```useRef``` Hook and pass the initial value that you want to reference as the only argument. ```const ref = useRef(0);``` It return like this ``` { current: 0 // The value you passed to useRef }```

- You can access the current value of that ref through the ```ref.current``` property. This value is intentionally mutable, meaning you can both read and write to it. It’s like a **secret pocket of your component** that React doesn’t track. (This is what makes it an “escape hatch” from React’s one-way data flow.)

- Just like state ref can point to anything (string,obj,arr, function etc). It's a plain JavaScript object with the current property that you can read and modify.

- Note that the **component doesn’t re-render with every increment**. Like state, refs are retained by React between re-renders. However, setting state re-renders a component. Changing a ref does not!

- When a piece of information is used for rendering, keep it in state. When a piece of information is only needed by event handlers and changing it doesn’t require a re-render, using a ref may be more efficient.

**Example: building a stopwatch**

- You can combine refs and state in a single component. For example, let’s make a stopwatch that the user can start or stop by pressing a button. In order to display how much time has passed since the user pressed “Start”, you will need to keep track of when the Start button was pressed and what the current time is. This information is used for rendering, so you’ll keep it in state:

```
const [startTime, setStartTime] = useState(null);
const [now, setNow] = useState(null);
```

- When the “Stop” button is pressed, you need to cancel the existing interval so that it stops updating the now state variable. You can do this by calling clearInterval, but you need to give it the interval ID that was previously returned by the setInterval call when the user pressed Start. You need to keep the interval ID somewhere. Since the interval ID is not used for rendering, you can keep it in a ref:

```
default function Stopwatch(){
  ..// state
  const intervalRef = useRef(null);

  function handleStart() {
    setStartTime(Date.now());
    setNow(Date.now());

    clearInterval(intervalRef.current);
    intervalRef.current = setInterval(() => {
      setNow(Date.now());
    }, 10);
  }

  function handleStop() {
    clearInterval(intervalRef.current);
  }
}

```
- When a piece of information is used for rendering, keep it in state. When a piece of information is only needed by event handlers and changing it doesn’t require a re-render, using a ref may be more efficient.

**Differences between refs and state**

- In most cases, you’ll want to use state. Refs are an “escape hatch” you won’t need often. Here’s how state and refs compare:

ref: ```useRef(initialValue)``` returns ```{ current: initialValue }```
state: ```useState(initialValue)``` returns the current value of a state variable and a state setter function ```( [value, setValue])```

ref: Doesn’t trigger re-render when you change it.
state: Triggers re-render when you change it.

ref: Mutable—you can modify and update current’s value outside of the rendering process.
state: “Immutable”—you must use the state setting function to modify state variables to queue a re-render.

ref: You shouldn’t read (or write) the current value during rendering.
state: You can read state at any time. However, each render has its own snapshot of state which does not change.

```
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }
```
Because the **count value is displayed**, it makes sense to use a state value for it. When the counter’s value is set with setCount(), React re-renders the component and **the screen updates to reflect** the new count.

If you tried to implement this with a ref, React would never re-render the component, so you’d never see the count change! See how clicking this button does not update its text:

```
  let countRef = useRef(0);

  function handleClick() {
    // This doesn't re-render the component!
    countRef.current = countRef.current + 1;
  }
```

This is why reading ```ref.current``` during render leads to unreliable code.

**When to use refs ?**

Typically, you will use a ref when your component needs to “step outside” React and communicate with external APIs—often a browser API that won’t **impact the appearance of the component**. Here are a few of these rare situations:

- Storing timeout IDs
- Storing and manipulating DOM elements. (as in angular)
- Storing other objects that aren’t necessary to calculate the JSX.

If your component needs to store some value, but it **doesn’t impact the rendering logic**, choose refs.

**Best practices for refs**

Following these principles will make your components more predictable:

- **Treat refs as an escape hatch**. Refs are useful when you work with external systems or browser APIs. If much of your application logic and data flow relies on refs, you might want to rethink your approach.

- **Don’t read or write ```ref.current``` during rendering**. If some information is needed during rendering, use state instead. Since React doesn’t know when ref.current changes, even reading it while rendering makes your component’s behavior difficult to predict. (The only exception to this is code like ```if (!ref.current) ref.current = new Thing()``` which only sets the ref once during the first render.)

- This is because the **ref itself is a regular JavaScript object**, and so it behaves like one.

**Refs and the DOM** 

- You can point a ref to any value. However, the most common use case for a ```ref``` is to access a DOM element. For example, this is handy if you want to focus an input programmatically. When you pass a ```ref``` to a ```ref``` attribute in JSX, like 
```<div ref={myRef}>```, React will put the corresponding DOM element into ```myRef.current```. You can read more about this in Manipulating the DOM with Refs.

- Remember ref and ref attribute are different.
- Value stored in refs is preserved during re-renders.

 https://beta.reactjs.org/learn/referencing-values-with-refs

## 2. Manipulating the DOM with Refs

- React automatically updates the DOM to match your render output, so your components won’t often need to manipulate it. However, sometimes you might need access to the DOM elements managed by React—for example, **to focus a node, scroll to it, or measure its size and position**. There is no built-in way to do those things in React, so you will need a ref to the DOM node.

**Getting a ref to the node** 

To access a DOM node managed by React, do following after importing and declaring ```useRef```, pass it to the DOM node as the ref attribute ```<div ref={myRef}> ```

- The useRef Hook returns an object with a single property called ```current```. Initially, ```myRef.current``` will be null. When React creates a DOM node for this ```<div>```, React will put a reference to this node into ```myRef.current```. You can then access this DOM node from your event handlers and **use the built-in browser APIs** defined on it. for ex.
```myRef.current.scrollIntoView(); // // You can use any browser APIs```

Focus text input example

```
//...
  function handleClick() {
    inputRef.current.focus();
  }
  return (
    <input ref={inputRef} />
    <button onClick={handleClick}>
    //...
  )
```
- While DOM manipulation is the most common use case for refs, the ```useRef``` Hook can be used for storing other things outside React, like timer IDs. Similarly to state, refs remain between renders. **Refs are like state variables that don’t trigger re-renders** when you set them.

Example: Scrolling to an element 

```
//...
  function handleScrollToSecondCat() {
    secondCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

```
**How to manage a list of refs using a ref callback (deep dive)**

Sometimes you might need a ref to each item in the list, and you don’t know how many you will have.

1. One possible way around this is to get a single ref to their parent element, and then use DOM manipulation methods like ```querySelectorAll``` to “find” the individual child nodes from it. However, this is brittle and can break if your DOM structure changes.

2. Another solution is to **pass a function to the ref attribute**. This is called a **ref callback**. React will call your ref callback with the DOM node when it’s time to set the ref, and with null when it’s time to clear it. This lets you maintain your own array or a Map, and access any ref by its index or some kind of ID.
This example shows how you can use this approach to scroll to an arbitrary node in a long list. 

```
  function scrollToId(itemId) {
      const map = getMap();
      const node = map.get(itemId);
      node.scrollIntoView(//..);
  }
  function getMap() {
    if (!itemsRef.current) {
      // Initialize the Map on first usage.
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }
//...
<ul>
          {catList.map(cat => (
            <li
              key={cat.id}
              //ref callback
              ref={(node) => {
                const map = getMap();
                if (node) {
                  map.set(cat.id, node);
                } else {
                  map.delete(cat.id);
                }
              }}
            >
 //...           
```

- In this example, itemsRef doesn’t hold a single DOM node. Instead, it holds a Map from item ID to a DOM node.
The ref callback on every list item takes care to update the Map.

**Accessing another component’s DOM nodes (forward-refs)**

- When you put a ref on a built-in component that outputs a browser element like ```<input />```, React will set that ref’s current property to the corresponding DOM node (such as the actual``` <input />``` in the browser).

However, if you try to put a ref on your own component, like ```<MyInput />```, by default you will get null. Here is an example demonstrating it. Notice how clicking the button does not focus the input.
```

return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
```
To help you notice the issue, React also prints an error to the console
``` Warning: Function components cannot be given refs. Attempts to access this ref will fail. Did you mean to use React.forwardRef()?```

- This happens because by default React does not let a component access the DOM nodes of other components. Not even for its own children! This is intentional. Refs are an escape hatch that should be used sparingly. Manually manipulating another component’s DOM nodes makes your code even more fragile.

- Instead, components that want to expose their DOM nodes have to **opt in to that behavior**. A component can specify that it **“forwards” its ref** to one of its children. Here’s how MyInput can use the forwardRef API.

```
const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});
```
This is how it works:

1. ```<MyInput ref={inputRef} />``` tells React to put the corresponding DOM node into ```inputRef.current```. However, it’s up to the ```MyInput``` component to **opt into that—by default, it doesn’t**.
2. The ```MyInput``` component is declared using ```forwardRef```. **This opts it into receiving the ```inputRef``` from above as the second ```ref``` argument which is declared after ```props```**.
3. ```MyInput``` itself passes the ```ref``` it received to the <input> inside of it.

```
import { forwardRef, useRef } from 'react';

const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }
  return (
      <MyInput ref={inputRef} />
      <button onClick={handleClick}> 
      //...
    )
```

- In design systems, it is a common pattern for low-level components like buttons, inputs, and so on, to forward their refs to their DOM nodes. On the other hand, high-level components like forms, lists, or page sections usually won’t expose their DOM nodes to avoid accidental dependencies on the DOM structure.

**Exposing a subset of the API with an imperative handle (deep dive)**

In uncommon cases, you may want to restrict the exposed functionality, provided by ```forwardRef``` to the parent component. For ex: ```MyInput``` want to restrict it's functionality (let's say only expose focus), which can be done with ```useImperativeHandle```

```
import from {...} from 'react';

const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    // Only expose focus and nothing else
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input {...props} ref={realInputRef} />;
});
```

- Here, ```realInputRef``` inside ```MyInput``` holds the actual input DOM node. However, ```useImperativeHandle``` instructs React to provide your own special object as the value of a ref to the parent component. So ```inputRef.current``` inside the Form component will only have the focus method. In this case, the ref “handle” **is not the DOM node, but the custom object** you create inside ```useImperativeHandle``` call.

**When React attaches the refs**

In React, every update is split in two phases:

- During **render**, React calls your components to figure out what should be on the screen.
- During **commit**, React applies changes to the DOM.

- In general, you don’t want to access refs during rendering. That goes for refs holding DOM nodes as well. During the first render, the DOM nodes have not yet been created, so ```ref.current``` will be null. And during the rendering of updates, the DOM nodes haven’t been updated yet. So it’s too early to read them.

- React sets ```ref.current``` during the commit. Before updating the DOM, React sets the affected ```ref.current``` values to null. After updating the DOM, React immediately sets them to the corresponding DOM nodes.

- **Usually, you will access refs from event handlers**. If you want to do something with a ref, but there is no particular event to do it in, you might need an **Effect**. We will discuss effects on the next pages.

**Flushing state updates synchronously with flushSync (deep dive)**

- Consider code like this, which adds a new todo and scrolls the screen down to the last child of the list. Notice how, for some reason, it always scrolls to the todo that was just before the last added one. 
The issue is with these two lines:

```
setTodos([ ...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```
- In React, state updates are queued. Usually, this is what you want. However, here it causes a problem because ```setTodos``` does not immediately update the DOM. So the time you scroll the list to its last element (using ref), the ```todo``` has not yet been added. This is why scrolling always “lags behind” by one item.

- To fix this issue, you can force React to update (“flush”) the DOM **synchronously**. To do this, import ```flushSync``` from ```react-dom``` and wrap the state update into a ```flushSync``` call.

```
  flushSync(() => {
    setTodos([ ...todos, newTodo]);
  });
  listRef.current.lastChild.scrollIntoView();
```

- This will instruct ```React``` to update the DOM synchronously right after the code wrapped in ```flushSync``` executes. As a result, the last todo will already be in the DOM by the time you try to scroll to it.

**Best practices for DOM manipulation with refs**

- Refs are an escape hatch. You should only use them when you have to “step outside React”. Common examples of this include managing focus, scroll position, or calling browser APIs that React does not expose.

- If you stick to non-destructive actions like focusing and scrolling, you shouldn’t encounter any problems. However, if you try to modify the DOM manually, you can risk conflicting with the changes React is making.

```
  //...
  const [show, setShow] = useState(true);
  const ref = useRef(null);
  return(
      <div>
        <button onClick={() => { setShow(!show); }}>
          Toggle with setState
        </button>
        <button onClick={() => { ref.current.remove(); }}>
          Remove from the DOM
        </button>
        {show && <p ref={ref}>Hello world</p>}
      </div>
  )   
```

- After you’ve manually removed the DOM element, trying to use ```setState``` to show it again will lead to a crash. This is because you’ve changed the DOM, and React doesn’t know how to continue managing it correctly.

- **Avoid changing DOM nodes managed by React**. Modifying, adding children to, or removing children from elements that are managed by React can lead to inconsistent visual results or crashes like above.

- However, this doesn’t mean that you can’t do it at all. It requires caution. You can safely modify parts of the DOM that React has no reason to update. For example, if some ```<div>``` is always empty in the JSX, React won’t have a reason to touch its children list. Therefore, it is safe to manually add or remove elements there.

[Challenge For Practice](https://beta.reactjs.org/learn/manipulating-the-dom-with-refs)

## 3. Synchronizing with Effects

- Some components need to synchronize with external systems. For example, you might want to control a non-React component based on the React state, set up a server connection, or send an analytics log when a component appears on the screen. **Effects let you run some code after rendering** so that you can synchronize your component with some system outside of React.

**What are Effects and how are they different from events?**

 There's two types of logic inside React components:

1. **Rendering code** lives at the top level of your component. This is where you take the props and state, transform them, and return the JSX you want to see on the screen. Rendering code must be pure. Like a math formula, it should only calculate the result, but not do anything else.

2. **Event handlers**  are nested functions inside your components that do things rather than just calculate them. An event handler might update an input field, submit an HTTP POST request to buy a product etc. Event handlers contain “side effects” (they change the program’s state) and are caused by a specific user action (for example, a button click or typing).

- Sometimes these above to concepts aren't enough. Consider a ```ChatRoom``` component that must connect to the chat server whenever it’s visible on the screen. 
Connecting to a server is not a pure calculation (it’s a side effect) so it can’t happen during rendering. (can't apply Rendering code logic)
Also, there is no single particular event like a click that causes ChatRoom to be displayed. (can't apply Event handlers logic)

- **Effects let you specify side effects that are caused by rendering itself, rather than by a particular event**. Sending a message in the chat is an event because it is directly caused by the user clicking a specific button. However, setting up a server connection is an *Effect* because it needs to happen regardless of which interaction caused the component to appear. Effects run at the end of the rendering process after the screen updates. This is a good time to synchronize the React components with some external system (like network or a third-party library).

**You might not need an Effect**

- Don’t rush to add Effects to your components. Keep in mind that Effects are typically used to “step out” of your React code and **synchronize with some external system**. This includes browser APIs, third-party widgets, network, and so on. If your Effect only adjusts some state based on other state, you might not need an Effect.


**How to write an Effect ?**

To write an Effect, follow these three steps:

1. **Step 1: Declare an Effect**. By default, your Effect will run after every render.

Import, declare and call it at the top level of your component.

Every time your component renders, React will update the screen and then run the code inside useEffect. In other words, **useEffect “delays” a piece of code from running until that render is reflected on the screen**.

Consider a ```<VideoPlayer>``` React component. It would be nice to control whether it’s playing or paused by passing an ```isPlaying``` prop to it.

However, the browser ```<video>``` tag does not have an ```isPlaying``` prop. The only way to control it is to manually call the play() and pause() methods on the DOM element. You need to synchronize the value of ```isPlaying``` prop, which tells whether the video should currently be playing, with imperative calls like play() and pause().

We’ll need to first get a ref to the ```<video>``` DOM node. You might be tempted to try to call play() or pause() during rendering, but that isn’t correct:

```
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  if (isPlaying) {
    ref.current.play();  // Calling these while rendering isn't allowed.
  } else {
    ref.current.pause(); // Also, this crashes.
  }
//...
```

The reason this code isn’t correct is that it tries to do something with the DOM node during rendering. In React, rendering should be a pure calculation of JSX and should not contain side effects like modifying the DOM.

Moreover, when VideoPlayer is called for the first time, its DOM does not exist yet! There isn’t a DOM node yet to call play() or pause() on, because React doesn’t know what DOM to create until after you return the JSX.

The solution here is to wrap the side effect with useEffect to move it out of the rendering calculation:

```
//...
 useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  });
//...
```

By wrapping the DOM update in an Effect, you let React update the screen first. Then your Effect runs.

In this example, the “external system” you synchronized to React state was the browser media API. You can use a similar approach to wrap legacy non-React code (like jQuery plugins) into declarative React components.

**Pitfall**

By default, Effects run after every render. This is why code like this will produce an infinite loop:

```
const [count, setCount] = useState(0);
useEffect(() => {
  setCount(count + 1);
});
```
Because, set state, (```setCount```) causes re-render, and after rendering ```useEffect``` runs, if we are setting state inside ```useEffect```  this, process will go on. So, effects should usually synchronize your components with an external system. If there’s no external system and you only want to adjust some state based on other state, you might not need an Effect.

2. **Step 2: Specify the Effect dependencies**
 
 - Most Effects should only re-run when needed rather than after every render. For example, a fade-in animation should only trigger when a component appears. Connecting and disconnecting to a chat room should only happen when the component appears and disappears, or when the chat room changes. You can control this by specifying dependencies.

By default, Effects run after every render. Often, this is not what you want:

- Sometimes, it’s slow. Synchronizing with an external system is not always instant, so you might want to skip doing it unless it’s necessary. For example, you don’t want to reconnect to the chat server on every keystroke.
- Sometimes, it’s wrong. For example, you don’t want to trigger a component fade-in animation on every keystroke. The animation should only play once when the component appears for the first time.

Here is the previous example with a few console.log calls and a text input that updates the parent component’s state. After every 
keyword input typing, causes the Effect to re-run. Because state is being set, which causes re-reders.

You can tell React to **skip unnecessarily re-running the Effect** by specifying an array of dependencies as the second argument to the useEffect call. Start by adding an empty [] 

```  
useEffect(() => {
    if (isPlaying) {
      //...
    } else {
      console.log('Calling video.pause()');
      ref.current.pause();
    }
}, []); // This causes an error
```

You should see an error saying ```React Hook useEffect has a missing dependency: 'isPlaying'```.

The problem is that the code inside of your Effect depends on the isPlaying prop to decide what to do, but this dependency was not explicitly declared. To fix this issue, add isPlaying to the dependency array:

```  
useEffect(() => {
    if (isPlaying) { // It's used here...
      // ...
    } else {
      // ...
    }
}, [isPlaying]); // ...so it must be declared here!
```
 Specifying ```[isPlaying]``` as the dependency array tells React that it should skip re-running your Effect if isPlaying is the same as it was during the previous render. 

The dependency array can contain multiple dependencies. React will only skip re-running the Effect if all of the dependencies you specify have exactly the same values as they had during the previous render. React compares the dependency values using the Object.is (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison

Notice that you can’t “choose” your dependencies. You will get a lint error if the dependencies you specified don’t match what React expects based on the code inside your Effect. This helps catch many bugs in your code. If your Effect uses some value but you don’t want to re-run the Effect when it changes, you’ll need to edit the Effect code itself to not “need” that dependency.


**Pitfall**

The behaviors without the dependency array and with an empty [] dependency array are very different.

```
useEffect(() => {
  // This runs after every render
});

useEffect(() => {
  // This runs only on mount (when the component appears)
}, []);

useEffect(() => {
  // This runs on mount *and also* if either a or b have changed since the last render
}, [a, b]);

```

**Why was the ref omitted from the dependency array? (deep dive)**

This Effect uses both ref and isPlaying, but only isPlaying is declared as a dependency:
```
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying]);
```
This is because the ref object has a stable identity: React guarantees you’ll always get the same object from the same useRef call on every render. It never changes, so it will never by itself cause the Effect to re-run. Therefore, it does not matter whether you include it or not. Including it is fine too: ```[isPlaying, ref];```

The set functions returned by useState also have stable identity, so you will often see them omitted from the dependencies too. If the linter lets you omit a dependency without errors, it is safe to do.

Omitting always-stable dependencies only works when the linter can “see” that the object is stable. For example, if ref was passed from a parent component, you would have to specify it in the dependency array. However, this is good because you can’t know whether the parent component always passes the same ref, or passes one of several refs conditionally. So your Effect would depend on which ref is passed.

3. **Add cleanup if needed**. Some Effects need to specify how to stop, undo, or clean up whatever they were doing. For example, “connect” needs “disconnect”, “subscribe” needs “unsubscribe”, and “fetch” needs either “cancel” or “ignore”. You will learn how to do this by returning a cleanup function.
Let’s look at each of these steps in detail.

Consider a different example. You’re writing a ChatRoom component that needs to connect to the chat server when it appears. You are given a createConnection() API that returns an object with connect() and disconnect() methods. How do you keep the component connected while it is displayed to the user?

Start by writing the Effect logic. It would be slow to connect to the chat after every re-render, so you add the dependency array:

```
useEffect(() => {
  const connection = createConnection();
  connection.connect();
}, []); //dependency array 
```

**The code inside the Effect does not use any props or state, so your dependency array is [] (empty). This tells React to only run this code when the component “mounts”, i.e. appears on the screen for the first time.**

See below example:
```
export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
  }, []);
```

- This Effect only runs on mount, so you might expect "✅ Connecting..." to be printed once in the console. However, if you check the console, "✅ Connecting..." gets printed twice. Why does it happen?

- Imagine the ```ChatRoom``` component is a part of a larger app with many different screens. The user starts their journey on the ```ChatRoom``` page. The component mounts and calls connection.connect(). Then imagine the user navigates to another screen—for example, to the Settings page. The ```ChatRoom``` component unmounts. Finally, the user clicks Back and ```ChatRoom``` mounts again. This would set up a second connection—but the first connection was never destroyed! As the user navigates across the app, the connections would keep piling up.

- Bugs like this are easy to miss without extensive manual testing. To help you spot them quickly, in development React remounts every component once immediately after its initial mount. Seeing the "✅ Connecting..." log twice helps you notice the real issue: your code doesn’t close the connection when the component unmounts.

To fix the issue, return a cleanup function from your Effect:

```
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []);
  ```
React will call your cleanup function each time before the Effect runs again, and one final time when the component unmounts (gets removed).

Now you get three console logs in development: "✅ Connecting... "❌ Disconnected." "✅ Connecting..."
This is the correct behavior in development.By remounting your component, React verifies that navigating away and back would not break your code.React is probing your code for bugs in development. This is normal and you shouldn’t try to make it go away.

In production, you would only see "✅ Connecting..." printed once. Remounting components only happens in development to help you find Effects that need cleanup. You can turn off **Strict Mode** to opt out of the development behavior, but we recommend keeping it on. This lets you find many bugs like the one above.


**how to fix my Effect so that it works after remounting?**

Usually, the answer is to implement the cleanup function.  The cleanup function should stop or undo whatever the Effect was doing. The rule of thumb is that the user shouldn’t be able to distinguish between the Effect running once (as in production) and a setup → cleanup → setup sequence (as you’d see in development).

Most of the Effects you’ll write, will fit into one of the common patterns below

**Controlling non-React widgets**

Sometimes you need to add UI widgets that aren’t written to React. For example, let’s say you’re adding a map component to your page. It has a setZoomLevel() method, and you’d like to keep the zoom level in sync with a zoomLevel state variable in your React code. Your Effect would look like similar to this:

```
useEffect(() => {
  const map = mapRef.current;
  map.setZoomLevel(zoomLevel);
}, [zoomLevel]);
```
Note that there is no cleanup needed in this case. In development, React will call the Effect twice, but this is not a problem.

Some APIs may not allow you to call them twice in a row. For example, the ```showModal``` method of the built-in ```<dialog>``` element throws if you call it twice. Implement the cleanup function and make it close the dialog:

```
useEffect(() => {
  const dialog = dialogRef.current;
  dialog.showModal();
  return () => dialog.close();
}, []);
```
In development, your Effect will call ```showModal()```, then immediately close(), and then ```showModal()``` again. This has the same user-visible behavior as calling ```showModal()``` once, as you would see in production.

**Subscribing to events**

If your Effect subscribes to something, the cleanup function should unsubscribe:

```
useEffect(() => {
  function handleScroll(e) {
    console.log(e.clientX, e.clientY);
  }
  window.addEventListener('scroll', handleScroll);
  return () => window.removeEventListener('scroll', handleScroll);
}, []);
```
In development, your Effect will call ```addEventListener()```, then immediately ```removeEventListener()```, and then ```addEventListener()``` again with the same handler. So there would be only one active subscription at a time. This has the same user-visible behavior as calling ```addEventListener()``` once, as you would see in production.

**Triggering animations**

If your Effect animates something in, the cleanup function should reset the animation to the initial values:

```
useEffect(() => {
  const node = ref.current;
  node.style.opacity = 1; // Trigger the animation
  return () => {
    node.style.opacity = 0; // Reset to the initial value
  };
}, []);
```
In development, opacity will be set to 1, then to 0, and then to 1 again. This should have the same user-visible behavior as setting it to 1 directly, which is what would happen in production. If you use a third-party animation library with support for tweening, your cleanup function should reset the tween’s timeline to its initial state.

**Fetching data**

If your Effect fetches something, the cleanup function should either abort the fetch or ignore its result:

```
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```
You can’t “undo” a network request that already happened, but your cleanup function should ensure that the fetch that’s not relevant anymore does not keep affecting your application. For example, if the userId changes from 'Alice' to 'Bob', cleanup ensures that the 'Alice' response is ignored even if it arrives after 'Bob'.

**In development, you will see two fetches in the Network tab.** There is nothing wrong with that. With the approach above, the first Effect will immediately get cleaned up so its copy of the ignore variable will be set to true. So even though there is an extra request, it won’t affect the state thanks to the if (!ignore) check.

**In production, there will only be one request**. If the second request in development is bothering you, the best approach is to use a solution that **deduplicates requests and caches their responses** between components:

```
function TodoList() {
  const todos = useSomeDataLibrary(`/api/user/${userId}/todos`);
  // ...
  ```
This will not only improve the development experience, but also make your application feel faster. For example, the user pressing the Back button won’t have to wait for some data to load again because it will be cached. You can either build such a cache yourself or use one of the many existing alternatives to manual fetching in Effects.

**What are good alternatives to data fetching in Effects? (deep dive)**

Writing fetch calls inside Effects is a popular way to fetch data, especially in fully client-side apps. This is, however, a very manual approach and it has significant downsides:

**Effects don’t run on the server**. This means that the initial server-rendered HTML will only include a loading state with no data. The client computer will have to download all JavaScript and render your app only to discover that now it needs to load the data. This is not very efficient.
**Fetching directly in Effects makes it easy to create “network waterfalls”**. You render the parent component, it fetches some data, renders the child components, and then they start fetching their data. If the network is not very fast, this is significantly slower than fetching all data in parallel.
**Fetching directly in Effects usually means you don’t preload or cache data.** For example, if the component unmounts and then mounts again, it would have to fetch the data again.
**It’s not very ergonomic.** There’s quite a bit of boilerplate code involved when writing fetch calls in a way that doesn’t suffer from bugs like race conditions.

This list of downsides is not specific to React. It applies to fetching data on mount with any library. Like with routing, data fetching is not trivial to do well, so we recommend the following approaches:

- If you use a framework, use its built-in data fetching mechanism. Modern React frameworks have integrated data fetching mechanisms that are efficient and don’t suffer from the above pitfalls.

- Otherwise, consider using or building a client-side cache. Popular open source solutions include 'React Query', 'useSWR', and 'React Router 6.4+'. You can build your own solution too, in which case you would use Effects under the hood but also add logic for deduplicating requests, caching responses, and avoiding network waterfalls (by preloading data or hoisting data requirements to routes).

You can continue fetching data directly in Effects if neither of these approaches suit you.

**Sending analytics**
Consider this code that sends an analytics event on the page visit:

```
useEffect(() => {
  logVisit(url); // Sends a POST request
}, [url]);
```
In development, ```logVisit``` will be called twice for every URL. We recommend to keep this code as is. Like with earlier examples, there is no user-visible behavior difference between running it once and running it twice. From a practical point of view, ```logVisit``` should not do anything in development because you don’t want the logs from the development machines to skew the production metrics. Your component remounts every time you save its file, so it would send extra visits during development anyway.

In production, there will be no duplicate visit logs.

To debug the analytics events you’re sending, you can deploy your app to a staging environment (which runs in production mode) or temporarily opt out of Strict Mode and its development-only remounting checks. You may also send analytics from the route change event handlers instead of Effects. For even more precise analytics, 
[intersection observers](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) can help track which components are in the viewport and how long they remain visible.

**Not an Effect: Initializing the application** 
Some logic should only run once when the application starts. You can put it outside your components:

```
if (typeof window !== 'undefined') { // Check if we're running in the browser.
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```
This guarantees that such logic only runs once after the browser loads the page.

**Not an Effect: Buying a product**

In Short: 

- Should be done in evernt handlers.(Buy click button pressed and ppost requst sent)

Explanation:

Sometimes, even if you write a cleanup function, there’s no way to prevent user-visible consequences of running the Effect twice. For example, maybe your Effect sends a POST request like buying a product:

```
useEffect(() => {
  // 🔴 Wrong: This Effect fires twice in development, exposing a problem in the code.
  fetch('/api/buy', { method: 'POST' });
}, []);
```
You wouldn’t want to buy the product twice. However, this is also why you shouldn’t put this logic in an Effect. What if the user goes to another page and then presses Back? Your Effect would run again. You don’t want to buy the product when the user visits a page; you want to buy it when the user clicks the Buy button.

Buying is not caused by rendering; it’s caused by a specific interaction. It only runs once because the interaction (a click) happens once. **Delete the Effect and move your /api/buy request into the Buy button event handler**:

```  
function handleClick() {
    // ✅ Buying is an event because it is caused by a particular interaction.
    fetch('/api/buy', { method: 'POST' });
  }
```

This illustrates that if remounting breaks the logic of your application, this usually uncovers **existing bugs**. From the user’s perspective, visiting a page shouldn’t be different from visiting it, clicking a link, and then pressing Back. React verifies that your components don’t break this principle by remounting them once in development.

**Putting it all together** 
This playground can help you “get a feel” for how Effects work in practice.

This example uses setTimeout to schedule a console log with the input text to appear three seconds after the Effect runs. The cleanup function cancels the pending timeout. Start by pressing “Mount the component”:

```
function Playground() {
  const [text, setText] = useState('a');
useEffect(() => {
    function onTimeout() {
      console.log('⏰ ' + text);
    }

    console.log('🔵 Schedule "' + text + '" log');
    const timeoutId = setTimeout(onTimeout, 3000);

    return () => {
      console.log('🟡 Cancel "' + text + '" log');
      clearTimeout(timeoutId);
    };
  }, [text]);
}
  export default function App() {
    //...
  return (
      <button onClick={() => setShow(!show)}>
        {show ? 'Unmount' : 'Mount'} the component
      </button>
    <Playground />}  
  //...    
  ```
  You will see three logs at first: ```Schedule "a" log, Cancel "a"``` log, and ```Schedule "a"``` log again. Three second later there will also be a log saying a. As you learned earlier on this page, the extra schedule/cancel pair is because React remounts the component once in development to verify that you’ve implemented cleanup well

  React always cleans up the previous render’s Effect before the next render’s Effect.
  
  Type something into the input and then immediately press “Unmount the component”. Notice how unmounting cleans up the last render’s Effect. In this example, it clears the last timeout before it has a chance to fire.

  Finally, edit the component above and comment out the cleanup function so that the timeouts don’t get cancelled. Try typing abcde fast. What do you expect to happen in three seconds? 

  Three seconds later, you should see a sequence of logs ```(a, ab, abc, abcd, and abcde)``` rather than five abcde logs. Each Effect “captures” the text value from its corresponding render.  It doesn’t matter that the text state changed: an Effect from the render with text = 'ab' will always see 'ab'. In other words, Effects from each render are isolated from each other. If you’re curious how this works, you can read about closures.

- You can’t “choose” your dependencies. They are determined by the code inside the Effect.
- React will call your cleanup function before the Effect runs next time, and during the unmount.

  Good to learn to side topics:
1. closures (used in effect)
2. race conditions bug: happen because two asynchronous operations are “racing” with each other, and they might arrive in an unexpected order.

[Do Challenge for Prac (last one imp)](https://beta.reactjs.org/learn/synchronizing-with-effects)
--------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------

### `React Important Concepts to Remember (old docs)`

### Rendering Elements

- React has a ReactDOM (shadow DOM) which converts JSX element (which are infact JS objects) into real DOM elements. 

- React Elements (JSX elements) and States are immutable (cannot be changed/updated, only assigned new when need to change)

- Becuase of Immutabe behaviour React DOM compares the element and its children to the previous one, and only applies the DOM updates necessary to bring the DOM to the desired state.

### Components and Props

- React function/class components should be pure functions i.e. they should not change the input(props)

- In React data flows top down (unidirectional)

- **props.children** : Anything inside the JSX tag gets passed into the component as a children prop. Since FancyBorder renders {props.children} inside a <div>, the passed elements appear in the final output.

```
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
    </FancyBorder>
    function FancyBorder(props) {
      return (
        <div className={'FancyBorder FancyBorder-' + props.color}>
          {props.children} //this will show Welcome heading
        </div>
      );
    }
```
Sometimes you might need multiple “holes” in a component. In that case send components as prop eg ``` {props.left} ``` for

```
    <SplitPane left={<Contacts />} right={ <Chat />} />
```

Actually there are no limitations on what you can pass as props in React. Remember that components may accept arbitrary props, including primitive values, React elements, or functions.

### State

- Never update state directly because through setState()/useState call, React knows the state has changed, and calls the render() method again. Moreover State Updates are Merged (not in FC) so, through setState  we can independently update single state variable.

- React States and Props updates may be Asynchronous so, don't rely on their prev values to update new state. Use updater func in arg instead of an object example:  

```
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

- Unlike the setState method found in class components, useState does not automatically merge update objects. 
You can replicate this behavior by combining the function updater form with object spread syntax:
If depending upon the prevState use function version of updating states (better apprach to avoid bugs)

```
    setUser((prevState: User) => ({
        ...prevState,
        [name]: value
    }));
```

Else can use simply this syntax

```
    setUser({
      ...user,
      [name]: value
    })
```

Simlarly when updating state twice won't work because state changing renders new for example 

```
  function brokenIncrement() {
    setCount(count + 1);
    setCount(count + 1);
  }

  function increment() {
    setCount(count => count + 1);
    setCount(count => count + 1);
  }
```


### Handling Evenets

- Need to call ``` e.preventDefault(); ``` to prevent default behaviour of the dom elements (like onSubmit method of <form>). Here e is synthetic event

- In JavaScript, class methods are not bound by default. So need to bind class methods in constructor or can use class feilds 
See below example respectively.

```
this.toggleSwitch = this.toggleSwitch.bind(this) (bind method)
```

```
incrementCounter = ()=>{} (class field method)
```

- Passing arguments to the event handlers can be done in two ways. See below example respectively.

*Note : Both these appraoches create different (new) function each time it's render. But there's no way around this when passing argument.*

```
onClick={(e) => this.deleteRow(id, e)} (Problem with this syntax: A different callback is created each time the Button renders )
```

```
onClick={this.deleteRow.bind(this, id)} (Problem with this syntax: The bind also creates new function )
```

### Conditional Rendering

- We can shorter the conditional rendering syntax by replacing 'if' with JS Logical && Operator or using Inline If-Else with Conditional Operator. See below example respectively.

```
{ unreadMessages.length > 0 && 
  <div> You Have {unreadMessages.length} Messages </div> 
} 
(If the expression does not return true/false, the above approach will return the false expression example:
 {count && <h1>Messages: {count}</h1>} in case of false will return <div> 0 </div>
```

``` 
The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
```

### Lists and Keys

- For transforming arrays into lists of elements we uses JS arrays map method in React

- Keys should be given to the elements inside the array to give the elements a stable identity. Keys helps React, identify which items have changed, are added, or are removed

- We generally use ID's inside keys because Keys must be **Unique**. As a last resort we can use indexes. If the order of items may change. This can negatively impact performance and may cause issues with component state (in case of indexes)

- Keys serve as a hint to React but they don’t get passed to your components as a Prop.

### Forms

- React manages the user input (two way data binding) by 'value' prop and 'handleChange' event. 
An input form element whose value is controlled by React in this way i.e. by accepts both a value and an onChange prop is called a **Controlled Component**. 
The  ```<select>``` and ```<textarea>``` tag are very similar to ```<input>``` tag. 

```
<input type="text" value={this.state.value} onChange={this.handleChange} />
```

- Multiple inputs can be handled by using the name attribute and handling them in single handleChange function. Also making use of  ES6 computed property name syntax to update the state key based on the name. (state name and input name prop must be same)

```
    const name = target.name;

    this.setState({
      [name]: value
    });
```


### Lifting States Up

- Often, several components need to reflect the same changing data. We recommend lifting the shared state up to their closest common ancestor. So that we have a single 'source of truth' in every child components (see exaple in code TempratureCalculator);

### Composition and Inheritance

- Simply React does not use Inheritance (theres no need of it).
Props and composition give you all the flexibility you need to customize a component’s look and behavior in an explicit and safe way. Remember that components may accept arbitrary props, including primitive values, React elements, or functions.

- If you want to reuse non-UI functionality between components, we suggest extracting it into a separate JavaScript module. The components may import it and use that function, object, or class, without extending (inheritance) it.

### Thinking in React

- Steps Need to take to build (page, feature)

1. Break The UI (design) Into A Component Hierarchy 
2. Build A Static Version in React
3. Identify The Minimal (but complete) Representation Of UI State
4. Identify Where Your State Should Live
5. Add Inverse Data Flow

- 3 Questions need to think (answer) while creating a state 

1. Is it passed in from a parent via props? If so, it probably isn’t state.
2. Does it remain unchanged over time? If so, it probably isn’t state.
3. Can you compute it based on any other state or props in your component? If so, it isn’t state.

### Strict Mode

- StrictMode is a tool for highlighting potential problems in an application. It activates additional checks and warnings for its descendants. Can be applied on any part of the application. Mostly contain warning of leagcy and deprecated apis and methods for eg, legacy context API,deprecated findDOMNode and also 
Identifying components with unsafe lifecycles
Detecting unexpected side effects
Ensuring reusable state

--------------------------------------------------------------------------------------------------------------------------------

### React Testing

**Testing Libraries Used in React**:

- We can use several libraries to test react components. The most used and recommended are ```Jest``` in combination with 
```React testing libraray (RTL)``` or ```vitest``` which is quite new with vitest projects.

1. ```Jest```: It's test runner 
2. ```React Testing Library```: It helps us interact with the DOM without checking the inner implemenation of the component. Like if we want test what's in the state, we don't need to know what's in it, we can just see what would be the output on the DOM(UI) because that what user cares. 
3. ```jsdom```: A lightweight browser implementation that runs inside Node.js.
4. ```vitest```: It's a test runner similar to Jest. It is easily configureable in vite projects.
5. ```msw```: mock service worker. It is used to mock api requests. Because Vitest runs in Node, web APIs are not available, so we need something that will mimic network behavior for us. We recommend (MSW) to accomplish this. It will let you mock both REST and GraphQL network requests, and is framework agnostic.
Mock Service Worker (MSW) works by intercepting the requests your tests make, allowing you to use it without changing any of your application code. It uses node-request-intercepter for vitest
6. ```vitest-fetch-mock``` or ```jest-fetch-mock``` or ```whatwg-fetch```: These libraries are needed to ```Polyfill fetch``` because fetch is not available in Node, which is where Jest is running your tests. Is it an experimental browser technology. You will need to polyfill the behaviour if you want to make actual http calls, or mock fetch to simulate network requests.

**Types of Test**:

```Unit Test```: It tests a single unit of application. Like if I give name prop to Heading component it' value should change 
```Integration Test```: It tests interaction between different components of the application 
```End to End tests```: It tests the overall flow of the application.


**Use Cases of Jest/Vitest and React Testing Library (from react docs)**

- ```Redering```: In the first step we need to render a component to test. Simplest scenario could be whether a component renders correctly for given props. we can use ```act()``` from ```react-dom``` but we use ```render``` menthod of RTL
- ```Data Fetching```: Instead of calling real APIs in all your tests, you can mock requests with dummy data. Mocking make tests faster and are backend independent. 
- ```Mocking Modules```: Can be done Jest/Vitest
- ```Event``` We recommend dispatching real DOM events on DOM elements, and then asserting on the result. Consider a Toggle component. We use RTL for that.
- **Timers**: Mocking timers if there are some timers used in your app code We use Jest/Vitest for that.


**Typical Scenrio of React Test Cases**:

Typically a ```Test Block``` will have these following 4 steps: 

1. Render a component we need to test
2. Find elemenets in that component we want to interact with
3. Interact with those elements
4. Assert that the values are as expected

Render, Find, Interact, Assert

We can also use  ```describe``` block to hold similar test blocks. It's used to structure tests.
```describe('description about tests',()=>{}) ```

**Query Methods**: used in RTL

- findBy,getBy,queryBy they return single element. While findAllBy, getAllBy, queryAllBy return array of elements 
- findBy works well for async requests. If we want something to async use findBy otherwise use getBy
- In case of no match queryBy return null  but getBy & findBy return error.

*note: In case of [findBy,getBy,queryBy] we want to find the condition that finds only one element. If our condition returns multiple elements then it would fail.*

**Priority of Query (methods)**: used in RTL

1. Accessible by Everyone: getByRole, getByLabelText, getByPlaceholderText, getByText
2. Semantic Queries: getByAltText, getByTitle
3. TestID: getByTestId

**Assertions**: used in jest/vitest

A test assertion is defined as an expression, which encapsulates some testable logic specified about a target under test.
We use ```expect``` function for that. 
The ```expect``` function is used every time you want to test a value. You will rarely call expect by itself. 
For example :  ```expect(text).toBeInTheDocument()```. There's a long of list of assertion methods. like
```().toBeVisible```,```(paragraphElement.textContent).toBe('1 Task Left')```, ```().not.toBeFalsy```,
```(element).toHaveClass('classname') ```
Recommended appraoch is to use one assertion per test

*Note: You can replace it() with xit() to temporarily exclude a test from being executed. Similarly, fit() lets you focus on a specific test without running any other tests.*


**Mocking Api Call (jest,axios)**:

1. jest.mock() function
```
jest.mock('axios') // this will mock axios module
axios.get.mockResolvedValueOnce({ data: 'mock data' }); //now send responce for Get call
const mockedAxios = axios as jest.Mocked<typeof axios>; // can also type cast 'axios' in typescript and use mockedAxios instead of axios


```
2. axios-mock-adapter library


**Mocking Api Call (Mock Service Worker msw)**:

It works like creating an endpoint / middleware in express.js

```
// declare which API requests to mock
const server = setupServer(
  // capture "GET /greeting" requests
  rest.get('/greeting', (req, res, ctx) => {
    // respond using a mocked JSON body
    return res(ctx.json({greeting: 'hello there'}))
  }),
)

// establish API mocking before all tests
beforeAll(() => server.listen())
// reset any request handlers that are declared as a part of our tests
// (i.e. for testing one-time error scenarios)
afterEach(() => server.resetHandlers())
// clean up once the tests are done
afterAll(() => server.close())

// ...

test('handles server error', async () => {
  server.use(
    // override the initial "GET /greeting" request handler
    // to return a 500 Server Error
    rest.get('/greeting', (req, res, ctx) => {
      return res(ctx.status(500))
    }),
  )

  // ...
})
 ```

--------------------------------------------------------------------------------------------------------------------------------

### Important/Popular React.js External Libraries`

1. [Formik](https://formik.org/docs/overview) => Used for Form handling including validation, keeping track of the visited fields, and handling form submission
2. [Axios](https://axios-http.com/docs/intro) => Promise based HTTP client for the browser and node.js
3. [React Router](https://v5.reactrouter.com/web/guides/quick-start) => Declarative routing for React web applications
4. [Redux](https://redux.js.org/introduction/getting-started)=> Used for global State anagement. Redux is a predictable state container for JavaScript apps.
5. [Jest] (https://jestjs.io/docs/tutorial-react) => Used for unit and integration tests.
6. [Material-UI](https://mui.com/)=> The world's most popular React UI framework. Used for consuming pre-built components like overlay, popups, aside-bars etc.
7. [@tanstack/react-query](React Query)=> Used for caching network rquests and state management (recommended by React)
8. [useSWR]


## React Router DOM
- Navigating to the Url
```
const navigate = useNavigate();
 navigate(`company/${symbol}`)
```
- Accessing param from the url 
```
let { id } = useParams();
```

--------------------------------------------------------------------------------------------------------------------------------

### Questions Regarding React

- What is Virtual Dom / Why do we need it / Why direct DOM manupulation is discouraged ? 

- Does react FC use closures?

- How do we default export components. Whats the difference ? 

- Why we usually use ```.map``` in jsx to loop through the list. Why not ```forEach``` ?

--------------------------------------------------------------------------------------------------------------------------------

### Imp points discovered during development

- react class components ```render()``` method are pure funtions. i.e. you cannot update state in it otherwise it will keep calling itself and your app would be stuck(unresponsive). This warning would also popup.
```
Cannot update during an existing state transition (such as within `render`). Render methods should be a pure function of props and state
```

- ```useEffect``` is always called after the render phase of the component. This is to avoid any side-effects from happening during the render commit phase (as it'd cause the component to become highly inconsistent and keep trying to render itself).

- ### UseEffect Hook

useEffect( () => console.log("mount"), [] ); componentDidMount
useEffect( () => console.log("data1 update"), [ data1 ] ); componentDidUpdate
useEffect( () => console.log("any update") );
useEffect( () => () => console.log("data1 update or unmount"), [ data1 ] );
useEffect( () => () => console.log("unmount"), [] ); componentWillUnmount