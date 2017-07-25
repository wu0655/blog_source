---
title: 求比x大的最小的2的N次方数
date: 2017-07-20 14:53:06
categories: reproduction
tags: [algorithm]
---

**求比x大的最小的2的N次方数**

例如：
x = 5， return 8;
x = 8， return 8;
x = 9， return 16;

```cpp
int pow2gt(int x)
{
  --x;
  x |= x>>1;  
  x |= x>>2;
  x |= x>>4;
  x |= x>>8;
  x |= x>>16;
  return x+1;
}
```
