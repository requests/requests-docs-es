.. _authentication:

Autenticación
=============

En este documento se discuten varios métodos de autenticación con Requests.

Muchos servicios web requieren autenticación, y existen muchas formas de
hacerlo. Abajo, se expondrán las nociones generales de varias formas de
autenticación presentes en Requests, desde lo simple hasta lo complejo.

Autenticación Básica
--------------------

Muchos servicios web aceptan autenticación mediante HTTP Basic Auth.
Esta es la forma más sencilla, y es soportada por Requests.

Ejecutar peticiones con autenticación básica HTTP es sencillo::

    >>> from requests.auth import HTTPBasicAuth
    >>> requests.get('https://api.github.com/user', auth=HTTPBasicAuth('user', 'pass'))
    <Response [200]>

De hecho, HTTP Basic Auth es tan común, que Requests provee una forma más
fácil de usarla::

    >>> requests.get('https://api.github.com/user', auth=('user', 'pass'))
    <Response [200]>

Pasar las credenciales en un tupla de esta manera, es exactamente igual
a utilizar ``HTTPBasicAuth`` como en el primer ejemplo.

Autenticación netrc
~~~~~~~~~~~~~~~~~~~

Si no se ha dado ningún método de autenticación en el argumento ``auth``,
Requests intentará obtener las credenciales de autenticación para el
hostname del URL del archivo de usuario netrc.

Si se encuentran las credenciales para el hostname, la petición es enviada
con HTTP Basic
Auth.

Autenticación Digest
--------------------

Otra forma popular de autenticación HTTP es Digest Authenticacion, y
Requests la soporta de manera similar::

    >>> from requests.auth import HTTPDigestAuth
    >>> url = 'http://httpbin.org/digest-auth/auth/user/pass'
    >>> requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
    <Response [200]>

Autenticación OAuth 1
---------------------

Una forma común de autenticación para varios API Web es OAuth. La
biblioteca ``requests-oauthlib`` permite a los usuarios de Requests
el hacer peticiones de autentición OAuth con facilidad::


    >>> import requests
    >>> from requests_oauthlib import OAuth1

    >>> url = 'https://api.twitter.com/1.1/account/verify_credentials.json'
    >>> auth = OAuth1('YOUR_APP_KEY', 'YOUR_APP_SECRET',
                      'USER_OAUTH_TOKEN', 'USER_OAUTH_TOKEN_SECRET')

    >>> requests.get(url, auth=auth)
    <Response [200]>

Para más información en cómo funciona OAuth, por favor vea el website
oficial de `OAuth`_ website.
Para ejemplos y documentación con requests-oauthlib, por favor vea el
repositorio `requests_oauthlib`_ en GitHub.

Otras formas de autenticación
-----------------------------

Requests está diseñado para permitir que otras formas de autenticación
puedan ser incluidas fácilmente. Los miembros de la comunidad
open-source con frecuencia escriben *handlers* de autenticación para
formas de autenticación más complicadas o que son usadas con menos
frecuencia. Algunos de estos handlers han sido recopilados por la
`Requests organization`_, entre los que se incluyen:

- Kerberos_
- NTLM_

Si quieres usar alguna de estas formas de autenticación, dirígete a su
página en GitHub y sigue las instrucciones.


Nuevas formas de autenticación
------------------------------

Si no logras encontrar una buena implementación del método de autenticación
que deseas utilizar, puedes implementarlo tu mismo. Requests hace que sea fácil
agregar tu propia forma de autenticación.

Para hacer esto, escribe una clase que herede de :class:`requests.auth.AuthBase` e
implementa el método ``__call__()``::

    >>> import requests
    >>> class MyAuth(requests.auth.AuthBase):
    ...     def __call__(self, r):
    ...         # Implementar mi autenticación
    ...         return r
    ...
    >>> url = 'http://httpbin.org/get'
    >>> requests.get(url, auth=MyAuth())
    <Response [200]>

Cuando un handler de autenticación es añadido a una petición, éste es
llamado durante la preparación de la misma. Por lo tanto. El método
``__call__()`` debe hacer todo lo que se necesite para que la
autenticación funcione. Algunos métodos de autenticación adicionarán hooks
para brindar más funcionalidad.

Algunos ejemplos se encuentran en `Requests organization`_ y en el
archivo ``auth.py``.

.. _OAuth: http://oauth.net/
.. _requests_oauthlib: https://github.com/requests/requests-oauthlib
.. _Kerberos: https://github.com/requests/requests-kerberos
.. _NTLM: https://github.com/requests/requests-ntlm
.. _Requests organization: https://github.com/requests

