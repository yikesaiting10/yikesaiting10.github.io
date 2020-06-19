---
layout: post
title:  shared_ptr的实现
subtitle: 面试知识点
author: zql
date: 2020-06-19
header-img: img/c++1.jpg
catalog:  true
tags:
    - C++
    - 算法
---
用一个指针指向对象，用一个指针记录引用计数。  
```c++
template <class T>
class smart
{
private:
	T* _ptr;
	int* _count;
public:
	//构造函数
	smart(T* ptr = nullptr) :_ptr(ptr)
	{
		if (_ptr)
		{
			_count = new int(1);
		}
		else
		{
			_count = new int(0);
		}
	}
	//复制构造函数,传引用，避免错误调用复制构造函数
	smart(const smart& ptr)
	{
		if (this != &ptr)
		{
			this->_ptr = ptr._ptr;
			this->_count = ptr._count;
			(*this->_count)++;
		}
	}
	//重载=，传引用，返回引用，避免多次调用复制构造函数
	smart& operator=(const smart& ptr)
	{
		if (this == &ptr)
		{
			return *this;
		}
		else
		{
			if(*(this->_count) > 0)
				*(this->_count)--;
			if (*(this->_count == 0))
			{
				delete this->_count;
				delete this->_ptr;
			}
			this->_ptr = ptr._ptr;
			this->_count = ptr._count;
			return *this;
		}
	}
	//重载*
	T& operator*()
	{
		if(_ptr)
			return *_ptr;
	}
	//取计数
	int countSize()
	{
		return *_count;
	}

	~smart()
	{
		(*_count)--;
		if (*_count == 0)
		{
			delete _ptr;
			delete _count;
		}
	}
};

int main()
{
	smart<int> p1(new int(1));
	cout << "初始化p1后计数为：" << p1.countSize() << endl;
	smart<int> p2(p1);
	cout << "复制构造p2后计数为：" << p2.countSize() << endl;
	while (true)
	{
		smart<int> p3 = p2;
		cout << "=得到p3后计数为：" << p3.countSize() << endl;
		break;
	}
	cout << "p3离开作用域后计数为：" << p2.countSize() << endl;
	getchar();
	return 0;
}
```
```
初始化p1后计数为：1
复制构造p2后计数为：2
=得到p3后计数为：3
p3离开作用域后计数为：2
```
