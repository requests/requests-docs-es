.. _faq:

Preguntas Frecuentes
===================

Esta parte de la documentación responde preguntas comunes acerca de Requests.

¿Datos Codificados?
-------------------

Requests descomprime automáticamente respuestas codificadas con gzip, y 
hace su mejor esfuerzo por decodificar el contenido de la respuesta a unicode,
cuando es posible.

También puedes tener acceso directo a la respuesta cruda (e incluso al socket)
de ser necesario.


¿Agentes de usuario propios?
----------------------------

Requests te permite reimplementar las cadenas del Agente de usuario, al 
igual que cualquier otra cabecera HTTP.


¿Por qué no Httplib2?
---------------------

Chris Adams dió un excelente resumen en
`Hacker News <http://news.ycombinator.com/item?id=2884406>`_:

    httplib2 es parte del porque debes usar requests: es mucho más respetable
    como cliente pero no está tan bien documentado y aún requiere de mucho código
    para realizar las operaciones básicas. Aprecio lo que httplib2 intenta realizar, 
    existe un buen número de molestias de bajo nivel a la hora de crear un cliente
    HTTP moderno, pero en serio, simplemente utiliza requests. Kenneth Reitz está
    motivado y entiende el grado al que las cosas simples deben ser simples, mientras
    que httplib2 se siente más como un ejercicio académico en lugar de algo que las
    personas deberían utilizar para desarrollar sistemas de producción[1]

    Divulgación: Estoy en la lista de autores de requests, pero solamente me puedo
    dar crédito por alrededor del 0.0001% de su genialidad.
    
    1. http://code.google.com/p/httplib2/issues/detail?id=96 es un buen ejemplo:
    un molesto bug que afectaba a muchas personas, para el cual había una solución
    desde hace meses, esta solución funcionó estupendamente cuando la apliqué a
    un *fork* y pude moler un par de TB de información con ella, pero tardó
    más de un año para que llegara a *trunk* y aún más para que llegara a PyPI
    donde cualquier otro proyecto que requería "httplib2" obtendría la versión 
    funcional.


¿Soporte para Python 3?
-----------------------

¡Sí! Esta es una lista de plataformas de Python que están
soportadas oficialmente:

* Python 2.6
* Python 2.7
* Python 3.1
* Python 3.2
* Python 3.3
* PyPy 1.9
