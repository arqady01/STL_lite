# 深度探索list

![list1](list/list1.png)

![list2](list/list2.png)

我们希望iterator模拟指针，因为list是非连续空间，`node++`毫无意义，它不知道加到（指向）何处，必须要进入node内部，调用next指针才行，即`(*node).next`

**list的迭代器**

![g2.9_iterator](list/g2.9_iterator.png)

### iterator 设计原则

iterator必须有能力回答algorithm的提问

比如知晓容器迭代器的分类以便高效选择（∵有些容器的迭代器只能++或--，而有的可以跳跃等），可以使用iterator_traits萃取出iterator_category，或者知晓迭代器之间的不同类型：iterator_traits<T>::difference_type，知晓迭代器元素类型：iterator_traits<T>::value_type;

**iterator必须提供的五种相关类型**

![iterator_five_category](list/iterator_five_category.png)

但如果iterator不是class而是一个退化的iterator呢？

定义一个萃取机制，使其有能力分辨是iterator还是pointer----利用偏特化即可解决

![iterator_extra](list/iterator_extra.jpg)

![iterator_extra_code1](list/iterator_extra_code1.png)

完整版：

![iterator_extra_code2](list/iterator_extra_code2.png)

========================

# 探索vector

![vector_model](vector/vector_model.png)



**尾插**

```cpp
void push_back(const T& x) {
    if (finish != end_of_storage) { //还有空间
        construct(finish, x); //全局函数，将finish设为x，但暂时不关心如何实现
        ++finish; //调整finish指针
    } else { //空间不足，需要扩容
        insert_aux(end(), x);
    }
}

template<class T, class Alloc>
void vector<T, Alloc>::insert(iterator position, const T& x) {
    const size_type old_size = size();
    const size_type len = old_size != 0 ? 2 * old_size : 1;

    iterator new_start = data_allocator::allocate(len);
    iterator new_finish = new_start;
    将原vector内容拷贝到新vector
    new_finish = uninitialized_copy(start, position, new_start);
    constuct(new_finish, x); //将new_finish处设为x
    ++new_finish; //调整finish指针
    destory(begin(), end()); //析构原vector
    deallocate();
    start = new_start; //调整迭代器指向新vector
    finish = new_finish;
    end_of_storage = new_start + len;
}

char* uninitialized_copy(char* f, *l, *result) {
    memmove(result, f, l - f);
    return result + (l - f); //重要
}
