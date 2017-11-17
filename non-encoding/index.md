---
layout: default
title: "NON-encoding"
summary: "simple universal text encoding"
categories: [encoding]
---

Variable length (wire)
```
0xxxxxxx
1xxxxxxx 0xxxxxxx
1xxxxxxx 1xxxxxxx 0xxxxxxx
1xxxxxxx 1xxxxxxx 1xxxxxxx 0xxxxxxx
```

Fixed length (memory)

```
00000000 0xxxxxxx
1xxxxxxx 0xxxxxxx

00000000 00000000 0xxxxxxx
00000000 1xxxxxxx 0xxxxxxx
1xxxxxxx 1xxxxxxx 0xxxxxxx

00000000 00000000 00000000 0xxxxxxx
00000000 00000000 1xxxxxxx 0xxxxxxx
00000000 1xxxxxxx 1xxxxxxx 0xxxxxxx
1xxxxxxx 1xxxxxxx 1xxxxxxx 0xxxxxxx
```

