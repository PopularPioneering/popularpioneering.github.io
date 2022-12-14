---
layout: guides
title: Three ways to find network components
thumbnail: /uploads/thumb-finding-components.jpg
masthead: /uploads/finding-components.jpg
---

## 1. By GUID

When you connect a component to your network it is assigned a unique identifier, known as a GUID. You can pass a GUID as a string into the `component.proxy()` function to get a quick reference to your networked component. You can easily copy the GUID of a component to the clipboard by clicking it in the list of attached components in the terminal.

```lua
local container = component.proxy('0123456789abcdef0123456789abcdef')
-- Do stuff with your container, like:
-- container:getInventories()
```

This technique is not advised for a number of reasons. GUIDs are assigned to the unique instance of that component; deconstruct the object and you will 'lose' the associated GUID requiring you to manually update your code with the GUID of the replacement. This can be a real hassle - GUIDs are not particularly readable and the more you add to your code the less meaning each one has. Scrolling through a long list of GUIDs to find the right one can get annoying very quickly. 

We want our code to be easy to maintain and not rely on lots of manual copy/pasting, avoiding that kind of grunt work with clever logic is the reason we're here! 

## 2. By nickname query

Rather than directly referencing a component, we can search - or query - our network for something more meaningful. You can give your networked components nicknames by interacting with them using the Network monitor. 

Here's an example of how you find a component with a nickname. We've given a connected Mk1 container the nickname of `Storage Container 1`.

```lua
local containerGuids = component.findComponent('Storage Container 1')
local containers = component.proxy(containerGuids)
-- returns an array of matching components
```

This time we are using another method of the component class: `component.findComponent()`. It takes one or more strings (our query terms) and returns an array of component GUIDs with nicknames that match those terms. This means that rather than passing a single GUID string into `component.proxy()` like before, we are passing in an array of GUID strings. 

When `proxy()` receives an array instead of a single string, it returns an array of components instead of a single component. Even if there's only a single GUID in the array, `proxy()` will return an array containing the single component.

#### Working with component arrays

One of the easiest ways to interact with an array of components is to loop over each item. In this example we've given some Rail Block Signals the nickname 'Networked Rail Signal'. To receive events from these signals we need to pass each one to the `event.listen()` method.

```lua
local networkSignalGuids = component.findComponent('Networked Rail Signal')
local networkSignals = component.proxy(networkSignalGuids)

for index, signal in pairs(networkSignals) do
  event.listen(signal)
end
```

If we ever need to add more signals, we can just give them the same nickname, restart our script, and our new signals will be picked up alongisde the old ones. No code changes required!

#### Nickname queries are powerful

Understanding how component queries work is an essential part of building networks with FIN. Think of them like a search engine - rather than looking for exact matches, `findComponent()` will return any component where one or more words in the nickname matches the search term. 

To test this out, build 3 containers and connect them to a networked computer. Lets imagine we want to store Iron Ore in the first container, Copper Ore in the second, and Iron Plates in the third. Using the Network Manager, give the three containers the following nicknames:

```
Storage Iron Ore
Storage Copper Ore
Storage Iron Plates
```

We can use `findComponent()` to find these components in many different arrangements. 

```lua
local allContainers = component.proxy(component.findComponent('Storage'))
-- This will return all three containers

local ironContainers = component.proxy(component.findComponent('Iron'))
-- This will return the Iron Ore and Iron Plates containers

local oreContainers = component.proxy(component.findComponent('Ore'))
-- This will return the Iron Ore and Copper Ore containers
```

We've also condensed our `proxy()` and `findComponent()` calls down into one line. Typically you will only ever pass the result of `findComponent()` into a single `proxy()` call. There's no need to declare it as a variable so we can just pass the result straight in. Nice!


#### I only need a single component!

You may find yourself using `findComponent` to search for a single component - thats fine, in fact there's a nice little piece of 'syntactic sugar' you can do to make your code a little neater.

```lua
local container = component.proxy(component.findComponent('StorageContainer 1'))[1]
-- Returns a single container
```

Notice the `[1]` at the end? That's an instruction to select the first index in our array. We know that the array returned by `proxy()` will contain a single item at index `1` (Lua is one-indexed by default) so we dive right in and pass the component into our local `container` variable. If our array did contain more than one item  we could use `[2]` for the second component, `[3]` for the third component and so on.


## 3. By class

Can we avoid having to manually enter nicknames entirely? Nicknames are very useful when organizing your network, but there are some instances where you might need to 'discover' components on a network without manually naming them. Enter the `findClass()` function.

Here's how it looks added to our shortened one-liner from the previous example:

```lua
local component.proxy(component.findComponent(findClass("Build_StorageContainerMk1_C")))
```

This time we're chaining three function calls. We pass the result of `findClass()` into `findComponent()` and then we pass the result of that into `proxy()`.

We're passing a string into `findClass()`, this is the internal name the game uses to differentiate object types. You can use the Reflection Viewer built into the Network Manager to find out the class name of an object, just click on an object to see its entry, the class name should be at the top. Typically they all follow the format `Build_[ObjectName]_C`

Rather than returning a string or array, `findClass()` returns the Type that matches the name. When `findComponent()` receives a Type instead of string, it returns an array of UUIDs for every component with a matching Type on the network. 
 
You might have also noticed that `findClass()` isn't a method of the component class. Instead it is an additional function provided by the FIN Lua runtime. Thanks FicsIt-Networks!

## Further Reading

As ever, the FicsIt-Networks Documentation is a great place to cement your understanding of the component network:

* [Basic Concepts: Network Components](https://docs.ficsit.app/ficsit-networks/latest/BasicConcept.html#_network_components)
* [The Component API](https://docs.ficsit.app/ficsit-networks/latest/lua/api/Component.html)
* [The Component Network](https://docs.ficsit.app/ficsit-networks/latest/lua/guide/TheComponentNetwork.html)
* [Interacting With Components](https://docs.ficsit.app/ficsit-networks/latest/lua/guide/InteractingWithComponents.html)



