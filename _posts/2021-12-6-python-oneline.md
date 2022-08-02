---
layout: post
title:  "Python em uma linha"
categories: Python
permalink: /python/one-line
---

Alguns *snippets* simples para escrever *code golf* em Python.

---

Escrever os nomes do métodos leva muito tempo, use o `assignment` a seu favor!

```python
S = [1, 2, 3]

_, *S = S       # list.pop(..., 0)
*S, _ = S       # list.pop(...)

S += 5,         # list.append(..., 5)
                # list.extend(..., (5,))

S[:2] += 3,    # list.insert(..., 2, 3)
S[:2] += [3]
```

---

Escreva nomes em uma linha de forma coerente!

```python
x, *y = 2,  # `y` será uma lista vazia!!

d, e, f = "def"

a, b, c = [0]*3  # Errado
a = b = c = 0    # Certo
```

---

Negar *bool*s é bem simples, eu particularmente gosto muito de fazer assim:
```python
print(True<1)   # False
print(False<1)  # True
```

Dentro de um `if`, usar `x<1` ocupa muito espaço, podemos fazer apenas `~-x`.
```python
x = False

if~-x: ...
if x<1: ...
```

> As vezes usar `if~x` pode ser suficiente

---

Condicionais são mais complexas de se comprimir, geralmente é melhor pensar em uma forma com o que você já tiver, mas pra te dar um norte, você pode usar a expressão `(x, y)[z]` onde `z` é um `bool` ou um `int` sendo `0` ou `1` - se for diferente, você receberá `IndexError` -, `x` corresponde `z` a `False` e `y`, `z` a `True`.

```python
c = lambda x, y, z: (x, y)[z]
print(j := c(5, 3, 5>3), j*j)  # 3 6
```

> Eu sei, eu sei... Com o *walrus* foi o único jeito que eu encontrei de deixar ambas linhas com o mesmo tamanho... Mentira! O `j*j` foi uma referência a você sabe o que - é, aquele anime das poses lá

Se você estiver mexendo com strings em sua forma literal - pelo menos uma -, é melhor utilizar a expressão `z*y or z`.
```python
z = True

n = z*"NiumXp"or"Nium"      # Certo
n = ("NiumXp","Nium")[z]    # Errado
```

> `False` é o mesmo que `0`, ao fazermos `0*"Nium"`, recebemos `''` e o `or` converte para `bool`, resultando em `False` novamente. Se `x` resultar em `False` na expressão `x or y`, `y` será retornado independentemente de seu resultado.

---

Converções de sequências também podem ser comprimidas!!!

```python
D = {1: 0,
     2: 0,
     3: 0}  # dict

T = (*D,),  # tuple
S = {*D}    # set
L = [*D]    # list
```

Essas conversões que eu mostrei serve para diversos casos, é possível reduzir no `assign` da seguinte forma:

```python
T = *D,  # tuple
*L, = D  # list
```

---

Pra finalizar, uma coisa muito importante é entender a gramática do Python.

Só é preciso de espaço em um identificador caso a outra coisa não seja um identificador!
```python
from.import*

while...:
while[1]:

""or[]and""
```

Se tiver *highlight*, estes ficarão errados, então decidi separa-los.

```
1or 2
0xfor 1in 2

[0xfor x in[2]]
```

A segunda e última linha desse último código são o mesmo que `15 or 1 in 2` e `15 or x in [2]
`, respectivamente.
