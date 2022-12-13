---
layout: snippets
title: Three ways to find network components
---

```lua
function Init()
  event.ignoreAll()
  event.clear()
end

function Update()
  while true do
    let e, s, v = event.pull(1)
  end
end

Init()
Update()
```
