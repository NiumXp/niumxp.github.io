---
layout: post
title:  "typing.Optional"
categories: Python
permalink: /python/typing-Optional
---

# typing.Optional


Muita gente acaba utilizando e dizendo que `typing.Optional` - em funções - é para dizer que o parâmetro é opcional.
De fato é, mas dizem no sentido de ter um valor padrão, o que está totalmente errado!

```py
def pow(x, y: Optional[int] = 2) -> int:
    return x*y
```

A assinatura está totalmente errada em comparação à implementação!
Você pode até pensar que está certa dizendo algo como:
> `y` é uma parâmetro opcional, se você não passa-lo, ele receberá `2`.

..mas não tem nada a ver!

### Um `Optional[T]` é equivalente à `Union[T, None]`
> Podemos dizer apenas `T | None` e, apartir do Python 3.10 usar dessa forma nos códigos!

Ou seja, a assinatura anterior nos diz que podemos passar `None` ao parâmetro `y`.
Mas a implementação claramente daria erro, então, a assinatura correta para a implementação seria:

```py
def pow(x, y: int = 2) -> int:
```


Perfeito! Mas e como ficaria a implementação com um `Optional[T]`? Muito simples.

```py
def pow(x, y: Optional[int] = None) -> int:
    if y is None:
        return x

    return x*y
```


Quando anotamos um parâmetro dizendo que ele será opcional, estamos na verdade dizendo que a função se comportará totalmente diferente com ou sem o parâmetro -
como se fossem *flags* de CLIs, tipo o `-v` do `docker-compose up`!
