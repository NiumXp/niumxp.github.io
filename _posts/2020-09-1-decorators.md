---
layout: post
title:  "Decoradores em Python"
categories: Python
permalink: /python/decorators
---

Decoradores são objetos que transformam, embrulham e/ou não fazem nada na declaração de outros objetos - apenas em funções e classes.

Assim como o [*Iterator*](https://refactoring.guru/pt-br/design-patterns/iterator) - que eu citei no último post sobre [o básico de geradores](https://niumxp.github.io/python/basic-of-generators), o [*Decorator*](https://refactoring.guru/pt-br/design-patterns/decorator) também é um padrão de projeto [sintaticamente açucarado](https://pt.wikipedia.org/wiki/Açúcar_sintático) em Python para facilitar muita coisa e deixar o código beeem mais bonito!

Você provavelmente já deve ter visto algum decorador em alguma classe por aí. Da uma olhada no `@property` do seguinte código:

```py
import math


class Person:
    def __init__(self, name, life):
        self.name = name
        self.life = life

    @property
    def dead(self):
        return math.ceil(self.life) < 1
```

Aquilo é um decorador. Sempre que houver **nomes prefixados com `@` acima de uma declaração de função ou classe**, será um decorador em ação!

> Quando o objeto `property` é decorado em algum método - ou em algum objeto -, ele é embrulhado para dentro de um descritor especial - escrito totalmente em C.

### Mais exemplos
Decoradores são muito bonitos e estes simples exemplos aqui não poderiam ficar de fora!

```py
@perfect
class Shrimp:
    pass


@very_bad
@horrible
def pudding():
    pass
```

> Mostrando dessa forma nem da pra se imaginar o quão feio decoradores podem ficar...

Podemos decorar uma classe e função com diversos decoradores! - o máximo de decoradores que eu cheguei a ver sendo utilizado na mesma declaração foi em torno de 6 ou 7, é bem dificil ultrapassar de 3 decoradores.

### E como essa **maravilha** funciona!!?
Ótima pergunta meu caro editor de texto! Quando escrevemos uma função decorada que nem esta aqui:

```py
@lovely
def nium():
    pass
```

Estamos na verdade escrevendo de forma mais bonita ou fácil o seguinte:

```py
def nium():
    pass


nium = lovely(nium)
```

Ou seja, criamos o objeto da função (linha 1 até 2), passamos pro decorador (linha 4) e o que ele retornar será salvo no nome da função (linha 4).

Bem simples, não? Não? Vamos entender melhor decorando uma função com `print` então!

```py
@print
def pudding():
    pass


print(pudding)
```
```
<function pudding at 0x0000021A16AFEF70>
None
```

A função `pudding` foi criada e passada para o `print`, que logo foi mostrada no terminal, daí o `print` retornou `None`, que foi salvo em `pudding`, depois a gente imprime o valor de `pudding` que é `None`.
Ou seja, em outras palavras ("códigos" soaria estranho):

```py
def pudding():
    pass


pudding = print(pudding)
print(pudding)
```

Com vários decoradores podemos usar a mesma ideia, saca só:

```py
@bool
@print
def pudding():
    pass
```

É a mesma coisa que

```py
def pudding():
    pass


puddinng = bool(print(pudding))
```

### Mão na massa ~~adoro pizza~~
É possível criar um decorador para diversas coisas e, por que não um que imprime o retorno de uma função?

```py
def imprimir(function):
    def embrulho(*args):
        result = function(*args)
        print(result)

    return embrulho


@imprimir
def somar(x, y):
    return x + y


somar(1, 1)  # 2
somar(2, 5)  # 7
```

---

É bem comum vermos [*closure*](https://en.wikipedia.org/wiki/Closure_(computer_programming))s, [*wrapper*](https://en.wikipedia.org/wiki/Wrapper_function)s e [funções aninhadas](https://en.wikipedia.org/wiki/Nested_function) dentro de decoradores, provavelmente você irá ver muito um código no seguinte estilo dentro deles:

```py
def decorator(function):
    def wrapper(*args, **kwargs):
        return function(*args, **kwargs)

    return wrapper
```

Basicamente o `wrapper` embrulha `function` para fazer algo a mais.

---

Fazer o embrulhamento de uma função dentro de um decorador e o retornar  significa mudarmos a função que nos foi passada por outra e, se precisarmos acessar a documentação de `function` por exemplo, só conseguiremos acessar a de `wrapper`.
Para evitar esse problema existe o decorador `wraps` da biblioteca `functools`, que é sensacional!!

```py
from functools import wraps


def decorator(function):
    @wraps(function)
    def wrapper():
        return function()

    return wrapper


@decorator
def pudding():
    pass


print(pudding.__name__)  # pudding
```

Tente remover a linha 5 e ver o que acontece!

### Decoradores com parâmetros...?
É possível passar parâmetros para os decoradores - na verdade, o que acontece é que nós podemos resolver uma função na sintaxe do decorator, resultando geralmente em uma *closure*, que acaba sendo o decorador de fato - para que possamos mudar o comportamento dele, por exemplo.

Imagine que queiramos criar um decorador que embrulhe uma função fazendo com que ela retorne o que passarmos e o que foi executado por ela mesma. Teriamos algo mais ou menos assim:

```py
def retornar(valor):
    def decorator(function):
        def wrapper(*args, **kwargs):
            resultado = function(*args, **kwargs)
            return valor, resultado

        return wrapper
    return decorator
```

Basicamente criamos uma função chamada `retornar` que recebe `valor` e, nos retorna uma outra função chamada `decorator` que recebe uma função e a embrulha com outra função fazendo que com que ela retorne `valor` e o próprio resultado da função embrulhada.

```py
@retornar(2)
def somar(x, y):
    return x + y


resultado = somar(2, 4)
print(resultado)  # (2, 6)
```

Essa questão de "decoradores com parâmetros" são bem difíceis de se entender na primeira vez, tente colocar alguns `print`s e/ou até tentar desfazer o decorador para entender melhor o fluxo de execução!

### Suco de laranja
A partir do Python 3.9, a gramática dos decoradores foram relaxadas, sendo possível passar expressões ao invés de apenas nomes e argumentos - igual à chamada de uma função.

```py
@[
    10, 25,
    30, 0, 100,
    lambda _: lambda: print(_.__name__, _(), sep='')
][-1]
def Nium():
    return "Xp"


Nium()  # Imprime NiumXp no terminal
```

Esse exemplo é bem tosco, mas da pra ter uma ideia do quão estranho funções e/ou classes podem aparecer com decoradores daqui pra frente.

Claro que a proposta dessa mudança não tem nada a ver com o que eu mostrei. Existem listas de decoradores em algumas bibliotecas e, não é/era fácil de se utilizar, tinham de ser feitos coisas bem estranhas...

```py
def decorate(index, sequence, f):
    return f(sequence[index])


@decorate(5, buttons, lambda i: i.events.click)
def on_click(button):
    pass
```

Ficava/Fica muito ruim de se ler, além de que vários decoradores eram/são criados... Com essa mudança,podemos deixar o código da seguinte maneira:

```py
@buttons[5].button.events.click
def on_click(button):
    pass
```

Bem mais bonito!


> Da uma olhada na [PEP-0614](https://www.python.org/dev/peps/pep-0614/) para entender mais a fundo sobre essa mudança.

### Classes como decoradores
Utilizar uma classe como decorador é bem simples, podemos simplesmente utilizar `__init__` para receber os argumentos e o `__call__` para preparar o decorador. Também é possível utilizar o `__new__`, mas acaba sendo mais difícil e bem específico seu uso.

> Não consegui pensar em um exemplo legal para por aqui!!

## Curiosidade
A [PEP-0318](https://www.python.org/dev/peps/pep-0318/) mostra algumas propostas de sintaxes para os decoradores e...

```py
def @classmethod foo(arg1,arg2):
    pass


def foo @classmethod (arg1,arg2):
    pass
```

...já imaginou se viesse dessa forma para o Python? Seria beeem estranho.
