.. _advanced:

Uso avanzado
============

Este documento explica el uso de algunas funcionalidades más
avanzadas de Requests.


Objetos de sesión
-----------------

El objeto de sesión o ``Session``, permite mantener ciertos parámetros
a través de múltiples peticiones. De igual forma, almacena las cookies
generadas en todas las peticiones que hayan usado la misma instancia
de :class:`Session`.

Un objeto sesión tiene todos los métodos del API principal de Requests.

Utilicemos una sesión para almacenar las cookies de varias peticiones::

    s = requests.Session()

    s.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
    r = s.get("http://httpbin.org/cookies")

    print r.text
    # '{"cookies": {"sessioncookie": "123456789"}}'


Las sesiones también pueden ser utilizadas para proveer información por
defecto a los métodos de peticiones. Esto se logra asignándole propiedades
a un objeto tipo :class:`Session`::

    s = requests.Session()
    s.auth = ('user', 'pass')
    s.headers.update({'x-test': 'true'})

    # se envían ambos: 'x-test' y 'x-test2'
    s.get('http://httpbin.org/headers', headers={'x-test2': 'true'})


Cualquier diccionario que sea pasado en la petición, será unido con los
valores que fueron asignados a nivel de sesión. Los parámetros asignados
en la petición sobreescribirán los de la sesión.

.. admonition:: Eliminar una entrada en un Diccionario de parámetro.

    En ocasiones, querrás omitir valores asignados a nivel de sesión. Para hacer esto, simplemente asigna ``None``, en la petición, a la llave que se desee omitir.

Todos los valores contenidos dentro de un objeto sesión están disponibles.
Ver :ref:`Session API Docs <sessionapi>` para más información.


Objetos de petición y de respuesta
----------------------------------

Siempre que se hace un llamado a ``requests.get()`` y amigos, está
ocurriendo dos cosas importantes. Primero, se está construyendo un
objeto tipo ``Request``, el cual será enviado a un servidor con el fín de
obtener información de éste. Segundo, un objeto ``Response`` es generado
una vez que ``requests`` obtenga una respuesta del servidor. El objeto
respuesta contiene toda la información entregada por el servidor, así como
el objecto ``Request`` que fue creado originalmente. A continuación una
simple petición para obtener información importante de los servidores de
Wikipedia::

    >>> r = requests.get('http://en.wikipedia.org/wiki/Monty_Python')

