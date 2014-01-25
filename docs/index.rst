.. Requests documentation master file, created by
   sphinx-quickstart on Sun Feb 13 23:54:25 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Requests: HTTP para Humanos
===========================

Versión v\ |version|. (:ref:`Installation <install>`)

Requests es una librería para HTTP, ref:`licenciada bajo Apache2 <apache2>`,
escrita en Python, para seres humanos.

El módulo urllib2 que se encuentra en el estándar de Python, ofrece la
mayoría de las funcionalidades necesarias para HTTP, pero su api está
completamente **rota**. Fue construida para otra época, - y una web
diferente--. Requiere una gran cantidad de trabajo (incluso reimplementar
métodos) para ejecutar las tareas más sencillas.

Las cosas no deberían ser así. No en Python.

::

    >>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
    >>> r.status_code
    200
    >>> r.headers['content-type']
    'application/json; charset=utf8'
    >>> r.encoding
    'utf-8'
    >>> r.text
    u'{"type":"User"...'
    >>> r.json()
    {u'private_gists': 419, u'total_private_repos': 77, ...}

Ver `el mismo código, sin Requests <https://gist.github.com/973705>`_.

Requests quita las complicaciones de trabajar HTTP/1.1 en Python -
haciendo que la integración con servicios web sea transparente. No hay
necesidad de agregar queries a tus URLs manualmente, o convertir tu
información a formularios para hacer una petición POST. La reutilización
de keep-alive y conexión HTTP se hace automáticamente, todo gracias a `urllib3 <https://github.com/shazow/urllib3>`_, el cual está integrado en Requests.


Testimonios
-----------

El gobierno de su Majestad, Amazon, Google, Twilio, Mozilla, Heroku, PayPal,
NPR, Obama for America, Transifex, Native Instruments, The Washington Post,
Twitter, SoundClound, Kippt, Readability y algunas organizaciones Federales
de los Estados Unidos de América utilizan Requests internamente. Ha sido
descargado más de 8,000,000 de veces desde PyPI.

**Armin Ronacher**
    Requests es el ejemplo perfecto de qué tan hermosa pueder ser una API
    con el nivel correcto de abstracción.

**Matt DeBoard**
    Voy a tatuarme el módulo de Python Requests de @kennethreitz,
    en mi cuerpo, de alguna forma. Todo completo.

**Daniel Greenfeld**
    Eliminé una librería de 1200 líneas de código enredado, con unas 10
    líneas de código gracias a la librería Requests de @kennethreitz. Hoy
    ha sido un día GENIAL.

**Kenny Meyers**
    Python HTTP: Cuando tengas dudas, o cuando no, usa Requests. Bonita, simple
    pytónica.

Soporte Destacado
-----------------
Request está listo para al web de hoy

- URLs y Dominios internacionales
- *Keep-Alive* y Agrupamiento de conexiones (*Connection* *Pooling*)
- Sesiones con Cookies persistentes
- Verificación SSL al estilo navegador
- Autenticación Básica y Digest
- Elegantes Cookies en pares Llave/Valor
- Descompresión automática
- Cuerpos de respuestas Unicode
- Subida de archivos Multiparte
- Tiempos de espera de conexión
- Soporte para `.netrc`
- Python 2.6 -- 3.3
- Seguridad para programación en hilos (*Thread-safety*)


Guía de Usuario
---------------

Esta parte de la documentación, la cual está compuesta de prosa en su
mayoría, empieza dando información general acerca de Requests, luego se
centra en instrucciones paso por paso de cómo utilizar la mayoría de
funcionalidades que brinda Requests.

.. toctree::
   :maxdepth: 2

   user/intro
   user/install
   user/quickstart
   user/advanced
   user/authentication


Guía para la comunidad
----------------------

Esta parte de la documentación, la cual está compuesta de prosa en su
mayoría, detalla el ecosistema alrededor de Requests y su comunidad.

.. toctree::
   :maxdepth: 1

   community/faq
   community/out-there.rst
   community/support
   community/updates

Documentación del API
---------------------

Si buscas información acerca de una función, clase o método en específico
está parte de la documentación es para ti.

.. toctree::
   :maxdepth: 2

   api


Guía del contribuidor
---------------------

Si quieres contribuir con el proyecto, esta parte de la documentación es
para ti.

.. toctree::
   :maxdepth: 1

   dev/philosophy
   dev/todo
   dev/authors
