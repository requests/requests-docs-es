.. _install:

Instalación
===========

Esta sección de la documentación cubre la instalación de Requests.
El primer paso a la hora de usar cualquier paquete es instalarlo de 
manera apropiada.


Distribute & Pip
----------------

Instalar Requests es simple usando `pip <http://www.pip-installer.org/>`_::

    $ pip install requests

o `easy_install <http://pypi.python.org/pypi/setuptools>`_::

    $ easy_install requests

Pero en realidad `no deberías hacer esto último <http://www.pip-installer.org/en/latest/other-tools.html#pip-compared-to-easy-install>`_.


Cheeseshop Mirror
-----------------

Si el Cheeseshop está abajo, puedes instalar Requests desde uno de los
mirrors. `Crate.io <http://crate.io>`_ es uno de ellos::

    $ pip install -i http://simple.crate.io/ requests


Obtiene el código fuente
------------------------

Requests se está desarrollando activamente en GitHub, allí,
el código está `siempre disponible <https://github.com/kennethreitz/requests>`_.


Puedes elegir entre clonar el repositorio público::

    git clone git://github.com/kennethreitz/requests.git

Descargar el `tarball <https://github.com/kennethreitz/requests/tarball/master>`_::

    $ curl -OL https://github.com/kennethreitz/requests/tarball/master

O descargar el `zipball <https://github.com/kennethreitz/requests/zipball/master>`_::

    $ curl -OL https://github.com/kennethreitz/requests/zipball/master


Una vez obtengas una copia del código fuente, puedes incluirlo en tu instalación
de Python, o instalarlo en site-packages fácilmente::


    $ python setup.py install

