Cómo ayudar
===========

Requests está bajo desarrollo activo, tus contribuciones son más que bienvenidas!

#. Revisa los reportes (*issues*) abiertos, o abre uno nuevo para empezar una discusión acerca de un error o bug.
   Existe una etiqueta llamada *Contributor Friendly* (amigable al contribuyente), para reportes que son ideales
   para personas que no están familiarizadas con el código fuente aún.
#. Haz un *fork* `al repositorio <https://github.com/kennethreitz/requests>`_ en GitHub y hazle cambios a tu 
   propia *branch*.
#. Escribe una prueba que demuestre que se ha solucionado el problema.
#. Envía un *pull request* y molesta al mantenedor hasta que este sea mergido y publicado. :)
   Asegúrate de agregarte a lista de autores: `AUTHORS <https://github.com/kennethreitz/requests/blob/master/AUTHORS.rst>`_.


Feature Freeze
--------------

A partir de la versión 1.0.0, Requests ha entrado en un estado de *feature freeze* (congelamiento de funcionalidades). 
No serán aceptados nuevas peticiones y *pull requests* para agregar nuevas funcionalidades 


Dependencias en el desarrollo
-----------------------------

Necesitarás instalar py.test con el fín ejecutar la suite de pruebas de Requests::

    $ make test-deps
    $ make test
    py.test
    platform darwin -- Python 2.7.3 -- pytest-2.3.4
    collected 25 items

    test_requests.py .........................
    25 passed in 3.50 seconds


Entornos de ejecución
---------------------

Actualmente, Requests soporta las siguientes versión de Python:

- Python 2.6
- Python 2.7
- Python 3.1
- Python 3.2
- Python 3.3
- PyPy 1.9

Soporte para Python 3.1 y 3.2 pueden ser incluídos en cualquier momento.

Nunca se soportará oficialmente Google App Engine. *Pull requests* para mejorar la compatibilidad con este serán aceptados, siempre y cuando
no introduzcan complicaciones en el código.


Estás loco?
-----------

- Soporte para SPDY sería genial. Pero que no usen extensiones en C.
