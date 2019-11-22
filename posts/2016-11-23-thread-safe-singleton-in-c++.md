---
title: thread-safe singleton in c++
tags: coding
---

C++中实现一个线程安全的单例模式(Singleton)，并不如看起来那么简单。最近刚好听了这么一个分享，阅读原始文献[@meyers2004c++]后写一个简单的记录。

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

这个实现展示了Singleton的大体代码结构，为保证只有一个实例，ctor设成private，提供一个static的instance函数获取static member。在instance第一次被调用时完成static member的构造。这个实现的缺点很显然，它未考虑线程安全性的问题。两个线程可能同时判断并发现pInstance==NULL，然后都去new Singleton，导致内存泄露。

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

这样Singleton是线程安全的了，每次只有一个线程判断是否需要new Singleton，但这个实现每次调用instance都要lock，对于已经new过的Singleton显然是没有必要的。

# double-check locking

为解决上面一个实现的性能问题，采用一种称为double-check locking pattern的办法，即在locking之前先检查一次pInstance==NULL，如果否，则无须lock，否则lock之后再判断是否确实需要new。

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

DCLP看起来完美解决了我们的问题，但其实DCLP不是线程安全的，这是最神奇的地方。

```
pInstance = new Singleton();
```

这行代码实际包含了以下三个动作：

```
tmp=operator new(sizeof(Singleton)); // step 1
new(pInstance)Singleton;             // step 2
pInstance=tmp;                       // step 3
```

但是，编译器可能reorder step 2 and step 3，DCLP的代码看起来像这样：

```
Singleton* Singleton::instance() {
  if (pInstance != NULL) // 1st check
    return pInstance;

  Lock lock; // acquire lock
  if (pInstance == NULL) { // 2nd check
    Singleton* tmp=operator new(sizeof(Singleton)); // step 1
    pInstance=tmp;                       // step 3
    new(pInstance)Singleton;             // step 2
  }
  return pInstance;
} // release lock
```

先进入critical section的线程执行完step 1后，并发线程在1st check时发现pInstance!=NULL，于是return pInstance，但实际上pInstance只是一段裸内存而已，尚未构造，从而导致core。

# thread safe singleton

引入memory barrier可以解决reordering的问题，代码看起来像这样：

```
Singleton* Singleton::instance() {
  if (pInstance != NULL) // 1st check
    return pInstance;

  Lock lock; // acquire lock
  if (pInstance == NULL) { // 2nd check
    Singleton* tmp = new Singleton();
    MemoryBarrier();
    pInstance = tmp;
  }
  return pInstance;
} // release lock
```

MemoryBarrier要求前后的代码不能越过memory barrier，所以tmp指向的一定是已经构造好的singleton。

# Meyers' Singleton Pattern

由于C++11中规定并发线程必须等待变量初始化完成，所以线程安全的singleton实现就很简单了。

```
Singleton& Singleton::instance() {
  static Singleton ins;
  return ins;
}
```
