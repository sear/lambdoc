.. _working-in-chaman:

Trabajando en el cluster
=======================================

Subiendo y descargando archivos y datos
----------------------------------------

Debemos usar **SCP** (Secure copy protocol) ó **SFTP** (Secure file transfer protocol) para transferir datos y archivos entre su maquina local y el cluster chaman. Usuarios con Unix/Linux y Cygwin pueden usar el comando ``scp``. Usuarios de Windows pueden usar `MobaXTerm <http://mobaxterm.mobatek.net/>`_, `Putty <http://www.putty.org/>`_ y `Filezilla <https://filezilla-project.org/>`_ para SFTP.

Manejando tareas
----------------

Hay multiples comandos disponibles que son bastante utiles para monitorear el estado de las tareas, verificar el estado del cluster y para solucionar problemas.

.. rubric:: showq

| Muestra todos los trabajos en las colas de chaman.
|

.. code-block:: shell 

    $ showq 

.. rubric:: qstat 

| Este comando provee de otra manera de ver información de las colas de ejecución. Recibe multiples argumentos, y los usuarios se les alienta el experimentar puesto que tiene una variedad de opciones para la entrega de información.
|

.. code-block:: shell

    # Muestra el estado de todas las tareas
    $ qstat -a 

.. code-block:: shell

    # Muestra todas las tareas que estan corriendo.
    $ qstat -r    

.. code-block:: shell

    # Muestra información detallada del JodID 12345
    $ qstat -f 12345

.. code-block:: shell
    
    # Mostrar información sobre todas las colas del cluster
    $ qstat -q

.. code-block:: shell

    # Mostrar las tareas en el cluster de un usuario en especifico
    $ qstat -u <usuario>        


.. rubric:: showstart

| Provee de un tiempo estimado para que una tarea en espera se le asignen recursos.

.. code-block:: shell

    $ showstart 12345

.. rubric:: checkjob 

| Provee de información sobre una tarea

.. code-block:: shell

    $ checkjob 123345

.. code-block:: shell

    # Mostrar aun mas información sobre la tarea
    $ checkjob -v 12345

.. rubric:: tracejob

| Muestra la bitacora generada de una tarea. La salida es un poco dificil para leer pero puede ser bastante util para solucionar problemas.

.. code-block:: shell 

    # Muestra información sobre la tarea 12345. (Solo muestra información hasta 24 horas atras)
    $ tracejob 12345

.. code-block:: shell

    # Este comando mostrara la bitacora de 2 dias atras.
    $ tracejob -n 2 12345

.. rubric:: qdel

| Elimina una tarea de la cola.

.. code-block:: shell

    # Elimina la tarea 12345
    $ qdel 12345

..
    Pendiente, que es este comando y como puede ser util
    .. rubric:: mdiag 

    TODO

.. rubric:: pbsnodes

| pbsnodes muestra todos los nodos de computo del cluster asi como sus propiedades y estado.

.. code-block:: shell 

    # Este comando genera mucha información se recomienda usar la pipa y less para navegar por la salida.
    $ pbsnodes | less

.. rubric:: pbs-query 

| pbs-query es una herramienta desarrollada por el equipo de cómputo científico de la dirección de Telematica CICESE, y su objetivo es darle al usuario una visión clara e intuitiva de los recursos que estan siendo utilizados en el cluster. `Repositorio código <https://repohub.cicese.mx/hmedrano/pbs-query>`_

.. code-block:: shell

    # Listado argumentos que recibe el comando:
    $ pbs-query --help
    usage: pbs-query [-h] [--allnodes] [--sort SORTCOL] [--allqueues]
                     [--queue COLA]

    PBSQuery Consultar detalles sobre recursos del cluster

    optional arguments:
      -h, --help            show this help message and exit

    Nodos:
      --allnodes, -an       Mostrar informacion sobre todos los nodos
      --sort SORTCOL, -s SORTCOL
                            Ordenar por: [node-name, core-free]

    Colas:
      --allqueues, -aq      Mostrar detalles de las colas de ejecucion
      --queue COLA, -q COLA
                            Mostrar informacion sobre una cola especifica    

| El siguiente comando lista en columnas los nodos de computo (online), sus recursos disponibles y las tareas que se encuentran corriendo dentro de ellos.

.. code-block:: shell

    $ pbs-query -an 


Compresión netCDF-4
-------------------

La funcionalidad de compresión es una característica exclusiva de la versión de netcdf-4, esta funcionalidad de compresión de datos es heredada de la librería HDF5 y ZLIB (misma librería que usa GZIP). Este tipo de compresión no requiere que modifiquemos los métodos para leer o escribir datos en un archivo que ya tenga algún nivel de compresión. 

