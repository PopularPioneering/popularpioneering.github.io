---
layout: snippets
title: Boilerplates for FicsIt-Networks
description: Handy boilerplates with some common setup and configuration functions to bring your graphics and network cards online
---

Here's a simple boilerplate that allows your script to run safely and gives you some useful ways to inject your logic. The Init function is called when the script first runs (you could perform additional setup functions from here). Once `Init()` has resolved `Update()` is called ensuring your script will run indefinitely. **Please note**, lowering the value passed to `event.pull()` is not recommended for performance reasons.

```lua
function Init()
  event.ignoreAll()
  event.clear()
  
  -- perform additional configuration steps here
  
end

function Update()
  while true do
    local e, s, v = event.pull(1)
    
    -- check for events here
   
  end
end

Init()
Update()
```

Here's the same boilerplate with some additional functions called in the Init function. These functions will register and bind a connected GPU, Large Screen and Network Card. `print()` commands have been used to display useful debugging information.

```lua
local gpu, network

function registerGraphics()
  print('- Registering GPU')
  gpu = computer.getPCIDevices(findClass("GPUT1"))[1]
  if gpu then 
    print('-- GPU found:', '#'..gpu.hash)
  else
    error('-- No GPU found. Connect a GPU or remove this function call.')
  end
  
  local screen = component.proxy(component.findComponent(findClass('Build_Screen_C')))[1]
  if screen then
    print('-- Screen found:', '#'..screen.hash)
  else
    print('-- No Screen found. Connect a screen or remove this function call')
  end

  event.listen(gpu)  
  gpu:bindScreen(screen)  
  gpu:flush()
  print('-- GPU bound and ready')
end

function registerNetwork()
  print('- Registering Network')
  network = computer.getPCIDevices(findClass("NetworkCard"))[1]
  if network then 
    print('-- Found', network)
  else
    error('-- No Network card found. Connect a card or remove this function call.')
  end
  event.listen(network)
  network:open(network_port)
  print('-- Network online with port', network_port, 'open')
end

function Init()
  print('[INIT] ================================================')

  event.ignoreAll()
  event.clear()

  registerGraphics()

  registerNetwork()

  -- perform additional configuration steps here

  print('=======================================================')
  print('Listenting for events...')
end

function Update()
  while true do
    local e, s, v, p, m = event.pull(1)
    
    -- check for events here   
    
    -- example event handling
    if e == "NetworkMessage" then
       print(e, s, v, p, m)
    end
  end
end

Init()
Update()
```
