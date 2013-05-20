---
layout: post
title: "¿Algo que leer?"
category: 
tags: [java, jvm, performance, http, garbage collection, netty]
---
{% include JB/setup %}

Ha pasado mucho tiempo desde la última entrada pero, siendo honesto, siempre encuentro algo mejor que hacer, por lo que la tarea de actualizar el blog siempre queda en el fondo de la cola.

Durante las últimas semanas he tenido que viajar bastante (Japón, Los Ángeles, ...) y he tenido tiempo a comenzar/terminar/comprar algunos libros que tenía pendientes en mi pila de lectura. Me gustaría compartir con vosotros algunos de ellos:

## Akka Concurrency ##

Llevo trabajando con Akka desde sus primeras versiones y, en mi humilde opinión, creo que se trata de una de las mejores herramientas que un desarrollador podría tener en su caja de herramientas a la hora de lidiar con temas como concurrencia y aplicaciones distribuidas. Puedo aseguraros una cosa; este libro no defrauda

<div id="akka-concurrency-book" align="center">
	<a href="http://www.artima.com/shop/akka_concurrency">
		<img src="http://www.artima.com/images/akkaCover500x500.gif" alt="Portada del libro Akka Concurrency">
	</a>
</div>

Libro muy bien escrito en el que todos los conceptos son analizados desde cero (no es necesaria ninguna experiencia previa con Akka para seguir el libro). A través de un caso práctico (un simulador de un avión), se ponen en liza muchos de las herramientas que el "toolkit" ofrece, explicando cómo se pueden utilizar además de sus principios de funcionamiento. Se cubren numerosos conceptos: actores (systems, dispatchers, mailbox, ...), routers, remoting, data flow, agentes, patrones y anti-patrones, I/O (el componente anterior, no el que está en desarrollo en la actualidad) y algunos de los "addons" más importantes.

Personalmente creo que se trata de una magnífica lectura tanto si ya has trabajado con Akka como si es la primera vez que oyes hablar de la plataforma pero quieres profundizar en diferentes alternativas a la hora de construir tus nuevas aplicaciones concurrentes y/o distribuidas.

## Netty in Action ##

Framework asíncrono dirigido por eventos que permite, de manera rápida y sencilla, la construcción de protocolos de red. Se trata de una versión MEAP (Manning Early Access Program) del que están accesibles aproximadamente ocho capítulos en el momento en el que estoy escribiendo en esta entrada.

Durante la primera parte del libro (la que está disponible en este momento) se analizan los componentes principales del framework: diferentes transportes, channel data handlers, codecs, buffering, ... La segunda parte se presentarán (no está disponible todavía) numerosos ejemplos prácticos: HTTP, SPDY, Websockets, UDP, . . . La tercera y última parte del libro se centra en aspectos un poco "más avanzados" como el modelos de hilos utilizado por el framework, cómo escribir tu propio transporte o el uso de eventos específicos entre otras muchas.

Los capítulos disponibles hasta el momento son bastante amenos y de fácil lectura. Además, su autor, Norman Maurer, está abierto a todo de críticas y/o sugerencias.

<div id="netty-in-action-book" align="center">
	<a href="http://www.manning.com/maurer/">
		<img src="http://www.manning.com/maurer/maurer_cover150.jpg" alt="High performance browser networking">
	</a>
</div>


## High performance browser networking ##

Ilya Grigorik está escribiendo no un libro sino un librazo, con todas las letras. De nuevo se trata de un ejemplar que está en desarrollo pero los capítulos disponibles hasta el momento hacen que desees los que están por llegar como alma que lleva el diablo.

La primera parte de libro cubre los bloques básicos que conforman TCP y UDP, para posteriormente profundizar en tecnologías novedosas como HTTP 2.0, Websockets, SSE (Server Side Events) o WebRTC entre muchas otras. Analiza numerosos protocolos y arquitecturas de red, profundizando en cómo todas ellas afectan al rendimiento de las aplicaciones (tanto web como móviles)

<div id="high-performance-browser-networking-book" align="center">
	<a href="http://shop.oreilly.com/product/0636920028048.do">
		<img src="http://akamaicovers.oreilly.com/images/0636920028048/rc_lrg.jpg" alt="High performance browser networking">
	</a>
</div>

## The garbage collection handbook ##

Llevo más de un año queriendo leer este libro y todas las búsquedas de una versión electrónica del mismo habían resultado infructosas hasta el momento. Por tanto, finalmente he comprado una versión en papel de la que solo he tenido ocasión de leer unas 20-25 hojas.

Se trata de un manual de referencia que refleja el estado del arte de la recolección automática de memoria, presentando de manera conjunta los avances hechos por investigadores y desarrolladores durante los últimos 50 años (tanto software como hardware)

<div id="the-garbage-collection-hand-book" align="center">
	<a href="http://www.amazon.com/Garbage-Collection-Handbook-Management-Algorithms/dp/1420082795">
		<img src="https://pbs.twimg.com/media/BKRKAi4CYAAAE9G.jpg" alt="The garbage collection handbook">
	</a>
</div>

No es una gran entrada pero sentía la necesidad de actualizar el blog :)

Espero que la próxima entrada no se haga tanto de esperar. Hasta pronto!