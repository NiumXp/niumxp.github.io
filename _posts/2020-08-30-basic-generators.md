
---
layout: post
title:  "O básico de geradores em Python"
categories: Python
permalink: /python/basic-of-generators
---

Geradores parecem ser complicados, mas não são não, são apenas objetos (ou valores) que literalmente geram outros objetos (valores).

Geradores são realmente uteis? Muito! Tenho certeza de que você já fez algo assim:
```py
list_ = ["Nium", "Xp",]

for index, element in enumerate(list_):
    print(index, element)
```

> Quando queremos criar uma variável com um nome *built-in* ou uma palavra reservada, sempre sufixamos com `_`, por isso o `list_`, já existe o nome `list`.

### Tá, mas e aí...?
Bom, imagine que precisemos fazer uma operação nos `x` primeiros itens de uma lista e posteriormente nos próximos que aparecerem.

Você pensou em criar uma variável contadora certo? Fazer isso é um grande problema, teria de haver um monte de `ifs` checando essa variável pra fazer o que queremos, além de ter vários `counter += 1` também...

Para evitar isso, existe um padrão de projeto chamado [*Iterator*](https://refactoring.guru/pt-br/design-patterns/iterator) que já está [sintaticamente açucarado](https://pt.wikipedia.org/wiki/Açúcar_sintático) inserido na linguagem.

Basicamente, *Iterators* guardam estados sobre *Iterables* (iteravéis) - você pode entender esse tal estado como um contador.

```py
distance = range(1, 10)
iterator = iter(distance)

next(iterator)  # 1
next(iterator)  # 2

number = next(iterator)
print(number)  # 3
```

Aquele `iter` é de *iterate*, ele irá retornar um objeto iterador sobre o nosso `range(0, 10)` - e por curiosidade, o iterador retornado se chama `range_iterator` e é escrito totalmente em C para ter uma performance maior, outros objetos *built-in*s também tem esse carinho especial.

Assim que escrevemos `next(iterator)` estavamos dizendo para o iterador ir para o próximo estado - que por consequência retorna o valor desse estado se não estiver exausto. Na primeira execução fomos para o primeiro estado e tivemos o valor `1`, no segundo o valor `2` e no terceiro o valor `3`.

### O que acontece quando chegamos no último valor de um iterador?
Simples, ele fica exausto e enraiza um erro. Por exemplo:
```py
distance = range(3)
iterator = iter(distance)

for number in range(5):
    next(iterator)
    print(number)
```
Se você rodar esse código, a saída esperada é a seguinte:
```
0
1
2
Traceback (most recent call last):
  File "d:\niumxp.github.io\_.py", line 5, in <module>
    next(iterator)
StopIteration
```

### E como eu crio um gerador?
Usando funções! Também há outras formas, mas não vem ao caso.

Da uma olhada nessa função que geradora:
```py
def generator():
    yield 3
    yield 0
    yield 7
    yield 5
```
Essa palavra `yield` significa produzir, tecnicamente dizemos para a nossa função produzir numeros.
Assim que utilizamos essa palavra dentro de uma função, ela automaticamente retorna um gerador - nada é executado dentro dele até mudarmos consumirmos aos poucos o gerador.

Ainda é possível utilizar `return` dentro da função, mas não terá o mesmo efeito que estamos acostumados. Usar um `return` irá exaustar o gerador, se tentarmos consumir ele, teremos o erro `StopIteration` mostrando o que pedimos para retornar.

Vamos executar o gerador em um `for` e ver o que acontece.
```py
for number in generator():
    print(number)
```
```
3
0
7
5
```

Nada de surpreendente né? Só que agora vamos fazer com esta função geradora aqui:

```py
def greetings():
    print("O Hi irá ser mostrado!")
    yield "Hi"

    print("O Hi foi mostrado e o Hey irá aparecer!")
    yield "Hey"

    print("E agora??? Cadê?")
    return "Hello"
```

É, nada de demais, tem só alguns `yield`s e um `return` no final, bem tranquilo.
Vamos rodar desta forma e ver o que acontece.

```py
iterator = greetings()
print(next(iterator))

print("Opa, olha que legal!")

next(iterator)

print("Xii")
next(iterator)
```
```
O Hi irá ser mostrado!
Hi
Opa, olha que legal!
O Hi foi mostrado e o Hey irá aparecer!
Xii
E agora??? Cadê?
Traceback (most recent call last):
  File "d:\niumxp.github.io\_.py", line 20, in <module>
    next(iterator)
StopIteration: Hello
```
Bem legal, não? Como não? Se você reparar, assim que damos um passo usando `next`, a execução da função para em um `yield`!

## Para que geradores são úteis?
> Eu realmente espero que você não tenha feito essa pergunta durante a leitura...

São úteis para diversas coisas, principalmente para operações *lazy* e é a base de toda concorrência em Python.
