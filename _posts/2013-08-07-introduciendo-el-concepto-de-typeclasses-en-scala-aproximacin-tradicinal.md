---
layout: post
title: "Introduciendo el concepto de typeclasses en Scala: aproximación tradicional"
category: 
tags: [scala, jvm, typeclasses, programming]
---
{% include JB/setup %}

En la [última entrada](http://migue.github.io/2013/08/04/introduciendo-el-concepto-de-typeclasses-en-scala-i/) describimos de manera muy somera el concepto de `typeclasses` así como los elementos básicos que el lenguaje de programación Scala nos ofrece para modelar dichos conceptos. Durante esta entrada y la próxima plantearemos un ejemplo más o menos completo que intentaremos resolver utilizando un enfoque "tradicional" así como una aproximación utilizando el concepto de `typeclasses` que nos ocupa.

Primera parte: ADT para expresiones
---------------------------------

Supongamos que estamos desarrollando un nuevo lenguaje de expresiones en el que deseamos soportar operaciones de suma y resta sobre números enteros. Una posible definición de este modelo sería algo como lo que sigue:

{% highlight scala %}
sealed trait Expression

case class Number(value: Integer) extends Expression
case class Sum(left: Expression, right: Expression) extends Expression
case class Minus(left: Expression, right: Expression) extends Expression

object ExpressionEvaluator {
  def evaluate(expression: Expression): Integer = expression match {
    case Number(value) => value
    case Sum(left, right) => evaluate(left) + evaluate(right)
    case Minus(left, right) => evaluate(left) + evaluate(right)
  }
}
{% endhighlight %}

El problema: representación de expresiones en diferentes formatos
-----------------------------------------------------------------

Una vez modelado nuestro lenguaje de expresiones se nos plantea el siguiente problema: ¿cómo representamos nuestras expresiones en diferentes formatos: JSON, XML, etc? Vamos a centrarnos en un formato determinado, por ejemplo JSON, con el objetivo de no perder el foco de nuestro principal tarea: construir un modelo claro, mantenible y extensible de conversión de nuestro modelo de expresiones.

Definamos nuestro modelo de representación de objetos JSON así como un método de utilidad que convierte un modelo de objetos JSON en su representación textual:

{% highlight scala %}
sealed trait JsonValue

case class JsonObject(entries: Map[String, JsonValue]) extends JsonValue
case class JsonArray(entries: Seq[JsonValue]) extends JsonValue
case class JsonNumber(value: Integer) extends JsonValue
case class JsonString(value: String) extends JsonValue

object JsonWriter {

  def write(json: JsonValue): String = json match {
    case JsonNumber(value) => value.toString()
    case JsonString(value) => value
    case JsonArray(entries) =>
      val convertedEntries = entries map write
      "[" + (convertedEntries mkString ",") + "]"
    case JsonObject(entries) =>
      val convertedEntries = for ((key, value) <- entries) yield key + ":" + write(value)
      "[" + (convertedEntries mkString ",") + "]"
  }
}
{% endhighlight %}

Primera aproximación: implementar la transformación en nuestro ADT de expresiones
---------------------------------------------------------------------------------

La primera aproximación que se nos puede ocurrir es la de incluir la conversión a JSON dentro de nuestro propio ADT. Para ello, el primer paso sería definir una abstracción que represente la capacidad de convertir un objeto a format JSON:

{% highlight scala %}
trait Json {
  def convert(): JsonValue
}
{% endhighlight %}

y hacer que todas las expresiones de nuestro ADT soporten esta nueva funcionalidad. Para conseguir esto, hacemos que nuestro modelo de expresiones hereden del `trait Json` definido anteriormente (los detalles de la conversión de expresiones a JSON son irrelevantes):

{% highlight scala %}
sealed trait Expression extends Json

case class Number(value: Integer) extends Expression {
  def convert(): JsonValue = JsonNumber(value)
}

case class Sum(left: Expression, right: Expression) extends Expression {
  def convert(): JsonValue = JsonObject(Map("operator" -> JsonString("+"), "left" -> left.convert(), "right" -> right.convert()))
}

case class Minus(left: Expression, right: Expression) extends Expression {
  def convert(): JsonValue = JsonObject(Map("operator" -> JsonString("-"), "left" -> left.convert(), "right" -> right.convert()))
}
{% endhighlight %}

El último paso necesario para completar nuestra implementación es incluir en nuestro helper `JsonWriter` un método que acepte un argumento de tipo `Json`:

{% highlight scala %}
object JsonWriter {
  ...

  def write(json: Json): String = {
    write(json.convert())
  }
}
{% endhighlight %}

Conseguido!! Una vez definido el modelo anterior ya podemos convertir nuestras expresiones a formato JSON: <img height='15' src='/images/icon-ok.png' width='15' alt="Conseguido">

{% highlight scala %}
val expression:Expression = Sum(Number(1), Number(10))

println(JsonWriter.write(expression))
{% endhighlight %}

Aunque parezca que hemos hecho un gran trabajo lo único conseguido hasta este momento es una solución bastante pobre al problema planteado: <img height='15' src='/images/icon-bad.png' width='15' alt="Conseguido">

* Uno de los objetivos iniciales era mantener nuestro ADT tan "limpio" como nos fuera posible, algo que, a la vista de la solución propuesta, no estamos consiguiendo.

* Nuestra solución pasa porque nuestras expresiones extiendan del `trait Json`, algo factible puesto que se trata de un modelo creado por nosotros mismos. Pero que pasaría si el modelo de expresiones no es controlado por nosotros sino que se trata de una librería de terceros en la que el tipo 'Expression' no pudiera heredar del tipo `Json`

* La apuesta por resolver nuestro problema mediante un enfoque de __subtype polymorphism__ no ha resultado completamente satisfactoria. Hemos creado un acoplamiento notable entre todas las clases de nuestro modelo y necesitamos buscar una solución basada en un enfoque de __adhoc polymorphism__ para intentar romper dicho acoplamiemto.

<img src='/images/TypeClassesBlogPost-TraditionalApproachModel.png' alt="Diagrama representativo del modelo de clases construido en esta entrada"/>


En la próxima entrada analizaremos una alternativa diferente, basada en `typeclasess`, que nos permitirá romper el acoplamiento mostrado en el diagrama anterior.
