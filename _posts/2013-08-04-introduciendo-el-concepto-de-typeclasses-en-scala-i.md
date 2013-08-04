---
layout: post
title: "Introduciendo el concepto de typeclasses en Scala (I)"
category: 
tags: [scala, jvm, typeclasses, programming]
---
{% include JB/setup %}

Podríamos resumir las `typeclasses` en Scala como algo similar a los interfaces Java pero con una flexibilidad muy superior. En Scala definimos esta abstracción mediante el uso de `traits`

{% highlight scala %}
trait Json[A] {
  def convert(element: A): String
}
{% endhighlight %}

Nótese la ligera diferencia con el enfoque de `mixin traits`

{% highlight scala %}
trait Json {
  def convert(): String
}
{% endhighlight %}

Una vez tenemos definido nuestro nuevo `typeclass` necesitamos un mecanismo que nos permita añadir un nuevo tipo al mismo:

{% highlight scala %}
implicit val IntJson = new Json[Int] {
  def convert(element: Int): String = element.toString()
}
{% endhighlight %}

Una vez hemos definido nuestro nuevo comportamiento a través del anterior `typeclass`, y hemos construido un mecanismo que nos permite añadir nuevos tipos al mismo, ya estamos en disposición de utilizar nuestra nueva construcción. Para ello, haremos uso del mecanismo de `implicits` que Scala nos ofrece:

{% highlight scala %}
def convert[A](element: A)(implicit jsonConverter : Json[A]): String = jsonConverter.convert(element)
{% endhighlight %}

Personalmente, en lugar de utilizar el mecanismo anterior, perfiero una aproximiación utilizando __context bounds__ 

{% highlight scala %}
def convert[A : Json](element: A): String = implicitly[Json[A]].convert(element)
{% endhighlight %}

¿Qué hemos obtenido con el enfoque anterior? Básicamente podemos definir instancias fueras del propio tipo (en nuestro ejemplo anterior, `Int` no sabe nada acerca de `Json`), consiguiendo lo que se conoce como el __open world assumption__. Adicionalmente, y gracias al mecanismo de `implicits` podemos sobreescribir la instancia del `typeclass` simplemente poniendo una nueva instancia en el ámbito correcto.

Entrada cortita intencionadamente con el objetivo de no difuminar y/o esconder los conceptos básicos de las `typeclasses` así como las construciones que el lenguaje de programación Scala nos ofrece para poner en práctica esta aproximación. A lo largo de las siguientes entradas plantearemos un sencillo "problema", que resolveremos, de manera detallada, mediante un enfoque "más tradicional" así como mediante el uso de una aproximación basada en el concepto de `typeclasses` presentando en esta entrada.