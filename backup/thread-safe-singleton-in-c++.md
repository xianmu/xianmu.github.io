---
title: thread-safe singleton in c++
tags: coding
---

# C++11

If you use C++11, everything is simplified.

```
template<class T>
class Singleton
{
  static T& GetInstance() 
  {
    static T t;
    return t;
  }
};
```

This short piece of code is a thread-safe singeton in C++11.

# C++98

However, a lot of legacy code does not permit you to use C++11, then things get to be a little complicated.

# pthread_once