*Chunking* o almacenamiento en trozos es una manera para facilitar tanto el acceso rapido a subconjuntos de datos y una compresión eficiente. El *chunking* en netcdf-4 es un atributo que se define para cada variable del archivo netcdf que puede, o no estar comprimido, donde se indica cual sera el tamaño de los trozos en los que se ira almacenando el arreglo.

.. figure:: netcdf-chunking.png
    :scale: 70%
    :align: center

    Almacenamiento continuo y almacenamiento en trozos

Este atributo si se define correctamente beneficia muchisimo el desempeño para el acceso a datos multidimensionales a lo largo de varios ejes. Esto es especialmente cierto para cuando se requiere obtener series de tiempo de archivos grandes, pues se balancean los tiempos de acceso para la dimensión mas lenta (tiempo, profundidad) y las de acceso espacial (x,y).   `Saber mas sobre chunking <https://www.unidata.ucar.edu/blogs/developer/en/entry/chunking_data_why_it_matters>`_

Ademas de estos beneficios, cuando el netcdf tambíen esta comprimido, la lectura de los datos no requiere la descompresión de toda la variable, si no solo los trozos que contienen el subconjunto solicitado.

.. note::
    La librería netcdf-4 utiliza un formato de compresión si perdidas, por lo que se garantiza que cada bit de los datos originales se pueden recuperar.

Aqui las reglas con las que se maneja la compresión de datos en netcdf-4:

* Los datos de la variable es lo que se comprime, no así los metadatos (atributos).
* Se debe especificar un nivel de compresion (*deflate level*) del 1 a 9.
* El nivel 1 pretende comprimir mas rapido que el nivel 9.
* El nivel 9 pretende comprimir mas pequeño que el nivel 1.
* El uso de la dimensión ilimitada (*unlimited dimension*) puede degradar la compresión.
* Una variable comprimida estara forzosamente almacenada en trozos (*chunked*)
* El tamaño de los trozos influye mucho en la calidad de la compresión.
* Para experimentar con la compresión netCDF se puede utilizar la herramienta nativa ``nccopy``, comandos NCO o SDKs de netCDF-4 (ej. `python-netcdf-4 <http://unidata.github.io/netcdf4-python/#section9>`_ )


Ejemplos de compresión
^^^^^^^^^^^^^^^^^^^^^^

**Usando: nccopy**

Usemos datos reales para este ejemplo, un archivo netcdf formato clasico de 1346.20 MB salida del modelo numerico NEMO.

Intentemos usando ``nccopy`` con un nivel de compresión 1, y ver que tanto espacio ganamos.

.. code-block:: shell

    $ nccopy -k 'netCDF-4' -d1 file4D_classic.nc file4D_nc4_d1.nc
    # El argumento -k 'netCDF-4' le indica que estaremos produciendo un archivo del tipo netCDF-4
    # -d1 Indica que usamos el nivel de compresión 1 
    # Tiempo cpu aprox. 19.81 seg.  (promedio de 10 ejecuciones de este mismo comando, en nodo de cómputo de chaman)

    $ ls -l 
    -rw-rw-r-- 1 operador operador 1347M Nov 22 16:30 file4D_classic.nc
    -rw-rw-r-- 1 operador operador  382M Nov 22 16:42 file4D_nc4_d1.nc
    # El archivo final es un 28% del tamaño original.

Ahora, igual usemos ``nccopy`` pero con un nivel de compresión 9, el máximo.

.. code-block:: shell

    $ nccopy -k 'netCDF-4' -d9 file4D_classic.nc file4D_nc4_d9.nc
    # Tiempo de cpu aprox. 70.40 seg. 

    $ ls -l 
    -rw-rw-r-- 1 operador operador 1347M Nov 22 16:30 file4D_classic.nc
    -rw-rw-r-- 1 operador operador  375M Nov 22 17:05 file4D_nc4_d9.nc
    # El archivo final es un 27.7% del tamaño original.

**Usando comandos NCO**

Una mejor alternativa para comprimir netcdf por lo que indican los tiempos de ejecución, son los comandos ``NCO`` `Sitio oficial <http://nco.sourceforge.net>`_.  NCO son una serie de comandos que hacen operaciones sobre archivos netcdf of rutas openDAP, y las salidas que se escriban en netcdf-4 se les puede agregar el parametro del nivel de compresion.

.. note::
    La version mas reciente de estos comandos estan accesibles en el modulo: ``module load intel/cdf-bundle2017`` 

