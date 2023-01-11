# React under the hood

## Introduction
React is an open-source JS library for building user interfaces based on UI components, and is commonly used in single-page applications (SPA). In this series of blogs, I will try to explain how React works behind the scenes. Before we go deep into React we have to explain some concepts like JSX, React components, elements, and component instances.
## JSX
Since we are talking about React, JSX must be mentioned. so, what is **JSX**?
JSX stands for JavaScript XML, and it's a syntax extension for JS that allows us to write HTML in JS code. 

1- React example using JSX
```
const myElement = <h1>Hello from the other side!</h1>;

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(myElement);
```
2- React example without JSX
```
const myElement = React.createElement('h1', {}, 'Hello from the other side!');

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(myElement);
```
By comparing 1st and 2nd code snippets,  you can see that using JSX makes React applications easier, allows you to describe what the UI should look like in HTML, and each tag in JSX serves as a “function call” that creates an element using ```createElement()```

## React components, Element, and component instances.
Let's see an example of React component
```
export default function App() {
  return <div className="App"></div>;
}
```
This is a simple React component that returns some JSX. but for React, the return value is an object like this, ``` console.log(welcome())``` 

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1658931699332/_TBDotOGd.png align="left")
Fig1

So what is happening here?. From the JSX section, I mentioned that every JSX serves as a function call to ``` React.createElement() ``` and each of these functions returns an object similar to Fig1.
Let's take a look at the object in Fig1. What's ** Type **? The type refers to an HTML element. you can see that it's ``` div ``` as in Fig1 we are returning ```div``` element.
You can see that **key** and **ref** are null since we have not provided any of them. But what are the **props**? The props contain all of the children.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1658932039408/eKZQMt6rW.png align="left")
Fig2

In Fig2, you can see that the div has a child ```h1``` so the return value of ```console.log(App())``` will be the same as in Fig1 but will add the children to the props. see in Fig3.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1658932182747/3BQJP5Kgj.png align="left")
Fig3
Let's move component instances.  It's the copy/instance of your component and will be used in render.
What does this mean?
in Fig2,3 we were logging the ``` App()``` but actually we do not use it like this. we call it as JSX element ```<App/>``` so let's log it and check what is the difference.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1658936071400/YRy8GVwqo.png align="left")
Fig 4
You can see that it looks like React element object except for the **type** property. The type here is representing our component. when we render our component in JSX, React is calling it behind the scene and check if it's a function or class component. 
If it's a function, React calls it directly, if it's a class Rect creates an instance of that class and calls its render method. so we can conclude that when React describes the component, React create an instance of that component and keep track of it, each instance has its own life cycle and an internal state. 

## Reconciliation
From the above section, we now understand that React creates a tree of elements, and it keeps it in memory, in something called VDOM (virtual dom) then it syncs the  VDOM and DOM. but what will happen if some component states and attributes have been changed?
Let's take some example
```
<h1 className="simple">First</h1>
```
```
<h1 className="dif" >First</h1>
```
As you can see they are identical except for the **className**. now some tree element has been updated, So how will react render the updated tree? React would try to make the least amount of changes possible. it takes the old tree and finds the smallest number of changes and updates them.  in our example, it just will update the **className** 
and this operation is getting handled by The Diffing Algorithm.

## The Diffing Algorithm 
As we mentioned in the last section, React uses The Diffing Algorithm for comparing the trees and finding the smallest number of operations.
Under normal circumstances, it has a complexity in the order of O(n3) where n is the number of elements in the tree. If you have 1000 elements => 1000 000 000 operations which are so expensive.  
>  React implements a heuristic O(n) algorithm based on two assumptions:

> 1- Two elements of different types will produce different trees.

> 2-  The developer can hint at which child elements may be stable across different renders with a key prop.

### The Diffing Algorithm assumptions

1- Two elements of different types will produce different trees means that whenever the root have different types, it will lead to a full rebuild. React will tear down the old tree and create a new one. Going from ```<a>```  to ```<input>```  or any tags will lead to a full rebuild.
When updating the attributes, React will just update the tree without needing to rebuild.
``` 
<h1 className='first'>test</h1>


<h1 className='last'>test</h1>
``` 
2- The developer can hint at which child elements may be stable across different renders with a key prop. Let's take an example to understand what this means.

```
<ul>
<li>first</li>
<li>second</li>
</ul>

<ul>
<li>first</li>
<li>second</li>
<li>third</li>
</ul>
```
Imagine you have an unordered list with 2 items and you want one more item at the **bottom** of the UL. In this case, React compares the first 2 items and recognizes that they are the same but notices that the 3rd one is new, so it will just insert it at the bottom and won't rerender the tree. but what will happens if we added the 3rd item at the top of our UL?
```
<ul>
<li>first</li>
<li>second</li>
</ul>

<ul>
<li>third</li>
<li>first</li>
<li>second</li>
</ul>
```
In this case, React compares the items at the same positions and notices they are different, so it will create a new tree and we know that it's a costly operation and we do not want it. so how did React solve that? yes by providing a **Key** to the children. React uses the key to match the children in the original tree with children in the subsequent tree.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1658943028858/bQ6Rcgrwz.png align="left")

```
<ul>
<li key="first">first</li>
<li key="second">second</li>
</ul>

<ul>
<li key="third">third</li>
<li key="first">first</li>
<li key="second">second</li>
</ul>
```
## Resource
1- [ReactJS doc](https://reactjs.org/docs/getting-started.html)
  


