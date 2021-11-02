---
layout: post
title: ¿Es Twitter de derechas?
author: Ricardo Kleinlein
tags: news nlp ESP
---

> La semana pasada se conocía un estudio interno de Twitter donde se anunciaba que aparentemente, la plataforma prioriza el contenido procedente de la derecha política en sus recomendaciones personalizadas a los usuarios. Al poco tiempo cientos de medios en todo el planeta se hicieron eco de un titular muy jugoso: “Twitter revela que su algoritmo beneficia a la derecha”.

<!--eoe-->

{:class="table-of-content"}
* TOC
{:toc}

![twitter logo with fairness symbol between question marks](/assets/images/posts/twitter/twitter_fairness_mark.png)

Al menos eso parecía desprenderse de [las palabras de Rumman Chowdhury](https://blog.twitter.com/en_us/topics/company/2021/rml-politicalcontent), directora de ética y transparencia en Twitter, escritas a raíz de un estudio interno en el que evaluaban algunos de los efectos derivados de delegar la recomendación de contenido a algoritmos automáticos. 
Se puede acceder al informe completo [aquí](https://cdn.cms-twdigitalassets.com/content/dam/blog-twitter/official/en_us/company/2021/rml/Algorithmic-Amplification-of-Politics-on-Twitter.pdf).

# Twitter es apolítico, pero no sus fundadores

Jack Dorsley, CEO y cofundador de Twitter, siempre ha mantenido que su verdadera pasión es la política, y en una entrevista concedida a la CNN en 2018 él mismo admitía sin ambages considerarse, tanto a sí mismo como al núcleo de su equipo, abiertamente de izquierdas. Asimismo, negaba categóricamente que su orientación política afectara al funcionamiento de la plataforma que él dirige. 

Si bien es cierto que ambos lados del espectro político suelen quejarse de un trato desfavorable (véase el [veto a Trump](https://blog.twitter.com/en_us/topics/company/2020/suspension), o el [cierre de cuentas masivo en Venezuela](https://www.reuters.com/article/us-venezuela-politics-twitter-idUSKBN1Z72SG)) , se estima que durante el año 2020 se escribían en promedio alrededor de **¡500 millones!** de tweets cada día procedentes de millones de cuentas diferentes (según datos de [Statista.com](https://www.statista.com/topics/737/twitter/#dossierKeyfigures)). Como pueden imaginar, sería imposible para un equipo humano no solo examinar y validar cada mensaje, sino ponerlo en contexto con respecto a la actividad de otros usuarios. Es por ello que desde sus inicios, Twitter ha confiado ampliamente en las capacidades de sistemas recomendadores, detectores de contenido ofensivo, modelos de semejanza entre usuarios, sistemas de Procesamiento del Lenguaje Natural (NLP)… En suma, **Twitter depende de la IA**, y del buen o mal equilibrio que logre alcanzar en la propagación de las ideas de todo signo político. 

# Cómo afecta el uso de Inteligencia Artificial?

Si la balanza se desequilibrara a favor de alguna corriente política en particular, su posición preeminente como lugar de debate público en la red se derrumbaría cual castillo de naipes. Por eso, desde la propia compañía publicaron un informe, en parte auditoría interna, sobre sus algoritmos. Su objetivo era medir hasta qué punto existe un sesgo en los protocolos que de manera automática enlazan usuarios y contenido recomendado. Éste no es un procedimiento trivial, y están implicados muchos modelos de IA, cada uno de los cuales procesa tan sólo una pequeña parte del problema. Algunos están relacionados con el historial de tweets del usuario, otros con su actividad y permanencia, otros con la detección de temáticas o de contenido ofensivo… En él, los investigadores  no entran a examinar cada uno de ellos por separado, sino que intentan evaluar si, en conjunto, dichos sistemas recomendadores tienden a propagar con mayor facilidad mensajes emitidos desde cuentas “de derechas” o “de izquierdas”. 

# Procedimientos y conclusión del estudio

Para ello recopilaron las cuentas asociadas a los cargos más importantes dentro de los principales partidos políticos de cada uno de los 7 países estudiados: Estados Unidos, Japón, Reino Unido, Francia, España y Alemania, así como los tweets publicados desde dichas cuentas en el período que abarca desde el 1 de abril hasta el 15 de agosto de 2020.

Para evaluar la amplificación de un mensaje por los algoritmos automáticos de recomendación, compararon el porcentaje de aparición en pantalla de los tweets en perfiles sujetos a personalización (el modo más común de interactuar en Twitter) con respecto a usuarios para los cuales dicha función está desactivada y para quienes los mensajes, indistintamente de su relevancia o viralidad, aparecen en orden cronológico al abrir la aplicación.

Su principal conclusión es clara: **Los mensajes procedentes de cuentas de políticos asociados con la derecha tienden, en conjunto, a mostrarse más a menudo a los usuarios.**

# En un análisis más profundo...

Aparecen ya un par de aspectos fundamentales a la hora de interpretar los resultados; en primer lugar, la ventana de tiempo considerada. **Todos los tweets** analizados fueron publicados durante los meses en los que más intensamente se vivió la pandemia derivada del **brote de la COVID-19**. Es discutible que dichos mensajes representen de forma fidedigna el tono usual, o al común de las conversaciones, en Twitter. A fin de cuentas, se trata de un evento que en décadas no había tenido precedente, y ha provocado una oleada de reacciones que aún vivimos.

Por otro lado, y más importante, los mensajes considerados no son “de derechas”, como tampoco son “de izquierdas”. **De hecho, no se analiza en absoluto el contenido del mensaje en sí**. Es más, no se analiza la propagación de noticias o ideas publicadas por usuarios que no pertenezcan a la cúpula de alguno de los principales partidos políticos de cada país. Por tanto, **queda fuera del alcance de la investigación si las ideas de un lado u otro del espectro se propagan más o menos**. Decir por tanto que las derechas reciben mayor cobertura en Twitter es hacer una libre interpretación de los resultados.

Tan sólo podemos decir que, en promedio, considerando los dos principales partidos de cada país y asignándoles a cada uno una etiqueta de “izquierda” o “derecha” fija (lo cual es más que discutible en muchos casos), aparece cierta tendencia a que los mensajes emitidos desde cuentas de políticos de derechas aparezcan más a menudo que sus contrapartes de izquierdas.

No obstante, y esto es importante, no podemos hacer más que elucubraciones acerca de por qué esto es así, y **achacarlo a un sesgo en los algoritmos sería un error**. Aspectos como la frecuencia de publicación, el número de seguidores o la conveniencia de una publicación son dejados de lado en el estudio, por no mencionar la propagación derivada del uso de bots, práctica común dentro de casi todos los partidos políticos. 

Por último, algunas pruebas preliminares realizadas por mí mismo sobre los datos proporcionados en el estudio muestran que si en lugar de basar nuestras conclusiones tan sólo en la comparativa entre los dos partidos mayoritarios consideramos además otros partidos más extremos (como sería el caso de Podemos y Vox en España), la tendencia se suaviza en casi todos los países. De hecho, el propio estudio resalta que los tweets procedentes de partidos más radicales reciben menos amplificación, independientemente de su orientación política.

En definitiva, parece que los políticos de partidos alineados con la línea de pensamiento del Partido Popular Europeo consiguen en Twitter mayor protagonismo que ningún otro grupo parlamentario. **Los motivos exactos**, sin embargo, si bien se nos escapan por el momento, **apuntan a una estrategia de comunicación exitosa, antes que a un sesgo algorítmico**.

