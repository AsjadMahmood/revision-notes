### `React Important Concepts to Remember`

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
2. Only Call Hooks from React Functions
3. Call hook from top level of the component function.

- Every time your component renders, useState gives you an array containing two values:
1. The state variable (index) with the value you stored.
2. The state setter function (setIndex) which can update the state variable and trigger React to render the component again.

- **Behind the scene** when you set a state ```const [index, setIndex] = useState(0);```

1. Your component renders the first time. Because you passed 0 to useState as the initial value for index, it will return [0, setIndex]. React remembers 0 is the latest state value.
2. You update the state. When a user clicks the button, it calls setIndex(index + 1). index is 0, so it’s setIndex(1). This tells React to remember index is 1 now and triggers another render.
3. Your component’s second render. React still sees useState(0), but because React remembers that you set index to 1, it returns [1, setIndex] instead.
4. And so on!

- If you find that you often change two state variables together, it might be better to combine them into a single one. For example, if you have a form with many fields, it’s more convenient to have a single state variable that holds an object than state variable per field.

- You can have more than one state variable. Internally, React matches them up by their order.

- State is private to the component. If you render it in two places, each copy gets its own state.

- **Question:** How does React know which state to return? / There is no “identifier” that is passed to useState, so how does it know which of the state variables to return?

- If hooks are declared in conditional statement/ not declared in top level of the component, then we might get this error : 'Rendered fewer hooks than expected.'

- A state variable is only necessary to keep information between re-renders of a component. Within a single event handler, a regular variable will do fine. Don’t introduce state variables when a regular variable works well.

## 3. Render and Commit 

**Step 1:** 'Triggering' a Render

**Step 2:** React 'Renders' your components
“Rendering” is React calling your components.

1. On initial render, React will call the root component. ```root.render(<Image />); //in index.js```
2. For subsequent renders, React will call the function component whose **state update** triggered the render.

- This process is recursive: if the updated component returns some other component, React will render that component next, and if that component also returns something, it will render that component next, and so on. The process will continue until there are no more nested components and React knows exactly what should be displayed on screen.

- During the initial render: React will create the DOM nodes for <section>, <h1>, and three <img> tags.
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

### UseEffect Hook

useEffect( () => console.log("mount"), [] ); componentDidMount
useEffect( () => console.log("data1 update"), [ data1 ] ); componentDidUpdate
useEffect( () => console.log("any update") );
useEffect( () => () => console.log("data1 update or unmount"), [ data1 ] );
useEffect( () => () => console.log("unmount"), [] ); componentWillUnmount



### Important/Popular React.js External Libraries`

1. [Formik](https://formik.org/docs/overview) => Used for Form handling including validation, keeping track of the visited fields, and handling form submission
2. [Axios](https://axios-http.com/docs/intro) => Promise based HTTP client for the browser and node.js
3. [React Router](https://v5.reactrouter.com/web/guides/quick-start) => Declarative routing for React web applications
4. [Redux](https://redux.js.org/introduction/getting-started)=> Used for global State anagement. Redux is a predictable state container for JavaScript apps.
5. [Jest] (https://jestjs.io/docs/tutorial-react) => Used for unit and integration tests.
6. [Material-UI](https://mui.com/)=> The world's most popular React UI framework. Used for consuming pre-built components like overlay, popups, aside-bars etc.


### Questions Regarding React

- What is Virtual Dom / Why do we need it / Why direct DOM manupulation is discouraged ? 

- Does react FC use closures?

- How do we default export components. Whats the difference ? 


### Imp points discovered during development

- react class components ```render()``` method are pure funtions. i.e. you cannot update state in it otherwise it will keep calling itself and your app would be stuck(unresponsive). This warning would also popup.
```
Cannot update during an existing state transition (such as within `render`). Render methods should be a pure function of props and state
```

- ```useEffect``` is always called after the render phase of the component. This is to avoid any side-effects from happening during the render commit phase (as it'd cause the component to become highly inconsistent and keep trying to render itself).
