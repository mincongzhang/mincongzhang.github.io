---
layout: post
title: 有条件地调用构造函数
category: Cpp
description: 有条件地调用构造函数
tags: ["C++"]
---

Sometimes when don't want to call the constructor of some customized class, 
but need to use the object in some situation. Use a pointer pointing to new-created object.

有时候需要在某种条件下才想使用一个成员变量, 但初始那个成员变量必须要调用构造函数...怎么办?

```
Class Foo{
private:
	Bar m_bar;
	Foo():m_bar(init){}
};
```

这时候可以用智能指针:

```
Class Foo{
private:
	Bar m_bar;
	boost::scoped_ptr<Bar> m_bar_ptr;
	
	Foo():
	m_bar_ptr(condition? new Bar(init) : NULL)
	{}
};
```

值得注意的是boost的scoped_ptr也必须要初始化,所以也只能把它放在初始化列表里了(Initialization list).

否则:

error: no match for call to ‘(boost::scoped_ptr<> ... use reset

Reason is, if not called in constructor, needs ()operator. But smart pointer has no ()operator. So failed.
But declare in class, and call it in class initialization list is fine. Initialization list will use constructor.
http://stackoverflow.com/questions/17938399/boost-scoped-ptr-declaration-vs-allocation

p.s.
shared_ptr is more expensive than scoped_ptr