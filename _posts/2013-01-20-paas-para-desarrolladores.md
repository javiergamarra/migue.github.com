---
layout: post
title: "PaaS para desarrolladores"
category: 
tags: [cloud, paas, cloudfoundry, playframework, java]
---
{% include JB/setup %}

El viernes estuve impartiendo la primera de las dos clases sobre Cloud Computing en el [Master en Informática Móvil](http://www.web.upsa.es/mimo/) de la Universidad Pontificia de Salamanca. Se trata de un master de desarrollo para dispositivos móviles (de lo cual yo no tengo ni idea) pero los organizadores del mismo querían incluir una pequeña formación adicional en la que los alumnos pudieran adquirir una serie de conocimientos mínimos sobre Cloud Computing y cómo desplegar sus aplicaciones (aquellas que necesitan de parte servidor) de manera rápida y sencilla.

Por todo ello, el grueso de los contenidos están orientados hacia el mundo de la plataforma como servicio (PaaS), analizando sus componentes principales, sus ventajas e inconvenientes, y, lo más importante, ofreciendo una visión eminentemente práctica de todo ello.

Si alguno no conoce mucho sobre el tema y está interesado en iniciarse en este mundillo, los contenidos del curso están disponibles en el siguiente [repositorio](http://github.com/migue/paas-lessons) de Github. ¿Qué os podréis encontrar?

* Una pequeña introducción al mundo del Cloud Computing
* Un recorrido por las principales características del mundo de la plataforma como servicio
* Requisitos y cosas a tener en cuenta a la hora de escoger un proveedor de este tipo.
* Ejemplos prácticos sobre como desplegar y configurar aplicaciones

Los ejemplos de aplicaciones (son muy muy muy chorras) están hechos con [Play Framework (2.0.4)](http://playframework.org) y el proveedor de servicio es [CloudFoundry](http://cloudfoundry.org). 

La semana que viene tenemos la segunda y última clase en la que intentaremos realizar una aplicación un poquito más complicada, desplegaremos sobre nuestro proveedor de servicio actual e intentaremos realizar el mismo despliegue en otro proveedor que aún no tengo decidido. Los contenidos de la próxima semana estarán en el repositorio anterior a medida que los vaya completando. Si el tiempo nos lo permite, intentaremos ir a un nivel un poco más bajo e intentaremos realizar algún caso práctico sobre un proveedor de servicios IaaS como puede ser Amazon AWS o RackSpace.

Hasta pronto!

Migue