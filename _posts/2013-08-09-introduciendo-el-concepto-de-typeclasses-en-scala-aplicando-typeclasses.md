---
layout: post
title: "Introduciendo el concepto de typeclasses en Scala: aplicando typeclasses"
category: 
tags: [scala, jvm, typeclasses, programming]
---
{% include JB/setup %}

A lo largo de la [primera](http://migue.github.io/2013/08/04/introduciendo-el-concepto-de-typeclasses-en-scala-i/) y [segunda](http://migue.github.io/2013/08/07/introduciendo-el-concepto-de-typeclasses-en-scala-aproximacin-tradicinal/) de la serie sobre `typeclasses` hemos presentando el concepto de las mismas así como un problema sencillo que hemos resuelto utilizando un enfoque "tradicional".

La solución adoptada en nuestro primer intento nos había dado resultados no demasiado satisfactorios puesto que no habíamos conseguido mantener una implementación limpia de nuetro ADT, llegando a la conclusión de que un enfoque basado en __subtype polymorphism__ no satisfacía nuestros exigencias iniciales. Veamos si un enfoque __adhoc polymorphism__ nos ayuda a resolver nuestros problemas de un modo más elegante.

Nuestro primer paso será definir nuestro `typeclass`

{% highlight scala %}
trait Json[A] {
  def json(value: A): JsonValue
}
{% endhighlight %}

Tal y como ya habíamos dicho anteriormente, el enfoque utilizado en Scala está basado en la definición de un tipo genérico.

El siguiente paso que debemos acometer será la definición de un método en nuestra utilidad `JsonWriter`

{% highlight scala %}

object JsonWriter {
  ...

  def write(expression: Expression)(implicit converter:Json[Expression]): String = {
    write(converter.json(expression))
  }
}

{% endhighlight %}

El fragmento de código anterior estamos haciendo uso del mecanismo de [implicits](http://migue.github.io/2012/02/21/scala-implicits-simple-examples/) en el que, el compilador, de manera automática, buscará un elemento del tipo correspondiente y lo ubicará donde corresponda (en este caso como segundo argumento de la invocación del método `write`), permitiéndonos escribir código como el siguiente:

{% highlight scala %}

val sum: Expression = Sum(Number(1), Number(10))

JsonWriter.write(sum))

{% endhighlight %}

¿Cómo definimos el `converter` anterior? No necesitamos más que declarar, y poner en el contexto adecuado, un elemento del tipo `Json[Expression]` que será el encargado de convertir las expressiones en el modelo de objetos `JSON` correspondiente:

{% highlight scala %}

implicit val jsonConverter = new Json[Expression] {
    def json(value: Expression): JsonValue = value match {
      case Number(value) => JsonNumber(value)
      case Sum(left, right) => JsonObject(Map("operator" -> JsonString("+"), "left" -> json(left), "right" -> json(right)))
      case Minus(left, right) => JsonObject(Map("operator" -> JsonString("+"), "left" -> json(left), "right" -> json(right)))
    }
  }

{% endhighlight %}

Esta nueva aproximación basada en un enfoque __adhoc polymorphism__ nos ha permitido romper el acoplamiento descrito en la entrada anterior, obteniendo un modelo de clases más sencillo y limpio, evitando "contaminar" nuestro ADT como habíamos hecho en la solución propuesta anteriormente:

<img src='/images/TypeClassesBlogPost-TypeclassesApproachModel.png' alt="Diagrama representativo del modelo de clases construido en esta entrada" />

El uso de la aproximación anterior nos ha permitido llevar a cabo las mejoras que estábamos buscando, sin embargo, el parámetro implícito añadido en el método `write` de nuestra utilidad `JsonWriter` no me gusta demasiado así que vamos a intentar simplificarlo un poquito. Para ello, vamos a hacer uso del concepto `view context bound` ofrecido por Scala y vamos a reescribir nuestro método `write` del siguiente modo:

{% highlight scala %}

  def write[A: Json](value: A): String = {
    write(implicitly[Json[A]].json(value))
  }

{% endhighlight %}  

La aproximación anterior es la más utilizada cuando se plantea una solución basada en `typeclasses` en Scala y está basada en el mecanismo de `view context bounds`. De manera resumida, este mecanismo ofrecido por Scala se utiliza para declarar que, para un determinado tipo A, está disponible un valor implícito de tipo `B[A]`. Un ejemplo típico de uso de este mecanismo, dentro del propio Scala, es la inicialización de los arrays

{% highlight scala %}

def f[A : ClassManifest](n: Int) = new Array[A](n)

{% endhighlight %}
