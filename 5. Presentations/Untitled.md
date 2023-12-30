## Title 1



---

Title 2

```go
package main
```

```nomnoml
[<start>start] -> [<state>plunder] -> [<choice>more loot] -> [start]
[more loot] no ->[<end>]

[Pirate|
  [beard]--[parrot]
  [beard]-:>[foul mouth]
]

[<table>mischief| bawl | sing || yell | drink ]

[<abstract>Marauder]<:--[Pirate]
[Pirate] - 0..7[mischief]
[<actor id=sailor>Jolly;Sailor]
[sailor]->[Pirate]
[sailor]->[rum]
[Pirate]-> *[rum|tastiness: Int|swig()]

```

