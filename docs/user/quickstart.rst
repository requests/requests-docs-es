.. _quickstart:

Quickstart
==========

.. module:: requests.models

¿Ansioso por empezar? Esta página brinda una buena introducción sobre
como empezar a utilizar Requests. Esta guía asume que ya tengas
instalado Requests. Si aún no lo has hecho, ve a la sección
:ref:`Instalación <install>`.

Primero, asegúrate que:

* Requests esté :ref:`instalado <install>`
* Requests esté :ref:`actualizado <updates>`


Empecemos con algunos ejemplos sencillos.


Realizar un petición
--------------------

Realizar una petición en Requests muy sencillo.

Comienza importando el módulo de Requests::

    >>> import requests

Ahora, intentemos obtener un página web. Para este ejemplo, vamos a
obtener el timeline público de GitHub.::

    >>> r = requests.get('https://github.com/timeline.json')

Ahora, tenemos un objeto :class:`Response` llamado ``r``. Podemos
obtener  toda la información que necesitamos a partir de este objeto.

En Requests, un API simple significa que todas las formas the peticiones
HTTP son obvias. Por ejemplo, así es como realizas una petición HTTP
POST::

    >>> r = requests.post("http://httpbin.org/post")

¿Qué tal otros tipos de peticiones HTTP?: PUT, DELETE, HEAD y OPTIONS? Todos
estos son igual de simples::

    >>> r = requests.put("http://httpbin.org/put")
    >>> r = requests.delete("http://httpbin.org/delete")
    >>> r = requests.head("http://httpbin.org/get")
    >>> r = requests.options("http://httpbin.org/get")

Todo esto está bien, pero es solo el comienzo de lo que Requests puede hacer.


Pasar parámetros en URLs
------------------------

Con frecuencia, debes enviar algún tipo de información en el *query
string* de la URL. Si estuvieses creando la URL a mano, esta información
estaría en forma de pares llave/valor luego del signo de interrogación en
la URL, por ejemplo ``httpbin.org/get?key=val``. Requests te permite
proveer estos argumentos en forma de diccionario, usando el parámetro en
llave (*keyword argument*) ``params``. Como ejemplo, si quisieras pasar
``key1=value1`` y ``key2=value2`` a ``httpbin.org/get``, usarías algo
como esto::

    >>> payload = {'key1': 'value1', 'key2': 'value2'}
    >>> r = requests.get("http://httpbin.org/get", params=payload)

Puedes ver que la URL ha sido codificada correctamente imprimiéndola::

    >>> print r.url
    u'http://httpbin.org/get?key2=value2&key1=value1'

Nota que cualquier llama del diccionario cuyo valor es ``None`` no será
agregada al *query string* del URL.

Contenido de respuesta
----------------------

Podemos leer el contenido de la respuesta del servidor. Usemos el timeline
de GitHub nuevamente::

    >>> import requests
    >>> r = requests.get('https://github.com/timeline.json')
    >>> r.text
    '[{"repository":{"open_issues":0,"url":"https://github.com/...

Requests automáticamente decodificará el contenido que viene del servidor.
La mayoría de caracteres unicode serán decodificados correctamente.

Cuando ejecutas una petición, Requests tratará de obtener la codificación
de la respuesta basándose en las cabeceras HTTP. La codificación del
texto que Requests halló (o supuso), será utilizada cuando se acceda a
``r.text``. Puedes conocer la codificación que Requests está utilizando,
y cambiarla, usando la propiedad ``r.encoding``::

    >>> r.encoding
    'utf-8'
    >>> r.encoding = 'ISO-8859-1'

Si cambias la codificación, Requests utilizará este nuevo valor de
``r.encoding`` siempre que se invoque a ``r.text``. Podrías querer hacer
esto en cualquier situación donde puedas aplicar una lógica de trabajo
especial para trabajar según la codificación que esté en el contenido.
Por ejemplo, HTTP y XML tienen la habilidad de especificar su
codificación en su cuerpo. En situaciones como esta, deberías usar
``r.content`` para encontrar la codificación y después configuar
``r.encoding``. Esto te permitirá usar ``r.text`` con la codifiación
correcta.

Requests también puede utilizar codificaciones del usuario, en caso de ser necesario.
Si creaste tu propia codificación, y la has registrado usando el módulo ``codecs``,
puedes asignar el nombre de este codec como valor de ``r.encoding`` y Requests se
encargará de la decodificación.


Contenidos de respuesta binarios
--------------------------------

También puedes acceder al cuerpo de la respuesta como bytes, para
peticiones que no sean de texto::

    >>> r.content
    b'[{"repository":{"open_issues":0,"url":"https://github.com/...

