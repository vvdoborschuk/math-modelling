---
title: "Лабораторная работа №5. Модель хищник-жертва"
author: [Доборщук Владимир Владимирович]
institute: "RUDN University, Moscow, Russian Federation"
date: "13 марта 2021"
subtitle: "c/б 1032186063 | НФИбд-01-18"
keywords: [Моделирование, Лабораторная]
lang: "ru"
toc-title: "Содержание"
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
fontsize: 12pt
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: Fira Sans
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase
titlepage: true
titlepage-text-color: "000000"
titlepage-rule-color: "1A1B35"
titlepage-rule-height: 2
listings-no-page-break: true
indent: true
header-includes:
  - \usepackage{sectsty}
  - \sectionfont{\clearpage}
  - \linepenalty=10 # the penalty added to the badness of each line within a paragraph (no associated penalty node) Increasing the value makes tex try to have fewer lines in the paragraph.
  - \interlinepenalty=0 # value of the penalty (node) added after each line of a paragraph.
  - \hyphenpenalty=50 # the penalty for line breaking at an automatically inserted hyphen
  - \exhyphenpenalty=50 # the penalty for line breaking at an explicit hyphen
  - \binoppenalty=700 # the penalty for breaking a line at a binary operator
  - \relpenalty=500 # the penalty for breaking a line at a relation
  - \clubpenalty=150 # extra penalty for breaking after first line of a paragraph
  - \widowpenalty=150 # extra penalty for breaking before last line of a paragraph
  - \displaywidowpenalty=50 # extra penalty for breaking before last line before a display math
  - \brokenpenalty=100 # extra penalty for page breaking after a hyphenated line
  - \predisplaypenalty=10000 # penalty for breaking before a display
  - \postdisplaypenalty=0 # penalty for breaking after a display
  - \floatingpenalty = 20000 # penalty for splitting an insertion (can only be split footnote in standard LaTeX)
  - \raggedbottom # or \flushbottom
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
...

# Цели и задачи

**Цель:** изучить модель "хищник - жертва" (модель Лотки-Вольтерры) и реализовать процесс моделирования с помощью программных средств.

**Задачи:**

* изучить теорию о модели "хищник - жертва"
* построить модель "хищник - жертва":
  * построить графики изменения численности хищников, жертв
  * построить график зависимости численности жертв и хищников
  * найти стационарное состояние данной модели

# Теоретическая справка

Простейшая модель взаимодействия двух видов типа «хищник — жертва» - модель Лотки-Вольтерры. Данная двувидовая модель основывается на следующих предположениях

1. Численность популяции жертв $x$ и хищников $y$ зависят только от времени (модель не учитывает пространственное распределение популяции на занимаемой территории)

2. В отсутствии взаимодействия численность видов изменяется по модели Мальтуса, при этом число жертв увеличивается, а число хищников падает

3. Естественная смертность жертвы и естественная рождаемость хищника считаются несущественными

4. Эффект насыщения численности обеих популяций не учитывается

5. Скорость роста численности жертв уменьшается пропорционально численности хищников

$$
\begin{cases}
    \frac{dx}{dt} = ax(t)-bx(t)y(t)\\
    \frac{dy}{dt} = -cy(t)+dx(t)y(t)
\end{cases}
$$

В этой модели $x$ – число жертв, $y$ - число хищников. Коэффициент $a$ описывает скорость естественного прироста числа жертв в отсутствие хищников, c - естественное вымирание хищников, лишенных пищи в виде жертв. Вероятность взаимодействия жертвы и хищника считается пропорциональной как количеству жертв, так и числу самих хищников ($xy$). Каждый акт взаимодействия уменьшает популяцию жертв, но способствует увеличению популяции хищников (члены $-bxy$ и $dxy$ в правой части уравнения). 

# Программная реализация

## Подготовка к моделировнию

Все данные соответствуют варианту 14 = $(1032186063\mod{70}) + 1$.

**Инициализация библиотек**


```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.integrate import odeint
```

**Начальные данные и необходимые функции**

Введём соответствующие нашему варианту начальные данные для построения модели:


```python
a = 0.77
b = 0.077
c = 0.33
d = 0.033

v0 = [4, 9] # x0, y0
t = np.arange(0, 400, 0.1)
```

Создадим функцию для нашей СДУ:


```python
def syst(x, t):
    dx1 = -a*x[0] + b*x[0]*x[1]
    dx2 = c*x[1] - d*x[0]*x[1]
    return [dx1, dx2]
```

Воспользуемся функцией `odeint` из модуля `scipy.integrate` и решим нашу СДУ, после чего выделим значения популяции жертв (`y1`) и хищников (`y2`)


```python
y = odeint(syst, v0, t)
y1 = y[:, 0] # значения популяции жертв
y2 = y[:, 1] # значения популяции хищников
```

## Построение графиков для модели

### Графики численности хищников, жертв

С помощью модуля `matplotlib.pyplot` построим графики изменения численности хищников и жертв.


```python
plt.title('Victims population')
plt.plot(t, y1)
```

    
![График изменения численности жертв](image/output_19_1.png)
    



```python
plt.title('Predators population')
plt.plot(t, y2)
```

    
![График изменения численности хищников](image/output_20_1.png)
    



```python
plt.title('Victims and Predators population')
plt.plot(t, y1, label='victims')
plt.plot(t, y2, label='predators')
plt.legend(loc='upper right')
```

    
![График изменения численности жертв и хищников](image/output_21_1.png)
    


### График зависимости численности хищников от численности жертв

На следующем этапе нам необходимо вычислить точку стационарного состояния системы $\left(x_0 = \frac{c}{d}, y_0 = \frac{a}{b}\right)$. После её нахождения - построим график зависимости численности хищников от численности жертв.


```python
x0 = c/d
y0 = a/b

plt.title('Dependece of predators from victims')
plt.plot(y1, y2, 'steelblue')
plt.plot(x0, y0, color='firebrick', marker='o', label='Стационарное значение')
plt.legend()

print('Точка стационарного значения: ('+ str(x0)+', '+ str(y0)+ ')')
```

Точка стационарного значения: (10.0, 10.0)
    
![График зависимости численности хищников от жертв](image/output_24_1.png)
    


# Выводы

Была успешно изучена теорию о модели "хищник - жертва", после чего были грамотно реализованы графики изменения популяции хищников и жертв, график зависимости количества хищников от жертв и была найдена точка стационарного состояния системы. Реализация делалась на языке Python.