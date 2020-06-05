
Empezando en chaman:
=======================================

.. _granting-access-label:

Permisos para acceder
---------------------
Para acceder al cluster es necesario hacer la solicitud al grupo de investigación que administra chaman. 

.. _logging-in:

Registrarse
-----------
Para registrarte necesitas SSH (Secure Shell). Usuarios windows pueden usar PuTTY, Cygwin o MobaXTerm. Usuarios con OsX pueden usar la aplicación ssh incluida.
El registro se hace en el nodo maestro del cluster, localizado en chaman.cicese.mx. Si se registra desde una linea de comandos, escriba:

.. code-block:: shell

	$ ssh -X <usuario>@chaman.cicese.mx

Donde <usuario>	es el usuario proporcionado por los administradores del cluster. La contraseña también sera proporcionada.


.. _first-job:

Tu primer trabajo en el cluster
-------------------------------

El siguiente script mandara a pantalla el mensaje "Hola mundo" y dormira por 10 segundos, entonces imprimira el tiempo y la fecha. La salida de la consola sera puesta en un archivo. 

.. code-block:: bash
    
    #!/bin/sh

    # Directivas
    #PBS -N HolaMundo
    #PBS -l nodes=1,walltime=00:00:30

    # Definir los archivos de salida de consola y de error
    #PBS -o holamundo.out
    #PBS -e holamundo.err

    # La variable de entorno $PBS_O_WORKDIR es el directorio desde el que se ejecuta "qsub holamundo.sh"
    cd $PBS_O_WORKDIR

    # Imprimir "Hola mundo"
    echo "Hola Mundo"

    # Dormir por 10 segundos
    sleep 10

    # Imprimir la fecha y hora
    date

    # Fin del script

.. _submiting-first-job:

Enviando el trabajo
-------------------

Si su script es salvado como holamundo.sh, envie la tarea al cluster con:

.. code-block:: shell
	
	$ qsub holamundo.sh

Esta tarea creara dos archivos en el directorio de trabajo, uno para las salidas y otro para errores, uno llamado **holamundo.out** y otro **holamundo.err**. Si todo sale bien el primer archivo contendra la salida de la tarea y el segundo estara vacio.

.. note::
	Nota:
	Hay que asegurarse para todas las tareas que enviemos al manejador de tareas que el directorio de trabajo este dentro del espacio asignado en LUSTRE para su cuenta, ej. ``/LUSTRE/<miusuario>/miprimertarea/``

.. _now-what:

¿Ahora que?
-----------

Podemos aprender mas sobre como enviar tareas al cluster en la siguiente pagina :ref:`submit-jobs`. 

:ref:`working-in-chaman` cubre el manejo de las tareas, transferencía de archivos y tareas de ejemplo que nos dan instrucciones mas a profundidad sobre como ejecutar las aplicaciones mas comunes. Almacenamiento explica como se configura y maneja el almacenamiento en Chaman. 

Si deseas saber mas sobre como chaman se configuro la pagina de información tecnica proveé de mas detalles.