Si queremos acceder a las cabeceras que el servidor envió de vuelta,
hacemos lo siguiente::

    >>> r.headers
    {'content-length': '56170', 'x-content-type-options': 'nosniff', 'x-cache':
    'HIT from cp1006.eqiad.wmnet, MISS from cp1010.eqiad.wmnet', 'content-encoding':
    'gzip', 'age': '3080', 'content-language': 'en', 'vary': 'Accept-Encoding,Cookie',
    'server': 'Apache', 'last-modified': 'Wed, 13 Jun 2012 01:33:50 GMT',
    'connection': 'close', 'cache-control': 'private, s-maxage=0, max-age=0,
    must-revalidate', 'date': 'Thu, 14 Jun 2012 12:59:39 GMT', 'content-type':
    'text/html; charset=UTF-8', 'x-cache-lookup': 'HIT from cp1006.eqiad.wmnet:3128,
    MISS from cp1010.eqiad.wmnet:80'}

Sin embargo, si queremos obtener las cabeceras que enviamos al servidor, simplemente
accedemos a la petición, y de ahí, a las cabeceras::

    >>> r.request.headers
    {'Accept-Encoding': 'identity, deflate, compress, gzip',
    'Accept': '*/*', 'User-Agent': 'python-requests/1.2.0'}


Peticiones Preparadas
---------------------

Cuando recibes un objeto :class:`Response <requests.models.Response>`
desde una llamada API o un llamado de Session, el atributo ``request``
es en realidad un ``PreparedRequest``. En algunos casos desearías hacer
algún trabajo adicional al cuerpo o los encabezados (o a cualquier cosa
en realidad) antes de enviar una petición. Lo siguiente es una sencilla
receta para ello::

    from requests import Request, Session

    s = Session()
    req = Request('GET', url,
        data=data,
        headers=header
    )
    prepped = req.prepare()

    # hacer algo con prepped.body
    # hacer algo con prepped.headers

    resp = s.send(prepped,
        stream=stream,
        verify=verify,
        proxies=proxies,
        cert=cert,
        timeout=timeout
    )

    print(resp.status_code)

Debido a que no estás haciendo nada especial con el objeto ``Requests``,
lo prepara inmediatemente y modifica el objeto ``PreparedRequest``.
Entonces lo envía con el otro parámetro que habría sido enviado a
``requests.*`` o``Sesssion.*``.

Sin embargo, el código de arriba perderá algunas de las ventajas de tener
un objeto Requests :class:`Session <requests.Session>`. En especial,
el estado de nivel :class:`Session <requests.Session>` como lo es cookies
no será  aplicado a tu petición. Para obtener un :class:`PreparedRequest <requests.models.PreparedRequest>` con ese estado aplicado, reemplace la llamada
 :meth:`Request.prepare() <requests.Request.prepare>` con una invocación a
 :meth:`Session.prepare_request() <requests.Session.prepare_request>`,
 como lo siguiente::

    from requests import Request, Session

    s = Session()
    req = Request('GET',  url,
        data=data
        headers=headers
    )

    prepped = s.prepare_request(req)

    # do something with prepped.body
    # do something with prepped.headers

    resp = s.send(prepped,
        stream=stream,
        verify=verify,
        proxies=proxies,
        cert=cert,
        timeout=timeout
    )

    print(resp.status_code)

Validación de Certificados SSL
------------------------------

Requests puede verificar certificados SSL para peticiones HTTPS, al igual
que un navegador web. Para validar el certificado SSL de algún host,
podemos utilizar el argumento ``verify``::

    >>> requests.get('https://kennethreitz.com', verify=True)
    requests.exceptions.SSLError: hostname 'kennethreitz.com' doesn't match either of '*.herokuapp.com', 'herokuapp.com'

Debido a que no tengo SSL en este dominio, la petición falla. Intentemos
ahora con GitHub::

    >>> requests.get('https://github.com', verify=True)
    <Response [200]>

Para utilizar certificados privados, puedes pasar la ruta a un archivo
CA_BUNDLE en el parámetro ``verify``, o asignar el valor en la variable
de entorno ``REQUESTS_CA_BUNDLE``.

Requests puede saltarse la verificación si pasas ``verify=False`` en la petición.::

    >>> requests.get('https://kennethreitz.com', verify=False)
    <Response [200]>

Por defecto, ``verify`` tiene el valor ``True``, y solo aplica para
certificados del host.

También puedes especificar un certificado local para utilizar un
certificado en el lado del cliente; existen dos maneras, la primera como
un archivo que contenga la llave privada y el certificado, o como una tupla
con las rutas de ambos archivos::

    >>> requests.get('https://kennethreitz.com', cert=('/path/server.crt', '/path/key'))
    <Response [200]>

Si pasas una ruta inválida, o un certificado inválido::

    >>> requests.get('https://kennethreitz.com', cert='/wrong_path/server.pem')
    SSLError: [Errno 336265225] _ssl.c:347: error:140B0009:SSL routines:SSL_CTX_use_PrivateKey_file:PEM lib


Workflow del cuerpo del contenido
---------------------------------

Por defecto, cuando realizas una petición, el cuerpo de la respuesta es
descargado inmediatamente. Este comportamiento se puede cambiar,
postergando la descarga al momento en el que se acceda el atributo
:class:`Response.content`, con el parámetro ``stream=True``::

    tarball_url = 'https://github.com/kennethreitz/requests/tarball/master'
    r = requests.get(tarball_url, stream=True)

En este momento, únicamente las cabeceras de respuesta han sido
descargadas, y la conexión permanece abierta, lo que nos permite realizar
una descarga del contenido condicionada::

    if int(r.headers['content-length']) < TOO_LONG:
      content = r.content
      ...

Puedes controlar aún más este *workflow* utilizando los métodos
:class:`Response.iter_content` y :class:`Response.iter_lines`, o leyendo
desde la clase de urllib3 subyacente :class:`urllib3.HTTPResponse` en
:class:`Response.raw`.

Si condiguras ``stream`` a ``True`` mientras estás haciendo una petición,
Requests no puede liberar la conexión al *pool* a menos de que consumas
todos los datos o llames a :class:`Response.close <requests.Response.close>`.
Esto puede llevarte a ineficiencia con las conexiones. Si te encuentras
leyendo cuerpos de peticiones (o no leyéndolos del todo) mientras estás
usando ``stream=True``, deberías considerar el usar ``contextlib.closing``
(`documentado aquí`_), así::

    from contextlib import closing

    with closing(requests.get('http://httpbin.org/get', stream=True)) as r:
        # hacer cosas con la respuesta.

.. _`documentado aquí`: http://docs.python.org/2/library/contextlib.html#contextlib.closing

Keep-Alive
----------

Buenas noticias - gracias a urllib3, *keep-alive* es 100% automático
dentro de una sesión! Cualquier petición que se ejecute dentro de una
sesión, reutilizará la conexión apropiada!

Note que las conexiones sólo son devueltas a la piscina *pool* una vez se
haya leído toda la información en el cuerpo de la respuesta. Asegúrese
de pasar ``stream=False``, o de leer la propiedad ``content`` del objeto
``Response``.


Subir por Streaming
-------------------

Requests soporta subidas por *streaming*, lo cual permite enviar archivos
pesados sin leerlos en memoria. Para usar esta funcionalidad, simplemente
debes proveer un objeto tipo archivo para el cuerpo de la petición::

    with open('massive-body') as f:
        requests.post('http://some.url/streamed', data=f)


Peticiones Fragmentadas *Chunk-Encoded*
---------------------------------------

Requests también soporta transferencias fragmentadas para peticiones de
entrada y salida. Para enviar una petición por fragmentos, simplemente
debes proveer un objeto generador (o cualquier iterador sin tamaño) para
el cuerpo de la petición::

    def gen():
        yield 'hi'
        yield 'there'

    requests.post('http://some.url/chunked', data=gen())


Hooks de eventos
----------------

Requests tiene un sistem de *hooks* que puedes utilizar para manipular
porciones del proceso de petición, o manipulación de señales.

*Hooks* disponibles:

``response``:
    La respuesta generada a partir de Request.

Puedes asignar una función a este *hook* en cada petición, pasando
un diccionario ``{hook_name: callback_funcion}`` al parámetro ``hooks``
de la misma::

    hooks=dict(response=print_url)

La función ``callback_function`` recibirá una porción de datos como su
primer argumento.

::

    def print_url(r, *args, **kwargs):
        print(r.url)

Si ocurre algún error mientras se ejecuta el *callback*, se emitirá una
advertencia.

Si la función *callback* regresa algún valor, es asumido que este valor
reemplazará a los datos que le fueron pasados originalmente. Si la
función no regresa ningún valor, nada más es afectado.

Imprimamos algunos argumentos de la petición en tiempo de ejecución::

    >>> requests.get('http://httpbin.org', hooks=dict(response=print_url))
    http://httpbin.org
    <Response [200]>


Autenticación personalizada
---------------------------

Requests permite especificar tu propio mecanismo de autenticación.

Cualquier objeto invocable (*callable*) que se pase en el parámetro
``auth`` en una petición, podrá modificar esta petición antes de que sea
ejecutada.

Las implementaciones de autenticación son clases heredadas de
``requests.auth.AuthBase`` y son fáciles de definir. Requests provee
implementaciones de dos formas de autenticación comunes en
``requests.auth``: ``HTTPBasicAuth`` y ``HTTPDigestAuth``.

Supongamos que tenemos un servicio web que responderá únicamente si la
cabecera ``X-Pizza`` contiene cierta contraseña. Es poco probable, pero
es un buen ejemplo.

::

    from requests.auth import AuthBase

    class PizzaAuth(AuthBase):
        """Attaches HTTP Pizza Authentication to the given Request object."""
        def __init__(self, username):
            # configurar cualquier dato de auth-related aquí
            self.username = username

        def __call__(self, r):
            # modify and return the request
            r.headers['X-Pizza'] = self.username
            return r

Ahora, podemos crear una petición usando nuestra implementación de Pizza
Auth::

    >>> requests.get('http://pizzabin.org/admin', auth=PizzaAuth('kenneth'))
    <Response [200]>


Peticiones en streaming
-----------------------

Usando ``requests.Response.iter_lines()`` puedes iterar fácilmente sobre
APIs de streaming como el `API de Streaming de Twitter
<https://dev.twitter.com/docs/streaming-api>`_. Configura ``stream`` a
``True`` e itera con la respuesta usando :class:`~requests.Response.iter_lines()`::



    import json
    import requests

    r = requests.get('http://httpbin.org/stream/20', stream=True)

    for line in r.iter_lines():

        # filter out keep-alive new lines
        if line:
            print json.loads(line)


Proxies
-------

Si necesitas utilizar un proxy, puedes configurar peticiones individuales
usando el argumento ``proxies`` de la petición::

    import requests

    proxies = {
      "http": "http://10.10.1.10:3128",
      "https": "http://10.10.1.10:1080",
    }

    requests.get("http://example.org", proxies=proxies)

También puedes configurar proxies por medio de las variables de entorno ``HTTP_PROXY`` y ``HTTPS_PROXY``.

::

    $ export HTTP_PROXY="http://10.10.1.10:3128"
    $ export HTTPS_PROXY="http://10.10.1.10:1080"
    $ python
    >>> import requests
    >>> requests.get("http://example.org")

Para usar HTTP Basi Auth con tu proxy, debe utilzar la sintáxis `http://user:password@host/`::

    proxies = {
        "http": "http://user:pass@10.10.1.10:3128/",
    }


Conformidad
-----------

Requests está pensado para que sea conforme con todas las especificaciones
que apliquen, así como con RFCs, siempre y cuando esto no traiga consigo
complicaciones para los usuarios. Estos cuidados con las especificaciones
pueden llevar a comportamientos que para algunas personas que no
estén familiarizadas con ellas.


Codificaciones
^^^^^^^^^^^^^^

Cuando recibes una respuesta, Requests supone automáticamente la
codificación a usar cuando accesas al atributo :attr:`Response.text
<requests.Response.text>`. Requests primero verificará alguna
codificación en el encabezado HTTP, si no se ha especificado, se
utilizará `charade <http://pypi.python.org/pypi/charade>`_ para intentar
adivinar la codificación.

La única ocasión en la que Requests no intentará adivinar la codificación, es
cuando no hay un *charset* explícito en las cabeceras HTTP **y** la cabecera
``Content-Type`` contiene ``text``. En tal caso, el `RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.7.1>`_ especifica que el
*charset* por defecto será ``ISO-8859-1``. Requests obedecerá la
especificación en este caso. Si se necesita una codificación diferente,
puedes establecer manualmente el atributo :attr:`Response.encoding
<requests.Response.encoding>` o usar :attr:`Response.content <requests.Response.content>`.


Verbos HTTP
-----------

Requests provee acceso a casi todo el rango de verbos HTTP: GET, OPTIONS,
HEAD, POST, PUT, PATCH y DELETE. A continuación, se expondrán algunos
ejemplos detallados de como usar estos verbos en Requests, usando el API
de GitHub.

Comenzaremos con el verbo más común: GET. HTTP GET es un método idempotente
el cual regresa un recurso a partir de una URL; por lo tanto, este verbo
es utilizado cuando se quiere obtener información desde una ubicación web.
Un ejemplo de uso, es el de obtener información acerca de un commit
específico en GitHub. Supongamos que queremos obtener el commit
``a050faf`` de Requests. Lo hacemos de la siguiente manera::

    >>> import requests
    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/git/commits/a050faf084662f3a352dd1a941f2c7c9f886d4ad')

Debemos confirmar que GitHub respondió correctamente; en caso afirmativo, queremos
conocer el tipo de contenido, así::

    >>> if (r.status_code == requests.codes.ok):
    ...     print r.headers['content-type']
    ...
    application/json; charset=utf-8

De tal manera que GitHub regresa JSON. Genial, podemos utilizar el método ``r.json``
para procesarlo en objetos de Python.

::

    >>> commit_data = r.json()
    >>> print commit_data.keys()
    [u'committer', u'author', u'url', u'tree', u'sha', u'parents', u'message']
    >>> print commit_data[u'committer']
    {u'date': u'2012-05-10T11:10:50-07:00', u'email': u'me@kennethreitz.com', u'name': u'Kenneth Reitz'}
    >>> print commit_data[u'message']
    makin' history

Hasta ahora todo ha sido sencillo. Pues bien, vamos a investigar el GitHub
un poco más. Ahora podríamos ver la documentación, pero podríamos
divertirnos un poco más si usáramos Requests. Podemos utilizar el verbo
OPTIONS soportado por Requests para ver qué tipo de métodos HTTP están
soportados en la URL que acabamos de utilizar.

::

    >>> verbs = requests.options(r.url)
    >>> verbs.status_code
    500

¿Qué? ¡Esto no nos ayuda! Resulta que GitHub, al igual que muchos
proveedores de APIs, no implementan el método OPTIONS. Esto es algo
molesto, pero está bien, podemos utilizar la aburrida documentación. Si
GitHub hubiese implementado correctamente el verbo OPTIONS, debería
regresar los métodos permitidos en las cabeceras, por ejemplo:

::

    >>> verbs = requests.options('http://a-good-website.com/api/cats')
    >>> print verbs.headers['allow']
    GET,HEAD,POST,OPTIONS

Al observar la documentación, vemos que solo hay otro método permitido
para commits, el cual es POST, y lo que hace es crear un nuevo commit.
Debido a que estamos utilizando el repositorio de Requests, vamos a
evitar crear POSTS manualmente. En lugar de esto, vamos a jugar un poco
con la funcionalidad de Issues de GitHub.

Esta documentación fue agregada en respuesta al Issue #482. Dado que
este reporte ya existe, vamos a utilizarlo como ejemplo. Vamos a empezar
por obtener este recurso.

::

    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/issues/482')
    >>> r.status_code
    200
    >>> issue = json.loads(r.text)
    >>> print issue[u'title']
    Feature any http verb in docs
    >>> print issue[u'comments']
    3

Bien, ahora tenemos tres comentarios. Ahora, miremos el último de los comentarios.

::

    >>> r = requests.get(r.url + u'/comments')
    >>> r.status_code
    200
    >>> comments = r.json()
    >>> print comments[0].keys()
    [u'body', u'url', u'created_at', u'updated_at', u'user', u'id']
    >>> print comments[2][u'body']
    Probably in the "advanced" section

Bueno, esto parece ser algo tonto. Vamos a postear un comentario diciéndole
al posteador que es un tonto. Quién es el posteador?

::

    >>> print comments[2][u'user'][u'login']
    kennethreitz

Esta bien, vamos a decirle a este sujeto Kenneth que pensamos que este
ejemplo debe ir en la sección *quickstart*. De acuerdo con la
documentación del API de GitHub, la forma de hacer esto es haciendo un
POST a la conversación (*thread*). Hagámoslo.

::

    >>> body = json.dumps({u"body": u"Sounds great! I'll get right on it!"})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/482/comments"
    >>> r = requests.post(url=url, data=body)
    >>> r.status_code
    404

¿?, Esto es extraño. Probablemente necesitemos autenticarnos. Esto será
problemático, verdad? Pues no, Requests hace que usar varios métodos de
autenticación sea fácil, incluyendo Basic Auth.

::

    >>> from requests.auth import HTTPBasicAuth
    >>> auth = HTTPBasicAuth('fake@example.com', 'not_a_real_password')
    >>> r = requests.post(url=url, data=body, auth=auth)
    >>> r.status_code
    201
    >>> content = r.json()
    >>> print content[u'body']
    Sounds great! I'll get right on it.

Genial. Mmm ¡No, espera! Quería agregar que me tomará un tiempo, ya que
tengo que alimentar a mi gato. Si tan solo pudiera editar este comentario!
Por fortuna, GitHub nos permite usar el verbo HTTP PATCH para editar
este comentario. Vamos a hacerlo.

::

    >>> print content[u"id"]
    5804413
    >>> body = json.dumps({u"body": u"Sounds great! I'll get right on it once I feed my cat."})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/comments/5804413"
    >>> r = requests.patch(url=url, data=body, auth=auth)
    >>> r.status_code
    200

Excelente. Ahora, solo por hacerle la vida imposible a este sujeto
Kenneth, he decidido hacerle preocupar al no informarle que estoy
trabajando en esto. Esto quiere decir que quiero eliminar este
comentario. GitHub nos permite eliminar comentarios utilizando el método
DELETE. Vamos a deshacernos de este comentario.

::

    >>> r = requests.delete(url=url, auth=auth)
    >>> r.status_code
    204
    >>> r.headers['status']
    '204 No Content'

Excelente. Se ha ido. Por último, quiero saber qué tanto he utilizado el
API. GitHub envía esta información en las cabeceras, así que en vez de
descargar la página completa, voy a enviar una petición tipo HEAD, para
obtener los encabezados.

::

    >>> r = requests.head(url=url, auth=auth)
    >>> print r.headers
    ...
    'x-ratelimit-remaining': '4995'
    'x-ratelimit-limit': '5000'
    ...

Excelente. Es hora de escribir una aplicación en Python que abuse del
API de GitHub otras 4995 veces.


Link Headers
------------

Muchas APIs soportan *Link headers*. Estas cabeceras hacen que las APIs
sean más auto-descriptivas y detectables.

GitHub las utiliza para `paginación <http://developer.github.com/v3/#pagination>`_
en su API, por ejemplo::

    >>> url = 'https://api.github.com/users/kennethreitz/repos?page=1&per_page=10'
    >>> r = requests.head(url=url)
    >>> r.headers['link']
    '<https://api.github.com/users/kennethreitz/repos?page=2&per_page=10>; rel="next", <https://api.github.com/users/kennethreitz/repos?page=6&per_page=10>; rel="last"'

Requests procesará automáticamente estas cabeceras y hará que sean
fácilmente utilizables::

    >>> r.links["next"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=2&per_page=10', 'rel': 'next'}

    >>> r.links["last"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=7&per_page=10', 'rel': 'last'}

Transport Adapters
------------------

A partir de v1.0.0, Requests se movió a un diseño interno de tipo modular.
Parte de las razones de ello fue el implementar los Adaptores de
Transporte (*Transport Adapters*), originalmente `descritas aquí`_.
Los Transport Adapters proveen un mecanismo para definir métodos de
interacción para un servicio HTTP.En especial, permiten aplicar
configuración por cada servicio (*per-service*).

Request viene con un Transpor Adapter sencillo, el :class:`HTTPAdapter
<requests.adapters.HTTPAdapter>`. Este adaptador provee la interacción
por defecto de Request con HTTP y HTTPS usando la poderosa biblioteca
 `urllib3`_. En cualquier momento en que alguna clase Request se
inicializa, uno de estos es adjuntada al objeto :class:`Session <requests.Session>`
para HTTP y otra para HTTPS.

Requests permite a los usuarios crear y usar sus propios Transport
Adapters que proveean funcionalidad específica. Una vez creados, un
Transport Adapter puede ser montado en un objeto ``Session``, junto con
una indicación de cuáles servicios web debería aplicar.

::

    >>> s = requests.Session()
    >>> s.mount('http://www.github.com', MyAdapter())

El montaje llama a registros de instancias específicas de un
Transport Adapter a un prefijo. Una vez montada, cualquier petición
HTTP hecha con esa sesión cuya URL inicie con el prefijo dado usará
dicho Transport Adapter.

Muchos de los detalles de implementar un Transport Adapter está más allá
del alcance de esta documentación, pero mira en el siguiente ejemplo
para caso de uso sencillo de SSL. Para más sobre el asunto, deberías
mirar en la subclase ``requests.adapters.BaseAdapter``.

Ejemplo: Versión Específica de SSL
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

El equipo de Requests ha hecho una elección específica en usar cualquier
versión SSL por defecto en la biblioteca (`urllib3`_). Normalmente esto
está bien, pero de vez en vez, podrías encontrarte en la necesidada de
conectarte a un *service-endpoint* que usa una versión que no es
compatible con la default.

Puedes usar Transport Adapters para esto al tomar la mayoría de la
implementación existente de HTTPAdaptar, y agregando un parámetro
*ssl_version* que obtiene pasando por `urllib3`. Haremos un TA que
instruya a la biblioteca a usar SSLv3:

::

    import ssl

    from requests.adapters import HTTPAdapter
    from requests.packages.urllib3.poolmanager import PoolManager


    class Ssl3HttpAdapter(HTTPAdapter):
        """"Transport adapter" that allows us to use SSLv3."""

        def init_poolmanager(self, connections, maxsize, block=False):
            self.poolmanager = PoolManager(num_pools=connections,
                                           maxsize=maxsize,
                                           block=block,
                                           ssl_version=ssl.PROTOCOL_SSLv3)

.. _`descrita aquí`: http://kennethreitz.org/exposures/the-future-of-python-http
.. _`urllib3`: https://github.com/shazow/urllib3

Bloqueante o no-Bloqueante
--------------------------

Con el Transport Adapter puesto en su sitio, Requests no provee ningún
tipo de IO no-bloqueante. La propiedad :attr:`Response.content <requests.Response.content>` bloquerá hasta que la respuesta completa haya sido descargada.
Si se requiere más granularidad, la característica de *streaming* de la
biblioteca (vea :ref:`streaming-requests`) permite obtener pequeñas
cantidades de una respuesta a la vez. Sin embargo, esas llamadas serán
aún bloqueantes.

Si tienes preocupación sobre el uso de IO bloqueante, hay muchos proyectos
por ahí que combinan Requests con alguno de los Framework asincrónicos
de Python. Dos excelentes ejemplos son `grequests`_ y `requests-futures`_.

.. _`grequests`: https://github.com/kennethreitz/grequests
.. _`requests-futures`: https://github.com/ross/requests-futures
