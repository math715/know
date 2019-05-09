# STL

## 空间配置器
第一级配置器 malloc / free  > 128  
第二级配置器 freelists[16] memory pool
处理工具: 
contruct  destory
uninitialized_copy
uninitialized_fill
uninitialized_fill_n

## 迭代器与traits（萃取)编程技巧

Traits 编程技法
typename 告诉编译器是一个型别

迭代器相应型别：value type, difference type, point, reference, iterator catagoly
```c++
template <class I>
struct iterator_traits {
    typedef typename I::iterator_category iterator_category
    typedef typename I::value_type        value_type
    typedef typename I::difference_type   difference_type
    typedef typename I::pointer           pointer
    typedef typename I::reference         reference
}
```
偏特化
```c++
template <class T>
struct iterator_traits<T*>{
    typedef typename I::iterator_category iterator_category
    typedef typename T        value_type
    typedef typename ptrdiff_t   difference_type
    typedef typename T*           pointer
    typedef typename T&         reference
}
```
偏特化
```c++
template <class T>
struct iterator_traits<const T*>{
    typedef typename I::iterator_category iterator_category
    typedef typename T        value_type
    typedef typename ptrdiff_t   difference_type
    typedef typename T*          pointer
    typedef typename T&         reference
}
```

迭代器：
* input iterator
* output iterator
* forward iterator
* bidirectional iterator
* random access iterator

```c++
struct input_iterator_tag{};
struct output_iterator_tag{};
struct forward_iterator_tag : public input_iterator_tag{};
struct bidirection_iterator_tag: public forward_iterator_tag{};
struct random_access_iterator_tag: public bidirection_iterator_tag{};

```
iterator_traits 偏特化
```c++
template <class Iterator>
struct iterator_traits {
    typedef typename Iterator::iterator_category iterator_category;
    typedef typename Iterator::value_type        value_type;
    typedef typename Iterator::difference_type   difference_type;
    typedef typename Iterator::pointer           pointer;
    typedef typename Iterator::reference         reference;
};

template <class T>
struct iterator_traits <T*> {
    typedef random_access_iterator_tag iterator_category;
    typedef T        value_type;
    typedef ptrdiff_t   difference_type;
    typedef T*          pointer;
    typedef T&        reference;
};

template <class T>
struct iterator_traits <const T*> {
    typedef random_access_iterator_tag iterator_category;
    typedef T        value_type;
    typedef ptrdiff_t   difference_type;
    typedef const T*          pointer;
    typedef const T&        reference;
};
```

### advance

```c++
template <class InputIterator, class Distance>
inline void __advance(InputIterator &i, Distance n, input_iterator_tag) {
    while (n--) i++;
}

template<class BidiectionalIterator, class Distance>
inline void __advance(BidiectionalIterator &i, Distance n, bidirection_iterator_tag) {
    if (n >= 0) {
        while (n--) ++i;
    } else {
        while (n++) --i;
    }
}


template<class RandomAccessIterator, class Distance>
inline void __advance(RandomAccessIterator &i, Distance n, random_access_iterator_tag) {
    i += n;
}

template<class InputIterator, class Distance>
inline void advance(InputIterator &i, Distance n) {
    __advance(i, n, iterator_category(i));
}

template<class Iterator>
inline typename iterator_traits<Iterator>::iterator_category 
iterator_category(const Iterator&) {
    typedef typename iterator_traits<Iterator>::iterator_category category;
    category();
}
```

### distance 
```c++
template <class InputIterator>
inline iterator_traits<InputIterator>::difference_type 
__distance(InputIterator first, InputIterator last, input_iterator_tag) {
 iterator_traits<InputIterator>::difference_type n = 0;
 while (first != last) {
     ++first; 
     ++n;
 }
 return n;
}

template <class RandomAccessIterator>
inline iterator_traits<RandomAccessIterator>::difference_type
__distance(RandomAccessIterator first, RandomAccessIterator last, random_iterator_tag) {
    return last - first;
}

template<class InputIterator>
inline iterator_traits<InputIterator>::difference_type 
distance(InputeIterator first, InputIterator last) {
    typedef typename iterator_traits<InputIterator>::iterator_category category;
    return __distance(first, last, category());
}
```


## 容器
### vector
### list
### deque 
### stack
### queue
### heap
### priority_queue
### slist

##
### tree
### RB-tree
### set
### map
### multiset
### multimap
### hash_set
### hast_map

## 算法

## 仿函数

## adapters