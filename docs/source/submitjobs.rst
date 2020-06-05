.. _submit-jobs:

Enviar tareas al cluster chaman
===============================

Tareas por lotes
^^^^^^^^^^^^^^^^

Tareas por lotes o *"Batch jobs"* son tareas que no interactuan con el usuario durante su ejecución. Son enviadas una vez y son canalizadas a la cola de ejecucion. 

.. 
    Tal vez incluir detalles sobre los limites de memoria o de duracion de las tareas de los usuarios.

Las especificaciones de varios ajustes y las llamadas al software a ser ejecutado estan contenidas en el script de envio creado y editado por el usuario.

De forma general para enviar una tarea al manejador de colas es usar el comando de PBS Torque **qsub**.

.. code-block:: shell

    $ qsub script_tarea.sh

Donde ``script_tarea.sh`` contiene información sobre la tarea, junto a la llamada a la aplicación.

Todos las tareas seran enviadas desde los nodos maestros ``chaman`` y ``kanik2``.  Un ejemplo de una tarea simple y de como enviar la tarea a ejecución se encuentra en la sección :ref:`first-job`.

Ejemplos mas avanzados se pueden revisar en :ref:`job-examples`.

Es importante recordar que las salidas y archivos de error que generen nuestras tareas deben escribirse en la sección del directorio LUSTRE que se nos asigno.


Tareas interactivas
^^^^^^^^^^^^^^^^^^^^^

Las tareas interactivas se especifican usando el parametro ``-I`` en el comando ``qsub``. Permite la interaccion del usuario durante su ejecución. Entregan al usuario un nuevo *shell* desde donde se pueden llamar a las aplicaciones. 

.. 
    Existen filtros o restricciones para las tareas interactivas en chaman? , donde estan descritas, pues es importante informarselas a los usuarios desde un inicio.

La forma general para enviar tareas interactivas es:

.. code-block:: shell
    
    $ qsub -X -I -q default -l nodes=1 -d $PWD

Donde ``-I`` es requerido y especifica una tarea interactiva. El parametro ``-X`` reenvia el servidor grafico del nodo que se nos asigno a nuestra sesión, permitiendo usar aplicaciones graficas.

Si un nodo esta disponible, el comando nos devuelve un *shell* en el nodo que se nos asigno, en caso contrario el comando se quedara en espera.


Directivas basicas
^^^^^^^^^^^^^^^^^^

Las siguientes opciones se pueden encotnra en un script para envio de tareas usando ``qsub``. Pueden aparecer como lineas en el script que comienzan con ``#PBS`` o como parametros en linea al comando ``qsub``. 

+------------------------+------------------------------------+---------------------------+--------------------------------+ 
| Opción                 | Descripción                        | Ejemplo                   | Notas                          |
+========================+====================================+===========================+================================+ 
|``#PBS -q nombre-cola`` | Asigna una cola de ejecución       | ``#PBS -q mem256``        | Asigna el job a la cola mem256 |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 
|``#PBS -N nombreTarea`` | Le da un nombre a la tarea         | ``#PBS -N MiTarea``       | Por defecto usa el nombre del  |
|                        |                                    |                           | script de envio de la tarea    |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 
|``#PBS -l mem=<Tamaño>``| Maxima cantidad de memoria fisica  | ``#PBS -l mem=200MB``     | Si se excede de esa cantidad   |
|                        | que podra usar nuestra tarea (MB)  |                           | la tarea es abortada           |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 
|``#PBS -l               | Tiempo maximo que la tarea puede   |``#PBS -l                  | 12 horas en ejecución como     |
|walltime=<tiempo>``     | permanecer en ejecución.           |walltime=12:00:00``        | maximo                         |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 
|``#PBS -l               | Numero de nodos y el numero de     |``#PBS -l nodes=6:ppn=20`` | Por defecto es 1 nodo 1 core   |
|nodes=N:ppn=T``         | cores por nodo que solicitamos     |                           |                                |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 
|``#PBS -M <email>``     | Correo electronico para            |``#PBS -M                  | Recomendado para tareas        |
|                        | notificaciones de estado de tarea. |hmedrano@cicese.mx``       | por lotes                      |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 
|``#PBS -o <ruta>``      | Ruta para el archivo de salida de  |``#PBS -o                  | Salida estandar.               |
|                        | la consola.                        |salida.out``               |                                |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 
|``#PBS -e <ruta>``      | Ruta al archivo de errores.        |``#PBS -e                  | Mensajes de error estandar.    |
|                        |                                    |errores.err``              |                                |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 
|``#PBS -j oe``          | Opción para combinar la salida     |``#PBS -j oe``             | Combina el error estandar y    |
|                        | estandar y error en un solo archivo|                           | salida estandar al mismo       |
|                        |                                    |                           | archivo definido en -o         |
|                        |                                    |                           |                                |
|                        |                                    |                           |                                |
+------------------------+------------------------------------+---------------------------+--------------------------------+ 

Variables de ambiente PBS
^^^^^^^^^^^^^^^^^^^^^^^^^

+-----------------------------+-----------------------------------------------------------------------+ 
| Nombre variable             | Descripción                                                           |
+=============================+=======================================================================+
| $PBS_NODEFILE               | Ruta del archivo que contiene la lista de los nodos asignados por PBS.|
+-----------------------------+-----------------------------------------------------------------------+
| $PBS_O_WORKDIR              | Directorio desde donde qsub fue llamado.                              |
+-----------------------------+-----------------------------------------------------------------------+
| $PBS_O_INITDIR              | Directorio de trabajo para la tarea (Especificado por la directiva    |
|                             | ``-d`` de qsub                                                        |
+-----------------------------+-----------------------------------------------------------------------+
| $PBS_JOBNAME                | Nombre de la tarea especificado por el usuario.                       |
+-----------------------------+-----------------------------------------------------------------------+
| $PBS_O_LOGNAME              | Nombre del usuario que envía la tarea.                                |
+-----------------------------+-----------------------------------------------------------------------+
| $PBS_NUM_PPN                | Numero de procesadores por nodo solicitados (ppn)                     |   
+-----------------------------+-----------------------------------------------------------------------+
| $PBS_JOBID                  | Numero de identificación unico de la tarea.                           |
|                             |                                                                       |
|                             |                                                                       |
|                             |                                                                       |
|                             |                                                                       |
|                             |                                                                       |
+-----------------------------+-----------------------------------------------------------------------+ 

El listado de variables completo esta disponible en la documentación oficial de `Torque <http://docs.adaptivecomputing.com/torque/6-0-1/help.htm#topics/torque/2-jobs/exportedBatchEnvVar.htm>`_.