Las codificaciones de transferencia ``gzip`` y ``deflate`` serán
decodificadas automáticamente.

Por ejemplo, para crear una imagen a partir de datos binarios en una
respuesta, puedes usar el siguiente código::

    >>> from PIL import Image
    >>> from StringIO import StringIO
    >>> i = Image.open(StringIO(r.content))


Contenido de respuesta JSON
---------------------------

También hay un decodificador de JSON incorporado en Requests, en caso de
que estés trabajando con datos JSON::

    >>> import requests
    >>> r = requests.get('https://github.com/timeline.json')
    >>> r.json()
    [{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...

Si la decodificación falla, ``r.json`` levantará una excepción. Por
ejemplo, si la respuesta obtiene un código 401 (No Autorizado/
Unauthorized), intentar ``r.json`` mandará una excepción
``ValueError: No JSON object could be decoded``.


Contenido de respuesta en crudo
-------------------------------

En el caso extraño que quieras obtener la respuesta en crudo a nivel
socket, puedes acceder ``r.raw``. Si quieres hacer esto, asegúrate de
pasar ``stream=True`` en la petición inicial. Una vez que hagas esto,
puedes hacer lo siguiente::

    >>> r = requests.get('https://github.com/timeline.json', stream=True)
    >>> r.raw
    <requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
    >>> r.raw.read(10)
    '\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'

De manera general, sin embargo, deberías usar un patrón como este para
guardar lo que se recibe del *stream* a un archivo::

    with open(filename, 'wb') as fd:
        for chunk in r.iter_content(chunk_size):
            fd.write(chunk)

Al usar ``Response.iter_content`` se manejará mucho de lo que deberías
haber manipulado a mano usando ``Response.raw`` directamente. Lo de arriba
es la forma preferida y recomendad de obtener el contenido obtenido

Cabeceras personalizadas
------------------------

Si quieres agregar cabeceras HTTP a una petición, simplemente pasa un ``dict``
al parámetro ``headers``.

Por ejemplo, en el ejemplo anterior no especificamos la cabecera content-type::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}
    >>> headers = {'content-type': 'application/json'}

    >>> r = requests.post(url, data=json.dumps(payload), headers=headers)


Peticiones POST más complicadas
-------------------------------

Típicamente, quieres enviar información en forma de formulario, como un formulario HTML.
Para hacerlo, pasa un diccionario al argumento `data`. Este diccionario será codificado
automáticamente como formulario al momento de realizar la petición::

    >>> payload = {'key1': 'value1', 'key2': 'value2'}
    >>> r = requests.post("http://httpbin.org/post", data=payload)
    >>> print r.text
    {
      ...
      "form": {
        "key2": "value2",
        "key1": "value1"
      },
      ...
    }

Existen ocasiones en las que quieres enviar datos en otra codificación. Si pasas un ``string`` en vez de un ``dict``,
la información será posteada directamente.

Por ejemplo, el API v3 de GitHub acepta información en forma de JSON POST/PATCH::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}

    >>> r = requests.post(url, data=json.dumps(payload))


Pasar un Archivo Multiparte en POST
-----------------------------------

Requests hace que sea simple subir archivos Multiparte::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': open('report.xls', 'rb')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<censored...binary...data>"
      },
      ...
    }

Puedes establecer explícitamente el nombre del archivo, _content_type_ y
encabezados::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.xls', open('report.xls', 'rb'), 'application/vnd.ms-excel', {'Expires': '0'})}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<censored...binary...data>"
      },
      ...
    }

Si quieres, puedes enviar cadenas de caracteres para ser recibidas
como archivos::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.csv', 'some,data,to,send\nanother,row,to,send\n')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "some,data,to,send\\nanother,row,to,send\\n"
      },
      ...
    }

Si estás enviando un archivo muy larga como una petición
``multipart/form-data``, puedes querer hacer un *stream* de la petición.
Por defecto, ``requests`` no lo soporta, pero hay un paquete separado
que sí lo hace - ``requests-toolbelt``--. Deberías leer
`la documentación de  toolbelt <https://toolbelt.rtfd.org>`_ para más
detalles de cómo usarlo.

Códigos de estado de respuesta
------------------------------

Podemos verificar el código de estado de la respuesta::

    >>> r = requests.get('http://httpbin.org/get')
    >>> r.status_code
    200

Requests también incluye un objeto para buscar estados de respuesta
y pueden ser referenciados fácilmente::

    >>> r.status_code == requests.codes.ok
    True

