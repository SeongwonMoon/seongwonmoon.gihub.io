---
title: First Post in Gatsby
date: "2020-08-19T22:40:32.169Z"
template: "post"
draft: false
slug: "hello gatsby"
category: "test"
tags:
  - "test"
description: "Cehck out."
socialImage: "/media/42-line-bible.jpg"
---

# TL;DR

```python
import sys
 
l = [n*2 for n in range(1000)] # List comprehension
g = (n*2 for n in range(1000)) # Generator expression
 
print(type(l))  # <type 'list'>
print(type(g))  # <type 'generator'>
 
print(sys.getsizeof(l))  # 9032
print(sys.getsizeof(g))  # 80
 
print(l[4])   # 8
#print(g[4])   # TypeError: 'generator' object has no attribute '__getitem__'
```

# iterable 객체란

1. **순회**(traversal) 가 가능한가?
    - 즉 `for` 문으로 돌릴 수 있는가?
2. 모든 **순회**가 끝났을 때 `Stopiteration` 에러를 반환하는가?

> 위 2개 조건을 만족하면 iterable 객체라고 한다. 우리가 익숙한 `list`, `tuple`, `dictionary`, 심지어 `string` 까지 모두 (iterable 객체 자체는 아니지만)iterable 객체로 전환이 가능하다.

```python
L = [1, 2, 3]
t = (1, 2, 3)
dic = {'a':123,}
s = 'abcde'

# iter 을 통해 ietrable 로 전환하였다.
it_obj = iter(L)
it_obj
>>> <list_iterator at 0x10c6e9910>

# next 와 stopiteration
next(it_obj)
>>> 1
next(it_obj)
>>> 2
next(it_obj)
>>> 3
next(it_obj)
>>> StopIteration:
```

# i**terator 이란**

- `__iter__` 과 `__next__` 함수가 있는 객체를 말한다. `dir` 로 뜯어보며 살펴보자.

```python
L = [1, 2, 3]

#  __iter__ 함수가 들어있는 것을 확인할 수 있다.
dir(L)
>>> ['__doc__'...**'**__iter__**'**...'sort']

# 직접 __iter__ 함수를 출력한 자체를 print문을 사용해서 출력해보면 이터레이터 객체임을 확인할 수 있다.
print(L.__iter__())
>>> <list_iterator object at 0x10c41e250>

# 즉, `iter()` 외에도 위 방법으로 __next__함수를 호출할수 있는 객체가 된다.
next(it_obj)
>>> 1
next(it_obj)
>>> 2
next(it_obj)
>>> 3
next(it_obj)
>>> StopIteration:
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/da51f428-4322-439e-827e-0283fdf80d7b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/da51f428-4322-439e-827e-0283fdf80d7b/Untitled.png)

# genera**tor 이란**

- `iterator` 의 일종이다. 따라서 마찬가지로 `__next__` 함수로 값을 꺼내올 수 있고 반복문이 끝나게 되면 `StopIteration` 이 발생한다.
- `yield`(산출) 가 들어있으면 `generator` 객체를 준다.
- 객체 자체에 `__iter__` 와 `__next__` 함수가 둘다 들어있어  이터레이터에서 처럼 `__iter__`를 호출한 후에 `__next__`함수를 호출하지 않고 `__next__`를 바로 호출할 수 도 있다.

```python
li = [1,2,3,4,5]
def gen(li):
    for elem in li:
        yield elem

g = gen(li)
g
<generator object gen at 0x10c6e2ed0>

dir(g)
>>> [...'__iter__', '__next__',...]
```

- 또한 send 함수를 통해 함수 연산 중간에 값을 전달할 수도 있다.

```python
def generator_send():
    received_value = 0

    while True:
        received_value = yield
        print("received_value = ",end=""), print(received_value)
        yield received_value * 2
        
        
gen = generator_send()

next(gen)
print(gen.send(2))
>>> received_value = 2
>>> 4

next(gen)
print(gen.send(3))
>>> received_value = 3
>>> 6

# 이해하기 약간 난해하다. 어찌되었든 iterator 객체라는 관점에서 순환 가능한 배열을 받아들여야 하는데 이는 while True 로 (계속 실행되게) 대체 해주고 있다. 따라서 입력받는 숫자를 이어진 배열로 인식하게되고 다음 차례차례는 next 을 통해 넘어갈 수 있다.
```

# genera**tor expression 이란**

- 제너레이터 함수외에도 제너레이터 표현식(generator expression)이 있다. 제너레이터 표현식은 직접적인 Lazy evaluation을 위해서 사용될 수 있다.
- 아래 코드에서`[ lazy_return(i) for i in L ]`  과 `( lazy_return(i) for i in L )`  는 반환하는 객체가 일시에 실행되는지, 아니면 순차적으로 실행되는지 차이를 볼수 있다.

```python
import time

def print_iter(iter):
    for element in iter:
        print(element)

def lazy_return(num):
    print("sleep 1s")
    time.sleep(1) # 1초 쉰 다음에 실행되라
    return num
```

```python
print("comprehension_list=")
comprehension_list = [ lazy_return(i) for i in L ]
print_iter(comprehension_list)

comprehension_list=
sleep 1s
sleep 1s
sleep 1s
sleep 1s
sleep 1s
sleep 1s
sleep 1s
sleep 1s
sleep 1s
sleep 1s
1
2
3
4
5
6
7
8
9
10

print("generator_exp=")
generator_exp = ( lazy_return(i) for i in L )
print_iter(generator_exp)

generator_exp=
sleep 1s
1
sleep 1s
2
sleep 1s
3
sleep 1s
4
sleep 1s
5
sleep 1s
6
sleep 1s
7
sleep 1s
8
sleep 1s
9
sleep 1s
10
```

# **이걸 왜 쓰는가?**

- 모두 게으른 연산때문에 iter을 쓴다. 게으른 연산을 쓰면 뭐가 좋을까? 언제쓸까?
- for 문은 연산의 순간을 결정할 수 없다. (계산 과정에서 손 놓고 있어야 한다.) 실행 과정에서 control flow 를 내가 계산할 수 있다라는 것은 대단한 이점이다.
- iterable 객체는 list 객체에 비해 메모리가 적다. 즉, 내가 가지고 있는 배열이 클 때 사용한다. iterable 객체는 데이터(저장 위치를 가리키는 포인터)를 가지고 있다가 하나씩 뱉기 때문에 배열 전체를 들고 있을 필요가 없다.

# Reference

- [https://bluese05.tistory.com/55](https://bluese05.tistory.com/55)
- [https://code-maven.com/list-comprehension-vs-generator-expression](https://code-maven.com/list-comprehension-vs-generator-expression)
- 컴퓨터 사이언스 부트캠프 with 파이썬 Computer Science Bootcampwith Python (양태환)
- 전문가를 위한 파이썬