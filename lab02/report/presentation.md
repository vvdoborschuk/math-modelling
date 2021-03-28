---
## Front matter
title: Лабораторная работа №2. Задача о погоне
author: [Доборщук Владимир Владимирович]
institute: "RUDN University, Moscow, Russian Federation"
subtitle: "c/б 1032186063 | НФИбд-01-18"
date: 27 марта 2021
lang: "ru"
## Formatting
toc: false
slide_level: 2
theme: metropolis
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: Fira Sans
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
 - '\makeatletter'
 - '\beamer@ignorenonframefalse'
 - '\makeatother'
aspectratio: 43
section-titles: true

---
# Цели и задачи

## Цель

Изучить задачу о погоне, а также реализовать её модель программно.

## Задачи

* изучить теорию о задаче о погоне
* построить модель для 2 случаев траектории движения

# Ход выполнения лабораторной работы

## Программная реализация

**Инициализация библиотек**

```python
import math
import numpy as np
from scipy.integrate import odeint
import matplotlib.pyplot as plt
```

## Программная реализация

Введём соответствующие нашему варианту начальные данные для построения модели:

```python
k=7.5
n=3.1

fi=math.pi/3
x1=k/(n+1)

r0=x1
tetha0=0
tetha=np.arange(tetha0, 2*math.pi, 0.001)

t=np.arange(0,6.284,0.001)
t1=np.arange(r0, k, 0.01)
```

## Программная реализация

Создадим функцию для нашей СДУ:


```python
def dr(r,tetha):
    dr = r/math.sqrt(n*n-1)
    return dr

def f2(t):
    xt=math.tan(fi+math.pi)*t
    return xt

def f3(t1):
    xt=math.tan(tetha0)*t1
    return xt

def cart2pol(x,y):
    rho = np.sqrt(x**2 + y**2)
    phi = np.arctan2(y, x)
    return rho, phi
```

## Программная реализация

Воспользуемся функцией `odeint` из модуля `scipy.integrate` и решим нашу СДУ.

## Модель первого типа


```python
r=odeint(dr,r0,tetha)
r1, tetha1 = cart2pol(t, f2(t))
r2, tetha2 = cart2pol(t1, f3(t1))

for i in range(len(tetha)):
    if abs(tetha1[i]-tetha[i]) <= 0.001:
        r_ins = tetha[i]
        tetha_ins = r[i][0]

plt.polar(tetha,r,'g')
plt.polar(tetha2, r2, 'g')
plt.polar(tetha1,r1,'b')
plt.polar(r_ins, tetha_ins, color='firebrick', marker='o', label='crosspoint')
plt.legend(loc='lower left')

print('crosspoint: r='+str(r_ins)+', tetha='+ str(tetha_ins))
```

`output: crosspoint: r=1.048, tetha=2.6145016420772618`


## Модель первого типа

    
![График первого случая](image/output_14_1.png){ #fig:001 width=60% }

## Модель второго типа


```python
x2 = k/(n-1)
r0 = x2

tetha0 = -math.pi
tetha = np.arange(tetha0, -tetha0, 0.001)
r=odeint(dr,r0,tetha)
```


```python
t1=np.arange(-k, -r0, 0.01)
t=np.arange(0,10,0.001)
```

## Модель второго типа


```python
r1, tetha1 = cart2pol(t, f2(t))
r2, tetha2 = cart2pol(t1, f3(t1))

for i in range(len(tetha)):
    if abs(tetha1[i]-tetha[i]) <= 0.001:
        r_ins = tetha[i]
        tetha_ins = r[i][0]

plt.polar(tetha,r,'g', label='police')
plt.polar(tetha2, r2, 'g')
plt.polar(tetha1,r1,'b', label='pirates')
plt.polar(r_ins, tetha_ins, color='firebrick', marker='o', label='crosspoint')
plt.legend(loc='lower left')

print('crosspoint: r='+str(r_ins)+', tetha='+ str(tetha_ins))
```

`output: crosspoint: r=1.047407346409745, tetha=14.888261569723666`

## Модель второго типа

![График второго случая](image/output_18_1.png){ #fig:002 width=60% }

# Выводы

Мы изучили теорию о задаче о погоне, а также успешно реализовали 2 модели для решения этой задачи.