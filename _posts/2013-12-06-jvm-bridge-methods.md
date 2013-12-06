---
layout: post
title: "JVM Bridge methods"
category: 
tags: [java, jvm, bytecode, low level]
---
{% include JB/setup %}

Siempre he defendido que la máquina virtual de Java (JVM en adelante) es uno de los mejores <i>managed runtimes</i> que nos podemos encontrar en la actualidad: disponible en múltiples plataformas, alto rendimiento, gran variedad de lenguajes disponibles (Java, Scala, Clojure, ...), compatibilidad hacia atrás (parece ser que a partir de Java 9 esto ya no va a ser así), infinidad de herramientas de monitorización o detección de problemas entre otras muchas.

Como no podía ser de otro modo, también presenta alguna serie de desventajas como las diferencias semánticas entre lenguajes y la máquina virtual (por la cual algunos lenguajes como Scala se ven especialmente afectados) o situaciones de <i>stop of the world</i> durante la recolección de basura (a no ser que utileceis alternativas como Azul VM que ofrecen una recolección de basura <i>pauseless</i>	).

Durante esta entrada, y puede que alguna más, trataré de analizar cómo una de esas diferencias semánticas entre el lenguaje (Java en este caso) y la máquina virtual afecta a la inclusión de nuevas funcionalidades o incluso al modo en el que escribimos nuestros programas. En concreto durante esta entrada analizaremos <b>bridge methods</b>

Con casi total seguridad muchos de nosotros hemos escrito en infinidad de ocasiones un código similar al que se muestra a continuación:

{% highlight java %}
class User {
	public User getUser() {
		return new User("User");
	}
}

class ExtendedUser extends User {
	@Override
	public ExtendedUser getUser() {
		return new ExtendedUser("ExtendedUser", "extendeduser@extendeduser.com");
	}
{% endhighlight %}

Una clase hija que sobreescribe un método disponible en una de sus clases padre haciendo el tipo de retorno más específico (<b>covariant override</b> es el término exacto). Los más experimentados recordarán que este código no compilaba en versiones anteriores a Java 1.5.

Por desgracia para mi, habitualmente intento comprender cómo funcionan las cosas así que reviso la especificación de la JVM en lo relativo a las reglas de sobreescitura de métodos encontrándome con lo siguiente:

Un método m2 declarado en una clase C sobreescribe a un método m1 declarado en una clase A <b>si y solo si</b> se cumplen las siguientes condiciones:

* C es una subclase de A
* m1 y m2 tienen nombre y descriptores idénticos
* Y se cumple cualquiera de los dos siguientes
** m1 es un método público o protegido (o si pertenece al mismo paquete y no está definido como público, protegido ni privado)
** m2 sobreescribe un método m3, siendo m3 distinto de m1 y m2, de tal modo que m3 sobreescribe a m1.

En el ejemplo que nos ocupa es evidente que la segunda regla no se cumple puesto que el método <code>getUser()</code> presenta descriptores diferentes (ocasionado por retornar un método más específico en la clase hija). En este caso, para la JVM estos dos métodos no están relacionados de ninguna manera. ¿Cómo funciona esto entonces?

En el caso de Java, el compilador (<b>javac</b>) genera un método sintético que hace de puente entre las dos signaturas del método __getUser()__ ayudando en la realización del dispatching adecuado.

{% highlight java %}
synthetic bridge User getUser() {
	return ((ExtendedUser)this).getUser();
}
{% endhighlight %}

Estos métodos son generados en tiempo de compilación y establecen un nexo entre el sistema de tipos de Java y la JVM. Analizando el <i>bytecode</i> de nuestro ejemplo, nos encontramos con que el compilador ha generado ese método en nuestro <i>classfile</i>

{% highlight java %}
public io.github.migue.jvm.bridge.User getUser();
flags: ACC_PUBLIC, ACC_BRIDGE, ACC_SYNTHETIC
Code:
  stack=1, locals=1, args_size=1
     0: aload_0
     1: invokevirtual #8                  // Method getUser:()Lio/github/migue/jvm/bridge/ExtendedUser;
     4: areturn
  LineNumberTable:
    line 31: 0
{% endhighlight %}

Como ya hemos mencionado anteriormente, el origen de este artefacto son las diferencias semánticas entre el lenguaje y la máquina virtual, y aunque se podrían utilizar otro tipo de alternativas como <b>invokestatic</b> o duplicar el código (entre otras muchas), el objetivo es delegar todo el proceso de enlazado al <i>runtime</i>.

No cabe duda que se trata de una característica muy interesante, pero el uso de este enfoque implica una serie de consecuencias que deben ser tenidas muy en cuenta:

 * Rompen la relación 1:1 existente entre el archivo de código fuente y el archivo <i>classfile</i>. En nuestra definición de la clase tenemos un método mientras que en el archivo compilado ahora tenemos dos.
 
 * Maquillan los <i>stacktraces</i> , mostrando información que no tiene una correspondencia directa en nuestro código fuente.
 
 * Generan APIs conflictivas. Podemos utilizar <i>java.lang.reflect.Method.isBridge()</i> para saber si dicho método es o no un <b>bridge</b>. ¿Y que significa esto? Nada en absoluto.

 * Si generamos nuestro propios <i>classfiles</i> (una técnica muy extendida en los días que corren) nadie nos prohibe establecer nuestros métodos como <i>bridge methods</i>. Esto implica que no podemos fiarnos puesto que alguien puede haber generado una clase, marcando sus métodos como <i>bridge methods</i> cuando no es cierto que lo sean.

 * Estos métodos son accesibles desde <b>JVMTI</b> con lo cual podríamos escribir un agente que modificara este tipo de métodos, alterando la semántica del lenguaje.

 Como hemos podido ver, los <b>bridge methods</b> establecen un nexo de colaboración entre el lenguaje y el <i>runtime</i> permitiendo al primero de ellos exponer la capacidad de realizar un proceso de <i>covariant override</i>. Sin embargo, y aunque se trata de una gran característica, la implementación acarrea una serie de inconvenientes que debemos tener en cuenta.

 En la siguiente entrada descubriremos cómo este tipo de métodos afectan a la compilación independiente o a la inclusión de nuevas características como los <b>default methods</b>