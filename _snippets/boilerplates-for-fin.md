---
layout: snippets
title: Boilerplatetes for FicsIt-Networks
description: A handy boilerplate with some common setup and configuration functions to bring your graphics and network cards online
---

```lua
local gpu, network
local network_port = 1111

function registerGraphics()
  print('- Registering GPU')
  gpu = computer.getPCIDevices(findClass("GPUT1"))[1]
  if gpu then 
    print('-- GPU found:', '#'..gpu.hash)
  else
    error('-- No GPU found. Connect a GPU or remove this function call.')
  end
  screen = component.proxy(component.findComponent(findClass('Build_Screen_C')))[1]
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

  print('=======================================================')
  print('Listenting for events...')
end

function Update()
  while true do
    local e, s, v = event.pull(1)
   
   
  end
end

Init()
Update()
```
