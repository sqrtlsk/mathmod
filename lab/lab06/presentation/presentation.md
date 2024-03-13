---
## Front matter
lang: ru-RU
title: Лабораторная работа №6
subtitle: Задача об эпидемии
author:
  - Камкина А. Л.
institute:
  - Российский университет дружбы народов, Москва, Россия

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
toc-title: Содержание
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
 - '\makeatletter'
 - '\beamer@ignorenonframefalse'
 - '\makeatother'
---

# Информация

## Докладчик

:::::::::::::: {.columns align=center}
::: {.column width="70%"}

  * Камкина Арина Леонидовна
  * студентка
  * Российский университет дружбы народов
  * [1032216456@pfur.ru](mailto:1032216456@pfur.ru)
  * <https://alkamkina.github.io/ru/>

:::
::: {.column width="25%"}

![](./image/me.jpg)

:::
::::::::::::::


## Цель работы

Построить графики к своей задаче об эпидемии, используя языки Julia и OpenModelica.

---
## Задача об эпидемии
Рассмотрим простейшую модель эпидемии. Предположим, что некая
популяция, состоящая из $N$ особей, (считаем, что популяция изолирована) подразделяется на три группы. Первая группа - это восприимчивые к болезни, но пока здоровые особи, обозначим их через $S(t)$. Вторая группа – это число инфицированных особей, которые также при этом являются распространителями инфекции, обозначим их $I(t)$. А третья группа, обозначающаяся через $R(t)$ – это здоровые особи с иммунитетом к болезни.

---
## Задача об эпидемии
До того, как число заболевших не превышает критического значения $I*$, считаем, что все больные изолированы и не заражают здоровых. Когда
$I(t)>I*$, тогда инфицирование способны заражать восприимчивых к болезни особей. 
Таким образом, скорость изменения числа $S(t)$ меняется по следующему
закону:
$$\begin{cases}
\dfrac{dS}{dt} = -aS, если I(0)<=I*\\ 
\dfrac{dS}{dt} = 0, если I(0)>I*
\end{cases}$$

---
## Задача об эпидемии
Поскольку каждая восприимчивая к болезни особь, которая, в конце концов,
заболевает, сама становится инфекционной, то скорость изменения числа
инфекционных особей представляет разность за единицу времени между
заразившимися и теми, кто уже болеет и лечится, т.е.:
$$\begin{cases}
\dfrac{dI}{dt} = aS - bI, если I(0)<=I*\\ 
\dfrac{dI}{dt} = -bI, если I(0)>I*
\end{cases}$$

---
## Задача об эпидемии
А скорость изменения выздоравливающих особей (при этом приобретающие
иммунитет к болезни)
$$\begin{cases}
\dfrac{dR}{dt} = bI
\end{cases}$$

Постоянные пропорциональности
$a = 0.03$ и $b = 0.07$, - это коэффициенты заболеваемости
и выздоровления соответственно, которые я выставила самостоятельно.

# Выполнение лабораторной работы
### Создание проекта (код на Julia) при I(0)<=I*
```
using Plots
using DifferentialEquations

N = 11300
S = N - I - R
I = 240
R = 46
p = [0.03, 0.07]
u = [S, I, R]
tspan = (0.0, 100.0)

function f(du, u, p, t)
    a, b = p
    S, I, R = u
    du[1] = 0
    du[2] = -b*d\u[2]
    du[3] = b*u[2]
end
prob1 = ODEProblem(f, u, tspan, p)
sol1 = solve(prob1, Tsit5())
plot(sol1, label = ["S" "I" "R"])
```
Полученный график при I(0)<=I*(рис. @fig:001).
![График при I(0)<=I* Julia](image/j1.png){#fig:001 width=70%}
### Создание проекта (код на Julia) при I(0)>I*
```
using Plots
using DifferentialEquations

N = 11300
S = N - I - R
I = 240
R = 46
p = [0.03, 0.07]
u = [S, I, R]
tspan = (0.0, 100.0)

function f1(du, u, p, t)
    a, b = p
    S, I, R = u
    du[1] = -a*u[1]
    du[2] = a*u[1]-b*u[2]
    du[3] = b*u[2]
end
prob1 = ODEProblem(f1, u, tspan, p)
sol1 = solve(prob1, Tsit5())
plot(sol1, label = ["S" "I" "R"])
```
Полученный график при I(0)>I*(рис. @fig:002).
![График при I(0)>I* Julia](image/j2.png){#fig:001 width=70%}

---
### Создание проекта (код на OpenModelica) при I(0)<=I*
```
model lab_06
parameter Real N = 11300;
parameter Real a = 0.03;
parameter Real b = 0.07;
Real S(start = N-240-46);
Real I(start = 240);
Real R(start = 46);

equation
der(S) = 0;
der(I) = -b*I;
der(R) = b*I;


end lab_06;
```
Полученный график при I(0)<=I*(рис. @fig:001).
![График при I(0)<=I* OpenModelica](image/o1.png){#fig:003 width=70%}
### Создание проекта (код на OpenModelica) при I(0)>I*
```
model lab_06
parameter Real N = 11300;
parameter Real a = 0.03;
parameter Real b = 0.07;
Real S(start = N-240-46);
Real I(start = 240);
Real R(start = 46);

der(S) = -a*S;
der(I) = a*S-b*I;
der(R) = b*I;

end lab_06;
```
Полученный график при I(0)>I*(рис. @fig:002).
![График при I(0)>I* OpenModelica](image/o2.png){#fig:004 width=70%}
---

# Вывод
В процессе выполнения данной лабораторной работы я построила графики, используя Julia и OpenModelica, а также приобрела первые практические навыки работы с Julia и OpenModelica.