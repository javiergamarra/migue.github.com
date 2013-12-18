---
layout: post
title: "JVM Bridge methods: procesos de compilacion independiente"
category: 
tags: [java, jvm, bytecode, low level]

---
{% include JB/setup %}

En la [entrada anterior](http://migue.github.io/2013/12/06/jvm-bridge-methods/) analizamos el concepto de <b>bridge methods</b>, sus orígenes y algunas de las implicaciones de los mismos. A lo largo de esta entrada intentaremos completar la información presentada en el post anterior presentando algunos de los problemas que nos podemos encontrar cuando utilizamos este tipo de construcciones.

Compilación independiente. "Contravariant underride"
----------------------------------------------------

Supongamos que disponemos de la siguiente jerarquía de clases:

{% highlight java %}
astract class User {
}

class CustomUser extends User {
	String getCredentials() { return "credentials"; }	
}

class Main {
	public static void main (String[] args) {
		User user = new CustomUser();

	}
}
{% endhighlight %}

Como se puede observar en el fragmento anterior, la clase derivada extiende a la clase padre añadiendo un nuevo método que la aplicación cliente (en nuestro caso la clase Main) todavía no está utilizando. Imaginemos por un momento que la clase User proviene de una librería externa que, tras la correspondiente actualización a la última versión, incluye un método como el que se muestra a continuación:

{% highlight java %}
astract class User {
	abstract Object getCredentials();
}
{% endhighlight %}

Al mismo tiempo, nos hemos visto obligados a modificar nuestro cliente de manera que ahora es necesario invocar al método <i>getCredentials</i>

{% highlight java %}
class Main {
	public static void main (String[] args) {
		User user = new CustomUser();
		System.out.println("Credentials: " + user.getCredentials());
	}
}
{% endhighlight %}

Puede que el ejemplo anterior parezca estúpido pero esta simplificación representa una construcción razonablemente habitual en el que un cliente (en nuestra simplificación la clase <i>Main</i>) recibe un listener o un callback.

¿Cual sería el resultado esperado? Aparentemente parece que todo debería funcionar de manera correcta, y deberíamos obtener la cadena "credentials" impresa en nuestra consola. Sin embargo, nuestra suposición dista mucho de la realidad. Si ejecutamos el código anterior obtendremos un error como el que se muestra a continuación:

{% highlight java %}
Exception in thread "main" java.lang.AbstractMethodError: User.getCredentials()Ljava/lang/Object;
	at Main.main(Main.java:4)
{% endhighlight %}

Posiblemente ninguno de nosotros haya experimentado este tipo de error en nuetras aplicaciones.

En la próxima entrada analizaremos otro tipo de problemas ocasionados por los <i>bridge methods</i> y los procesos de compilación independiente.

Hasta pronto!