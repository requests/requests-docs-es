.. _api:

Interfaz para Desarrolladores
=============================

.. module:: requests

Esta parte de la documentación cubre todas las interfaces de Requests.
En las partes donde Requests depende de librerías externas, documentamos
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

Esta sección expone las principales diferencias entre las versiones 0.x y 1.x,
y pretende facilitar las molestias a la hora de actualizar.


Cambios en el API
~~~~~~~~~~~~~~~~~

* ``Response.json`` ahora es un invocable y no una propiedad de una respuesta.

  ::

      import requests
      r = requests.get('https://github.com/timeline.json')
      r.json()   # This *call* raises an exception if JSON decoding fails

* El API de ``Session`` ha cambiado. Los objetos ``Session`` ya no reciben
parámetros. ``Session`` se escribe ahora con mayúscula, pero aún puede ser
instanciado como  ``session`` en minúscula, por razones de compatibilidad
hacia atrás.

  ::

      s = requests.Session()   # anteriormente session tomaba parámetros
      s.auth = auth
      s.headers.update(headers)
      r = s.get('http://httpbin.org/headers')

* Todos los *hooks* de petición han sido eliminados, con excepción de
'response'.

* Los ayudantes de autenticación han sido separados en módulos independientes.
Ver  requests-oauthlib_ y requests-kerberos_.

.. _requests-oauthlib: https://github.com/requests/requests-oauthlib
.. _requests-kerberos: https://github.com/requests/requests-kerberos

* El parámetro para realizar peticiones de *streaming* ha cambiado de
``prefetch`` a ``stream`` y la lógica ha sido invertida. Adicionalmente,
``stream`` ahora es obligatorio para leer respuestas en crudo.

  ::

      # en 0.x, pasar prefetch=False haría lo mismo que
      r = requests.get('https://github.com/timeline.json', stream=True)
      r.raw.read(10)

* El parámetro ``config`` en los métodos de peticiones ha sido eliminado.
Algunas de estas opciones ahora se configuran en una sesión ``Session``,
tales como *keep-alive* y el número máximo de redirecciones. La opción de
verbosidad debe ser manipulada configurando las opciones de registro
(*logging*).

  ::


      import requests
      import logging

      # estas dos líneas habilitan el debugg a nivel httplib (requests->urllib3->httplib)
      # verás el REQUEST, incluído HEADERS y DATA, así como RESPONSE sin HEADERS pero sin DATA.
      # la única cosa que falta será el response.body, que no es registrado.
      import httplib
      httplib.HTTPConnection.debuglevel = 1

      logging.basicConfig() # necesitas inicializar logging, de otra manera no verás nada desde requests
      logging.getLogger().setLevel(logging.DEBUG)
      requests_log = logging.getLogger("requests.packages.urllib3")
      requests_log.setLevel(logging.DEBUG)
      requests_log.propagate = True

      requests.get('http://httpbin.org/headers')


Licenciamiento
~~~~~~~~~~~~~~

Una diferencia clave que no tiene nada que ver con el API es el cambio en la
licencia, de ISC_ a `Apache 2.0`_. La licencia Apache 2.0 asegura que las contribuciones a Requests también estén cubiertas por la licencia Apache 2.

.. _ISC: http://opensource.org/licenses/ISC
.. _Apache 2.0: http://opensource.org/licenses/Apache-2.0

Migrando a 2.x
----------------


Comparado con el release 1.0, hay relativamente pocos cambios compatibles con
versiones anteriores, pero aún hay algunos pocos temas de los cuales estar
pendientes en esta liberación.

Para más detalles en los cambios de este release, incluidos nuevas API,
enlaces relevantes a los issues en GitHub y algunas de las correcciones de
bug, lee el blog_ de Cory al respecto

.. _blog: http://lukasa.co.uk/2013/09/Requests_20/


Cambios en la API
~~~~~~~~~~~~~~~~~

* Hay un par de cambios en cómo Requests maneja las excepciones.
``RequestException`` es ahora una subclase de ``IOError`` en vez de
``RuntimeError`` debido a que categoriza con mayor precisión este tipo de
error. Además, una secuencia de escape URL inválida ahora arroja una
subclase de ``RequestException`` en vez de ``ValueError``.

  ::

      requests.get('http://%zz/')   # arroja un requests.exceptions.InvalidURL

  Por último, las excepciones ``httplib.IncompleteRead`` causadas por una
  incorrecta codificación por trozos (*chunked*) ahora arrojará un
  ``ChunkedEncodingError``.

* La API de proxy ha cambiado ligeramente: ahora se requiere el esquema
para la URL del proxy.

  ::

      proxies = {
        "http": "10.10.1.10:3128",    # ahora usa http://10.10.1.10:3128
      }

      # En Requests 1.x, esto era legal, en Requests 2.x,
      #  esto arroja requests.exceptions.MissingSchema
      requests.get("http://example.org", proxies=proxies)


Cambios en el Comportamiento
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Las llaves en el diccionario ``headers`` ahora son cadenas nativas strings
para todas las versiones de Python , por ejemplo, ``bytestring`` en Python 2
y unicode on Python 3. Si las llaves no son cadenas nativas (unicode en
Python2 o bytestring en Python 3) serán convertidas al tipo de cadena nativa
asumiendo codificación UTF-8.

* Los Timeouts se comportan ligeramente diferente. En peticiones de streaming,
  sólo aplicann al intento de conexión. En peticiones regulares, el timeout
  es aplicado al proceso de conexión y a la descarga completa.

  ::

      tarball_url = 'https://github.com/kennethreitz/requests/tarball/master'

      # Un segundo de timeout para el intento de conexión
      # Tiempo ilimitado para descargar el tarball
      r = requests.get(tarball_url, stream=True, timeout=1)

      # Un segungo de para el intento de conexión
      # Otro segundo completo para descargar el tarball
      r = requests.get(tarball_url, timeout=1)

