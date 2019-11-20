---
title: thread-safe singleton in c++
tags: coding
---

C++中实现一个线程安全的单例模式(Singleton)，并不如看起来那么简单。最近组里面刚好听了这么一个分享，阅读了下原始文献[@meyers2004c++]，简单写一个记录。

从一个简单的实现开始。

```
class Singleton {
public:
  static Singleton* instance();

private:
  static Singleton* pInstance;
};

Singleton* Singleton::pInstance = NULL;

Singleton* Singleton::instance() {
  if (pInstance == NULL) {
    pInstance = new Singleton();
  }
  return pInstance;
}
```

这个实现展示了Singleton的大体代码结构，缺点也很显然，它未考虑线程安全性的问题。两个线程可能同时判断并发现pInstance==NULL，导致内存泄露。

# thread-safe singleton

不难修改以上代码使其满足线程安全性要求。

```
Singleton* Singleton::instance() {
  Lock lock; // acquire lock
  if (pInstance == NULL) {
    pInstance = new Singleton();
  }
  return pInstance;
} // release lock
```

这样Singleton是线程安全的了，每次只有一个线程判断是否需要new Singleton，但这个实现每次调用instance都要lock，对于已经正确new过的Singleton显然是没有必要的。

# double-check locking

为解决上面一个实现的性能问题，采用一种称为double-check locking的pattern，即在locking之前先检查一次pInstance==NULL，如果否，则无须lock，否则lock之后再判断是否需要new。

```
Singleton* Singleton::instance() {
  if (pInstance != NULL) // 1st check
    return pInstance;

  Lock lock; // acquire lock
  if (pInstance == NULL) { // 2nd check
    pInstance = new Singleton();
  }
  return pInstance;
} // release lock
```

# perils of DCLP

DCLP看起来完美解决了我们的问题，但其实DCLP不是线程安全的。

```
pInstance = new Singleton();
```

这行代码实际包含了以下三个动作：
1. 为Singleton分配内存；
2. 构造Singleton；
3. 赋值。


# pthread_once

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

This short piece of code is a thread-safe singeton implementatiaon in C++11 as static local intialization is thread safe.
