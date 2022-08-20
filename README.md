# SPA Project Stages Guide

Any project plan needs to be broken up into smaller steps. Here is one recommendation that is by no means the only way to do this.

Some of the advantages of using this guide are as follows;

- <b>Separation:</b> keeping each task separate allows for developers at a wide-range of abilities to add to the project.
- <b>Dependency-independent:</b> where upstream dependencies may take time to complete, this project plan allows for development to continue until they are.
- <b>Iterative</b> these steps can be followed for one part of your project, then done again for the next part and so on.
- <b>Familiarity:</b> having familiarity with one way of completing a project will give you an chance to work out what works for you - feel free to clone this and edit this to your preferences or perhaps start your own one from scratch if you'd prefer.
- <b>No mocks:</b> using mocks sucks! They are hard work to implement and a whole load of boiler plate and confusing code gets left behind once they are no longer being used.

It may make sense to read this guide the whole way through before starting a project;

## Stage 1a

### Stage 1a & 1b can be completed in either order;

- Work out the structure of any data dependencies that will effect the UI.
  - We will call this data "dependency data".
  - Such as;
    - APIs
    - DBs
    - user-event data - is the sidebar open/closed, etc.
    - etc.
- Create dummy data examples for each dependency
  - As many and as much variety of example dummy data as is faesible
  - If using typescript create corresponding types
- Provide a data store (zustand recommended) that will store the data "as is"
  - Do not parse the data in any way - all data may be potentially relevant
  - Any mapping / parsing will be dealt with at a later point

## Stage 1b

- Create the UI components and add data in whatever format makes the most sense.
  - We will call this format "UI Data".

## Stage 2

- Create a temporary section of the UI that can load the dummy data examples into the state.
  - Just use html buttons - do not overcomplicate things
  - If this temporary section causes issues with your UI;
    - <b>Don't</b> make changes to the UI
    - <b>Do</b> consider putting the components at the top (or bottom)
    - <b>Do</b> make the an additional button which closes/hides/removes from the dom this temporary section
- Prepare for althe state load (undefined) as well as

## Stage 3

- Create the mapping functions that will interface the dependency data with the UI data.
  - One directory containing all the mapping functions is recommended (although split into multiple files).
- The mapping functions can then be used - implement this in one of the following ways, either;
  - Within the component jsx;
  - Directly into the state - this is likely a good idea if the same map is used multiple times

```js
import Component1 from '@src/components/Component1'
import useStore from '@src/stores/useStore'
import { mapDependencyDataToUIData } from '@src/mappings'

const {
  dependencyData,
  getComponent1PropsFromDependencyData
} = useStore()

// either
const component1Props = mapDependencyDataToComponent1Props(dependencyData)
// or
const component1Props = getComponent1PropsFromDependencyData() // where mapping is implemented in state

const SomePage = () => {
  return (
    &lt;div>
      &lt;Component1
          { ...componentProps }
          otherProp={true}
          anotherProp={"some Text"}
      />
    &lt;/div>
  )
}

export default SomePage
```

## Stage 4

- Create the actions that will add functionality to your project and add them directly to your state.
  - if an action has async functionality (ie is supposed to send data to the APIs/DBs) we will handle this later, for now just add `console.log('async requst made here')` each time one is called.

## Stage 5

This stage must be done in the following order;

1. Connect your state to APIs/DBs etc - on load make a call that gets the dependency data then loads your state.
2. Once connected, replace `console.log('async requst made here')` with the correct async call to your API/DB.
3. Remove the temporary UI if working - don't delete this if you think it may be of use later.
3. By completing steps 5.1 & 5.2, you are making "optimistic upates" - ie you are always assuming that the updates will work. Think about if this is your preferred behaviour for each scenario and what could potentially go wrong if a call to the APIs/DBs is assumed to have worked but, in fact, failed.
4. This is optional based on your preferred behaviour. Refactor so that the changes to the state only happen after a successful call to the API - we can call these "patient updates". 
5. Additionally you may want create some conditions that show a loading state in the UI - to implement this go through the relevant parts of stages 1-5 again. 