Si hacemos una mala petición (respuesta diferente a 200), podemos
levantar una excepción con :class:`Response.raise_for_status()`::

    >>> bad_r = requests.get('http://httpbin.org/status/404')
    >>> bad_r.status_code
    404

    >>> bad_r.raise_for_status()
    Traceback (most recent call last):
      File "requests/models.py", line 832, in raise_for_status
        raise http_error
    requests.exceptions.HTTPError: 404 Client Error

Pero, debido a que nuestro ``status_code`` para ``r`` fue ``200``,
cuando llamamos ``raise_for_status()`` obtenemos::

    >>> r.raise_for_status()
    None

Todo está bien.


Cabeceras de respuesta
----------------------

Podemos ver las cabeceras de respuesta del servidor utilizando un
diccionario::

    >>> r.headers
    {
        'status': '200 OK',
        'content-encoding': 'gzip',
        'transfer-encoding': 'chunked',
        'connection': 'close',
        'server': 'nginx/1.0.4',
        'x-runtime': '148ms',
        'etag': '"e1ca502697e5c9317743dc078f67693f"',
        'content-type': 'application/json; charset=utf-8'
    }

Este diccionario es especial: está hecho únicamente para las cabeceras HTTP.
De acuerdo con el `RFC 7230 <http://tools.ietf.org/html/rfc7230#section-3.2>`_
, los nombres de las cabeceras HTTP no hacen distinción entre mayúsculas y
minúsculas.

Así que podemos acceder a las cabeceras utilizando letras mayúsculas o minúsculas::

    >>> r.headers['Content-Type']
    'application/json; charset=utf-8'

    >>> r.headers.get('content-type')
    'application/json; charset=utf-8'


Cookies
-------

Si una respuesta contiene Cookies, puedes acceder a ellas rápidamente::

    >>> url = 'http://example.com/some/cookie/setting/url'
    >>> r = requests.get(url)

    >>> r.cookies['example_cookie_name']
    'example_cookie_value'

Para enviar tus propias *cookies* al servidor, puedes utilizar el
parámetro ``cookies``::

    >>> url = 'http://httpbin.org/cookies'
    >>> cookies = dict(cookies_are='working')

    >>> r = requests.get(url, cookies=cookies)
    >>> r.text
    '{"cookies": {"cookies_are": "working"}}'


Historial y Redireccionamiento
------------------------------

Requests realizará redireccionamiento para peticiones par todos los verbos,
excepto HEAD.

GitHub redirecciona todas las peticiones HTTP hacia HTTPS. Podemos usar
el método ``history`` del objeto ``Response`` para rastrear las
redirecciones. Veamos que hace GitHub::

    >>> r = requests.get('http://github.com')
    >>> r.url
    'https://github.com/'
    >>> r.status_code
    200
    >>> r.history
    [<Response [301]>]

La lista :class:`Response.history` contiene una lista de
objetos tipo :class:`Request` que fueron creados con el fín
de completar la petición. La lista está ordenada desde la petición
más antigüa, hasta las más reciente.

Si estás utilizando GET u OPTIONS, puedes deshabilitar el redireccionamiento
usando el parámetro ``allow_redirects``::

    >>> r = requests.get('http://github.com', allow_redirects=False)
    >>> r.status_code
    301
    >>> r.history
    []

Si estás utilizando HEAD, puedes habilitar el redireccionamento de la
misma manera::

    >>> r = requests.post('http://github.com', allow_redirects=True)
    >>> r.url
    'https://github.com/'
    >>> r.history
    [<Response [301]>]


Timeouts
--------

Con el parámetro ``timeout`` puedes indicarle a Requests que deje de
esperar por una respuesta luego de un número determinado de segundos::

    >>> requests.get('http://github.com', timeout=0.001)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)


.. admonition:: Note:

    ``timeout`` no es el tiempo límite que la respuesta completa se
    descargue; de lo contrario, una excepción se levanta si el servidor
    no ha dado una respuesta dentro de los seguntos establecidos por
    ``timeout`` (más precisamente, si no se han recibido bytes en el
    socket por ``timeout`` segundos)


Errores y excepciones:
----------------------

En el caso de un problema de red (falla de DNS, conexión rechazada, etc),
Requests levantará una excepción tipo :class:`ConnectionError`.

En el caso de una respuesta HTTP inválida, Requests levantará una
excepción tipo :class::`HTTPError`.

Si se cumple el tiempo de espera (*timeout*), se levantará una
excepción tipo :class:`Timeout`.

Si una petición excede el número configurado de redirecciones
máximas, se levantará una excepción tipo :class:`TooManyRedirects`.

Todas las excepciones levantadas por Requests, heredan de
la clase :class:`requests.exceptions.RequestException`.

-----------------------

¿Listo para más? Mira la sección :ref:`avanzado <advanced>`.
