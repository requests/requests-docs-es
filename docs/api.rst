.. _api:

Interfaz para Desarrolladores
=============================

.. module:: requests

Esta parte de la documentación cubre todas las interfaces de Requests. En 
las partes donde Requests depende de librerías externas, hemos documentado
las más importantes aquí mismo, y proveemos enlaces a la documentación
canónica.


Interfaz Principal
------------------

Se puede acceder a todas las funcionalidades de Requests a través de estos 7
métodos. Todos ellos devuelven una instancia de :class: `Response <Response>`.

.. autofunction:: request

.. autofunction:: head
.. autofunction:: get
.. autofunction:: post
.. autofunction:: put
.. autofunction:: patch
.. autofunction:: delete


Clases de bajo nivel
~~~~~~~~~~~~~~~~~~~~

.. autoclass:: requests.Request
   :inherited-members:

.. autoclass:: Response
   :inherited-members:

Request Sessions
----------------

.. autoclass:: Session
   :inherited-members:


Excepciones
~~~~~~~~~~~

.. module:: requests

.. autoexception:: RequestException
.. autoexception:: ConnectionError
.. autoexception:: HTTPError
.. autoexception:: URLRequired
.. autoexception:: TooManyRedirects


Búsqueda de Códigos de Estado
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. autofunction:: requests.codes

::

    >>> requests.codes['temporary_redirect']
    307

    >>> requests.codes.teapot
    418

    >>> requests.codes['\o/']
    200

Cookies
~~~~~~~

.. autofunction:: dict_from_cookiejar
.. autofunction:: cookiejar_from_dict
.. autofunction:: add_dict_to_cookiejar


Codificaciones
~~~~~~~~~~~~~~

.. autofunction:: get_encodings_from_content
.. autofunction:: get_encoding_from_headers
.. autofunction:: get_unicode_from_response
.. autofunction:: decode_gzip


Clases
~~~~~~

.. autoclass:: requests.Response
   :inherited-members:

.. autoclass:: requests.Request
   :inherited-members:

.. autoclass:: requests.PreparedRequest
   :inherited-members:

.. _sessionapi:

.. autoclass:: requests.Session
   :inherited-members:


Migrando a 1.x
--------------

Esta sección expone las diferencias principales entre 0.x y 1.x, y pretende
facilitar las molestias al hora de actualizar.


Cambios en el API
~~~~~~~~~~~~~~~~~

* ``Response.json`` ahora es un invocable y no una propiedad de una respuesta.

  ::

      import requests
      r = requests.get('https://github.com/timeline.json')
      r.json()   # This *call* raises an exception if JSON decoding fails

* El API de ``Session`` ha cambiado. Los objetos ``Session`` ya no reciben parámetros.
  ``Session`` se escribe ahora con mayúscula, pero aún puede ser instanciado como 
  ``session`` en minúscula, por razones de compatibilidad hacia atrás.

  ::

      s = requests.Session()    # formerly, session took parameters
      s.auth = auth
      s.headers.update(headers)
      r = s.get('http://httpbin.org/headers')

* Todos los *hooks* de petición han sido eliminados, con excepción de 'response'.

* Los ayudantes de autenticación han sido separados en módulos independientes. Ver
  requests-oauthlib_ y requests-kerberos_.

.. _requests-oauthlib: https://github.com/requests/requests-oauthlib
.. _requests-kerberos: https://github.com/requests/requests-kerberos

* El parámetro para realizar peticiones de *streaming* ha cambiado de ``prefetch``
  a ``stream`` y la lógica ha sido invertida. Adicionalmente, ``stream`` ahora es
  obligatorio para leer respuestas en crudo.

  ::

      # en 0.x, pasar prefetch=False haría lo mismo que
      r = requests.get('https://github.com/timeline.json', stream=True)
      r.raw.read(10)

* El parámetro ``config`` en los métodos de peticiones ha sido eliminado. Algunas de
  estas opciones ahora se configuran en una sesión ``Session``, tal como *keep-alive* y
  el número máximo de redirecciones. La opción de verbosidad debe ser manipulada
  configurando las opciones de registro (*logging*).

  ::

      # La verbosidad se configura usando logging
      my_config = {'verbose': sys.stderr}
      requests.get('http://httpbin.org/headers', config=my_config)  # mal!


Licenciamiento
~~~~~~~~~~~~~~

Una diferencia clave que no tiene nada que ver con el API, es el cambio en la
licencia, la cambió de la licencia ISC_, a la licencia `Apache 2.0`_. La licencia
Apache 2.0 asegura que las contribuciones a Requests, también estén cubiertas por
la licencia Apache 2.

.. _ISC: http://opensource.org/licenses/ISC
.. _Apache 2.0: http://opensource.org/licenses/Apache-2.0

