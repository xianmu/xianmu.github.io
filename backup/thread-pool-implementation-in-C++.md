---
title: thread pool implementation in C++
tags: development
---

本文记录使用C++实现thread pool的过程。

# basic implementation

线程池的基本概念不多说，简单理解就是一个任务队列，加上若干线程不断从队列中取任务执行。

## Task

首先是为Task提供统一的抽象，这里使用protobuf中的Closure实现。

```
// 1. abstract base class
class Closure {
 public:
  Closure() {}
  virtual ~Closure();

  virtual void Run() = 0; // user defined task code
};

// 2. free functions
class FunctionClosure0 : public Closure {
 public:
  typedef void (*FunctionType)();

  FunctionClosure0(FunctionType func): func_(func) {}
  ~FunctionClosure0() {}

  void Run() {
    func_();
    delete this; // one shot
  }

 private:
  FunctionType func_;
};
Closure* NewCallback(void (*function)()) {
  return new FunctionClosure0(function);
}

// 3. member functions
template <typename Class>
class MethodClosure0 : public Closure {
 public:
  typedef void (Class::*MethodType)();

  MethodClosure0(Class* object, MethodType method) 
    : object_(object), method_(method) {}
  ~MethodClosure0() {}

  void Run() {
    (object_->*method_)();
    delete this; // one shot
  }

 private:
  Class* object_;
  MethodType method_;
};
template <typename Class>
Closure* NewCallback(Class* object, void (Class::*method)()) {
  return new MethodClosure0<Class>(object, method);
}
```

这里仅展示了无参一次性调用的closure实现，多参数，多次调用以及延迟绑定的实现可参考protobuf源码或者自行扩展，不赘述。对于要执行的task，用户只需要调用NewCallback得到对应的closure，然后put进线程池即可。

## ConcurrentQueue

```
template <typename T>
class ConcurrentQueue {
 public:
  ConcurrentQueue();
  ~ConcurrentQueue();
  
  // default non-block
  bool Put(T t, int wait_time_ms = 0);
  T Take();
  size_t Size();

 private:
  std::deque<T> queue_; 
};
```

## Thread

Thread仅仅是pthread库的一个C++封装。

```
class Thread {
 public:
  Thread(Closure* func);
  ~Thread();

  void Start();

 private:
  Closure* func_;
  pthread_t pthread_id_;
};
```

## thread pool

有了Task/ConcurrentQueue/Thread抽象，我们可以开始实现一个简单的线程池了。

```
Class ThreadPool {
 public:
  ThreadPool(size_t size);
  virtual ~ThreadPool();

  void Start(); // start all the threads
  void Stop();

  void AddTask(Closure* callback);
 
 private:
  ConcurrentQueue<Closure*> task_queue_;
  std::vector<Thread> threads_;
  bool running_;

  void ThreadFunction() {
    while(running_) {
      Closure* task = task_queue_.Take(); // block if empty
      task->Run();
    }
  }
};
```

这里使用固定的线程数，不考虑线程增减。
