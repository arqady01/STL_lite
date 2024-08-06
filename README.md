# 深度探索list

图片1

图片2

我们希望iterator模拟指针，因为list是非连续空间，`node++`毫无意义，它不知道加到（指向）何处，必须要进入node内部，调用next指针才行，即`(*node).next`

**list的迭代器**

图片

### iterator 设计原则

iterator必须有能力回答algorithm的提问

比如知晓容器迭代器的分类以便高效选择（∵有些容器的迭代器只能++或--，而有的可以跳跃等），可以使用iterator_traits萃取出iterator_category，或者知晓迭代器之间的不同类型：iterator_traits<T>::difference_type，知晓迭代器元素类型：iterator_traits<T>::value_type;

**iterator必须提供的五种相关类型**

图片5

但如果iterator不是class而是一个退化的iterator呢？

定义一个萃取机制，使其有能力分辨是iterator还是pointer----利用偏特化即可解决

