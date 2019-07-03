---
title: leveldb-annotated-1
tags: database
---

阅读了一遍leveldb-1.20的源码，写一个总结，不包括如lsm-tree等理论基础。

leveldb-1.20源码目录结构如图：

目录名及文件名基本体现了其逻辑功能，不赘述。

# port

port目录下是一些跨平台特性的封装，如字节序，多线程等等，我们只关注Gcc on X86下的实现。

## AtomicPointer

首先是AtomicPointer, 利用memory barrier实现了单个指针的acquire/release语义，避免reordering导致的bug。

```
inline void MemoryBarrier() {
  __asm__ __volatile__("" : : : "memory");
}
class AtomicPointer {
 private:
  void* rep_;
 public:
  AtomicPointer() { }
  explicit AtomicPointer(void* p) : rep_(p) {}
  inline void* NoBarrier_Load() const { return rep_; }
  inline void NoBarrier_Store(void* v) { rep_ = v; }
  inline void* Acquire_Load() const {
    void* result = rep_;
    MemoryBarrier();
    return result;
  }
  inline void Release_Store(void* v) {
    MemoryBarrier();
    rep_ = v;
  }
};
```

## Mutex

Mutex是对pthread_mutex的简单封装，除Lock/Unlock外没有实现其它接口。noncopyable也是C++惯用法。AssertHeld实际是空实现，pthread没有提供检查线程是否持有mutex的接口，可以参考muduo的实现。PthreadCall封装了结果检查及错误打印。

```
static void PthreadCall(const char* label, int result) {
  if (result != 0) {
    fprintf(stderr, "pthread %s: %s\n", label, strerror(result));
    abort();
  }
}

class Mutex {
 public:
  Mutex() { PthreadCall("init mutex", pthread_mutex_init(&mu_, NULL)); }
  ~Mutex() { PthreadCall("destroy mutex", pthread_mutex_destroy(&mu_)); }

  void Lock() { PthreadCall("lock", pthread_mutex_lock(&mu_)); }
  void Unlock() { PthreadCall("unlock", pthread_mutex_unlock(&mu_)); }
  void AssertHeld() { }

 private:
  friend class CondVar;
  pthread_mutex_t mu_;

  // No copying
  Mutex(const Mutex&);
  void operator=(const Mutex&);
};
```

util目录下有一个mutexlock.h是Mutex的Scope Guard。

```
class SCOPED_LOCKABLE MutexLock {
 public:
  explicit MutexLock(port::Mutex *mu) EXCLUSIVE_LOCK_FUNCTION(mu)
      : mu_(mu)  {
    this->mu_->Lock();
  }
  ~MutexLock() UNLOCK_FUNCTION() { this->mu_->Unlock(); }

 private:
  port::Mutex *const mu_;
};
```

## CondVar

CondVar是pthread_cond的简单封装，接口只有基础的Wait/Signal/SignalAll。

```
class CondVar {
 public:
  explicit CondVar(Mutex* mu) : mu_(mu) {
    PthreadCall("init cv", pthread_cond_init(&cv_, NULL));
  }
  ~CondVar() { PthreadCall("destroy cv", pthread_cond_destroy(&cv_)); }

  void Wait() { PthreadCall("wait", pthread_cond_wait(&cv_, &mu_->mu_)); }
  void Signal() { PthreadCall("signal", pthread_cond_signal(&cv_)); }
  void SignalAll() { PthreadCall("broadcast", pthread_cond_broadcast(&cv_)); }

 private:
  pthread_cond_t cv_;
  Mutex* mu_;
};
```

## PthreadOnce

PthreadOnce也没什么要说的。

```
void InitOnce(OnceType* once, void (*initializer)()) {
  PthreadCall("once", pthread_once(once, initializer));
}
```

# miscellaneous

除以上之外，port目录下还有一些其他的平台相关特性的处理，如fread/fwrite/fflush，fsync。以及snappy的编译检查及接口封装；利用SSE加速CRC32的实现；thread annotation等等。
