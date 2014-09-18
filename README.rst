Requests: HTTP para Humanos
===========================


.. image:: https://travis-ci.org/kennethreitz/requests.png?branch=master
        :target: https://travis-ci.org/kennethreitz/requests

Requests es una librería HTTP bajo la licencia Apache2, escrita en
Python, para seres humanos.

La mayoría de los módulos de Python existentes para enviar peticiones HTTP
son extremadamente verbosos y molestos. El módulo urllib2 incorporado en Python
ofrece la mayoría de las funcionalidades necesarias para HTTP, pero su *API*
está completamente rota. Requiere una gran cantidad de trabajo (incluso
reimplementar métodos) para ejecutar las tareas más sencillas.

Las cosas no deberían ser así. No en Python.

.. code-block:: pycon

    >>> r = requests.get('https://api.github.com', auth=('user', 'pass'))
    >>> r.status_code
    204
    >>> r.headers['content-type']
    'application/json'
    >>> r.text
    ...

Ver `el mismo código, sin Requests <https://gist.github.com/973705>`_.

Requests permite enviar peticiones HTTP/1.1. Puedes agregar cabeceras, datos
de formularios, archivos multiparte y parámetros con simples diccionarios de
Python, y accesar a los datos de respuesta de la misma manera. Requests
utiliza httplib y `urllib3 <https://github.com/shazow/urllib3>`_, pero
realiza todo el trabajo  difícil y los hacks locos por tí.


Características
---------------

- URLs y Dominios internacionales
- *Keep-Alive* & Agrupamiento de conexiones (*Connection* *Pooling*)
- Sesiones con Cookies persistentes
- Verificación SSL al estilo navegador
- Autenticación Básica y Digest
- Elegantes Cookies en pares Llave/Valor
- Descompresión automática
- Cuerpos de respuestas Unicode
- Subida de archivos Multiparte
- Tiempos de espera de conexión
- Seguridad para programación en hilos (*Thread-safety*)
- Soporte para proxy HTTP(S)


Instalación
-----------

Para instalar Requests, simplemente:

.. code-block:: bash

    $ pip install requests

También, si es absolutamente necesario:

.. code-block:: bash

    $ easy_install requests

Pero realmente no deberías hacer eso.

Documentación
-------------

La documentación está disponible en http://es.python-requests.org/


Contribuye
----------

#. Busca temas abiertos o abre un tema nuevo para comenzar una discusión
   sobre una idea o un bug. Existe una etiqueta llamada *Contributor* *Friendly*
   (Amigable al Contribuyente), la cual es ideal para personas que aún no están
   familiarizadas con el código fuente.
#. Si te sietes incómodo o poco certero sobre algún tema o sobre tus
   modificaciones, sientete libre de enviar un correo a @sigmavirus24 y él
   felizmente te ayudará vía correo electrónico, Skype, parejas remotas o con
   lo que estés más cómodo.
#. Haz un *Fork* `al repositorio`_ en Github para empezar a realizarle
   cambios a la rama principal (*master* *branch*)  (o crea otra rama a partir
   de ella).
#. Escribe una prueba que demuestre que el bug ha sido corregido o que la
   nueva característica funciona como se esperaba.
#. Envía un *pull* *request* y molesta al mantenedor hasta que sea consolidado
   y publicado. :) Asegúrate de agregarte a la lista de autores AUTHORS_.

.. _`al repositorio`: http://github.com/kennethreitz/requests
.. _AUTHORS: https://github.com/kennethreitz/requests/blob/master/AUTHORS.rst