Usando ``ncks`` para hacer una copia del archivo netcdf clasico a netcdf-4 con compresion nivel 1.

.. code-block:: shell

    $ ncks -O -4 -L 1 file4D_classic.nc file4D_nco_nc4_d1.nc
    # -4 Indica que se genera un archivo formato netcdf-4 
    # -L 1 indica el nivel de compresión
    # Tiempo cpu aprox. 13.51 seg. Poco mas de 6 seg. mas rapido que nccopy

    $ ls -l
    -rw-rw-r-- 1 operador operador 1347M Nov 22 16:30 file4D_classic.nc    
    -rw-rw-r-- 1 operador operador  288M Nov 23 00:18 file4D_nco_nc4_d1.nc

Un nivel de compresión 3. **Recomendado**

.. code-block:: shell

    $ ncks -O -4 -L 3 file4D_classic.nc file4D_nco_nc4_d3.nc
    # Tiempo cpu aprox. 14.41 seg.

    $ ls -l
    -rw-rw-r-- 1 operador operador 1347M Nov 22 16:30 file4D_classic.nc  
    -rw-rw-r-- 1 operador operador  283M Nov 23 00:20 file4D_nco_nc4_d3.nc      

Nivel de compresión 9 

.. code-block:: shell

    $ ncks -O -4 -L 9 file4D_classic.nc file4D_nco_nc4_d9.nc
    # Tiempo cpu aprox. 104.23 seg.    

    $ ls -l
    -rw-rw-r-- 1 operador operador 1347M Nov 22 16:30 file4D_classic.nc 
    -rw-rw-r-- 1 operador operador  270M Nov 23 00:41 file4D_nco_nc4_d9.nc   

Comprimiendo multiples archivos
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cuando estemos lidiando con un listado importante de archivos netcdf que necesitemos comprimir, tenemos que tomar otra alternativa como a la de hacer llamados en serie de los comandos anteriores, o dejar corriendo N procesos de compresión al mismo tiempo. Pues con esto podemos crear cuellos de botella que hacen el proceso de compresión sea aun mas lento.  

La alternativa es hacer tandas de procesos compresión y mandarlas como una tarea de PBS. Estas tareas de PBS se pueden declarar como un arreglo de tareas y mandarlas con `qsub`

Script para comprimir multiples archivos netcdf:  `ncdeflate-parallel <https://repohub.cicese.mx/hmedrano/ncdeflate-parallel>`_

Lo que hace este script es tomar un listado de archivos netcdf que se le especifiquen y mandar tareas a PBS con tandas de 20 (default) procesos de compresión. Este numero se utiliza por defecto pues es el numero de procesadores que se tienen el los nodos de computo "default".  Siendo esta la alternativa mas eficiente para comprimir multiples archivos netcdf.

Ejemplo:

.. code-block:: shell 

    $ ./deflatenc.sh -i files-original -o deflated 
     - entradas     : files-original/*.nc
     - salida       : ./deflated
     - compresion   : 3
     - numero archivos comprimir : 704
     - Tareas/ppn : 36 / 20 
    Enviado un arreglo de 36 tareas, cada un con 20 procesos de compresion (NCO), para comprimir un total de 704 archivos netcdf.
    24118[]

    $ qstat 
    Job ID                    Name             User            Time Use S Queue
    ------------------------- ---------------- --------------- -------- - -----     
    24118[]                    ncdeflate        operador              0 R default


Descargar y ver ejemplo en: `ncdeflate-parallel <https://repohub.cicese.mx/hmedrano/ncdeflate-parallel>`_ .




Sistema de archivos LUSTRE
--------------------------

¿Qué es LUSTRE?
^^^^^^^^^^^^^^^

Lustre es un sistema de archivos compartidos de alto desempeño para clusters Linux manejado por el software de Lustre. Es altamente escalable y puede soportar miles de nodos cliente, petabytes de almacenamiento, y un rendimiento de cientos de gigabytes por segundo. La carpeta que que contiene este sistema de archivos se encuentra montado en ``/LUSTRE``.

Cada sistema de archivos Lustre es en realidad un conjunto de varios mini sistemas de archivos, a los que se les llama "Object Storage Targets" (OSTs). El software de Lustre presenta los OSTs como un solo sistema de archivos unificado.

File stripping 
^^^^^^^^^^^^^^

*TODO*

Mas información
---------------

Sugerimos que los usuarios puedan revisar las paginas de manuales y la documentación oficial de PBS en este `vinculo <http://docs.adaptivecomputing.com/torque/6-0-1/help.htm>`_




