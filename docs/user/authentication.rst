.. _authentication:

Autenticación
=============

En este documento se discuten varios métodos de autenticación con Requests.

Muchos servicios web requieren autenticación, y existen muchas formas de hacerlo.
Abajo, se expondrán las nociones generales de varias formas de autenticación presentes
en Requests, desde lo simple hasta lo complejo.

Autenticación básica
--------------------

Muchos servicios web aceptan autenticación mediante HTTP Basic Auth. Esta es
la forma más sencilla, y es soportada por Requests.

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


Autenticación Digest
--------------------

Otra forma popular de autenticación HTTP es Digest Authenticacion, y
Requests la soporta de manera similar::

    >>> from requests.auth import HTTPDigestAuth
    >>> url = 'http://httpbin.org/digest-auth/auth/user/pass'
    >>> requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
    <Response [200]>


Otras formas de autenticación
-----------------------------

Requests ha sido diseñado para que otras formas de autenticación puedan 
ser incluidas fácilmente. Miembros de la comunidad open-source con frecuencia
escriben handlers de autenticación para formas de autenticación más complicadas,
o que son usadas con menos frecuencia. Algunos de estos handlers han sido
recopilados por la `Requests organization`_, entre los que se incluyen:

- OAuth_
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
implementa el método ``__call__()``. Cuando un handler de autenticación es añadido
a una petición, este es llamado durante la preparación de la misma. Por lo tanto, 
el método ``__call__()`` debe hacer todo lo que se necesite para que la autenticación
funcione. Algunos métodos de autenticación pueden utilizar hooks pare brindar
más funcionalidad.

Algunos ejemplos se encuentran en `Requests organization`_ y en el
archivo ``auth.py``.

.. _OAuth: https://github.com/requests/requests-oauthlib
.. _Kerberos: https://github.com/requests/requests-kerberos
.. _NTLM: https://github.com/requests/requests-ntlm
.. _Requests organization: https://github.com/requests

