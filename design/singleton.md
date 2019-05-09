# Singleton 单例模式

单例模式是最常见一种设计模式，保证对象是独一无二得。
要求在多线程是安全的
以C++语言为例,采用静态类和静态方法
基本单例模式如下：
```c++
class Singleton {
    public:
        Singleton(const Singleton &) = delete;
        static Singleton * GetInstance();
    private:
    Singleton(){
    }
    Singleton(const Singleton &） = delete; //c++11
    
};

Singleton * Singleton::GetInstance(){
    static Singleton instance;
    return &instance;
}
```

采用静态类的方式，造成程序开销比较大。采用延迟方式，当有需要的时候才创建。
```c++
class SingletonB {
    public:
        SingletonB(const Singleton &) = delete;
        static SingletonB * GetInstance();
    private:
        SingletonB(){
        
        }
    static SingletonB *instance = nullptr;

};

SingletonB SingletonB::instance;
SingletonB* SingletonB::GetInstance() {
        if (instance == nullptr) {
                instance = new SingletonB;
        }      
        return instance;
}  
```
注意此方法不是线程安全，由于指令是乱序的，造成未知的结果

采用原子操作而不是锁机制，减小性能开销，内存屏障的方式，保证了线程安全,[代码出处](https://www.jianshu.com/p/69eef7651667).
```c++
class SingletonA {
    public:
        static std::atomic<SingletonA*> instance;
        static SingletonA *getInstance()    {
            SingletonA* tmp = instance.load(std::memory_order_relaxed);
            std::atomic_thread_fence(std::memory_order_acquire);
            if(tmp == nullptr){
                static std::mutex mtx;
                std::lock_guard<std::mutex> lock(mtx);
                tmp = instance.load(std::memory_order_relaxed);
                if (tmp == nullptr)
                {
                        tmp = new SingletonA();
                        std::atomic_thread_fence(std::memory_order_release);
                        instance.store(tmp, std::memory_order_relaxed);
                }
            }
            return tmp;
        }
};
std::atomic<SingletonA*> SingletonA::instance;

```

c++ 推荐std::call_once函数, 保证函数调用是唯一的。
```c++
#include <mutex>
// function define c++11
template< class Callable, class... Args >
void call_once( std::once_flag& flag, Callable&& f, Args&&... args );

// for example
std::once_flag flag1;
 
void simple_do_once() {
    std::call_once(flag1, [](){ std::cout << "Simple example: called once\n"; });
}
```
