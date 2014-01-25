Filosofía del desarrollo
========================

Requests es una librería abierta pero es dogmática, creada por un
desarrollador abierto pero con opiniones propias.


Dictador Benevolente (*Benevolent Dictador*)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`Kenneth Reitz <http://kennethreitz.org>`_ es el Dictador Benevolente de
por vida, BDFL. Él tiene la última palabra en cualquier decisión
relacionada con Requests.


Valores
~~~~~~~

- Simplicidad siempre es mejor que funcionalidad.
- Escucha a todos, luego has caso omiso de ello.
- El API es todo lo que importa. Todo lo demás es secundario.
- Ajústate al 90% de los casos de uso. Ignora a los pocos que llevan la
contraria.


Versionado semántico
~~~~~~~~~~~~~~~~~~~~

Por muchos años, la comunidad open source ha estado plagada con algún tipo
de distonía en lo que se refiere a la numeración de versiones, las cuales
no tienen ningún sentido práctico.

Requests usa `Versionado Semántico (*Semantic Versioning*)
<http://semver.org>`_. Está especificación tiene como objetivo poner fín
a este sinsentido por medio de un conjunto pequeño de reglas prácticas que
tu y tu colegas pueden usar en sus proyectos.


¿Biblioteca Estándar?
~~~~~~~~~~~~~~~~~~

Requests no tiene ningún plan *activo* para ser incluído en la librería
estándar. Esta decisión ha sido ampliamente discutida con Guido, así como
un buen número de desarrolladores del núcleo de Python.

Esencialmente, porque la biblioteca estándar es donde van a morir las
bibliotecas. Para un modulo es apropiado ser incluido cuando el desarrollo
activo ya no es necesario.

Requests acaba de llegar a su versión 1.0.0. Esto es un gran avance, y
marca un paso en la dirección correcta.


Paquetes en Distros de Linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Se han hecho algunas distribuciones para algunos repositorios de Linux
incluyendo: Ubuntu, Debian, RHEL, y Arch.

Estas distribuciones por lo general corresponden a *forks* que divergen
del principal, o en otros casos no siempre están actualizados con el
último código y con las correcciones de errores. PyPI (y sus espejos) y
GitHub son la fuente oficial de distribución; otras alternativas no están
soportadas en este proyecto.
