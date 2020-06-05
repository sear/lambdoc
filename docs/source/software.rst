
******************************
Software disponible en chaman
******************************

Uso de modulos
--------------

Con este sistema se puede modificar de forma dinamica el ambiente de la sesión del usuario, esto con la intención de facilitar el uso de las diferentes herramientas de desarrollo instaladas en chaman. 

Si se desea que se cargue un cierto módulo cada vez que se inicie sesión hay que agregar al archivo *.bashrc* la siguiente linea: 

.. code-block:: shell     

     module load intel/MPI2017 


Mandos utiles de modules
^^^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: shell    
   :emphasize-lines: 1   

    $ module avail

| El cual listara los siguientes modulos definidos: 
|

.. code-block:: shell     

    ---------------------------------------------- /usr/share/Modules/modulefiles -----------------------------------------------
    anaconda-python-2.7      intel/hdf5               intel/netcdf4.2          modules
    anaconda-python-3.5      intel/hdf5-paralelo      intel/netcdf4.2-paralelo ncl_gnu-6.4.0
    dot                      intel/MPI2013            intelpython2             null
    git-2.9.5                intel/MPI2017            julia-v0.5.0             opt-python
    gnu/netcdf4.2            intel/MPI2018            julia-v1.0.0             rocks-openmpi
    intel/cdf-bundle2013     intel/ncl-6.3.0          matlab-R2014b            rocks-openmpi_ib
    intel/cdf-bundle2017     intel/netcdf3.6.2        module-git               singularity
    intel/cdf-bundle2018     intel/netcdf3.6.3        module-info              use.own


|

.. code-block:: shell    
   :emphasize-lines: 1   

   $ module load intel/cdf-bundle2018

| Para cargar el modulo de las librerias y binarios de netcdf version 4.5, mas herramientas como NCO, Ncview, CDO.
|
|

.. code-block:: shell    
   :emphasize-lines: 1   

   $ module list

| Listara los modulos que estan cargados: ej
|

.. code-block:: shell     

   Currently Loaded Modulefiles:
      1) intel/cdf-bundle2018

|

.. code-block:: shell    
   :emphasize-lines: 1   

   $ module unload intel/cdf-bundle2018

| Para descargar el modulo.
|
|

.. code-block:: shell    
   :emphasize-lines: 1   

   $ module load intel/MPI2013

| Para cargar la version de MPI de intel 11.1 
|
|

.. code-block:: shell    
   :emphasize-lines: 1   

   $ module display intel/MPI2013
   
| Muestra las variables de entorno definidas en el modulo de MPI intel 11.1 2013
|
|


Al enviar las tareas usando PBS, al script hay que agregarle el modulo que se va implementar por ej. 
``module load intel/MPI2013``

.. note:: 
    Puede no ser necesario definir el modulo en el script de PBS si el modulo deseado ya se esta cargando de forma automatica en el archivo .bashrc

Matlab
------

¿Que versión de matlab esta disponible?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

En chaman se encuentra instalada la version R2014b de matlab, para cargar el modulo ejecutamos:

.. code-block:: shell

    $ module load matlab-R2014b 

¿Como ejecuto MATLAB?    
^^^^^^^^^^^^^^^^^^^^^

.. warning::

    Hay que asegurarnos que ejecutar MATLAB en un nodo de computo y no en los nodos maestros ``chaman`` y ``kanik2``. 
    Es posible asi utilizar todos los cores de un nodo sin ninguna configuración especial. (Aunque es posible definir desde qsub cuantos cores pretendemos utilizar).

Ejecutar MATLAB interactivamente:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 

Para ejecutar Matlab interactivamente, necesitamos crear una sesión interactiva en un nodo de computo. 
Para hacer esto en chaman, solicitamos la sesion con 1 nodo de computo, con el siguiente comando:

.. code-block:: shell

    $ qsub -X -I -l nodes=1,walltime=24:00 -q default -d $PWD
      qsub: waiting for job 12788 to start
      qsub: job 12788 ready

    $ _

Una vez que la sesión interactiva comienze, podemos cargar los modulos apropiados y empezar Matlab como se describe a continuación:

.. code-block:: shell

    $ module load matlab-R2014b   
    $ matlab -nodisplay -nosplash -r "miScript ; exit" < /dev/null > matrun.out 2> matrun.err

Este comando ejecutara los contenidos de miScript.m, enviando la salida de consola al archivo ``matrun.out`` y los mensajes de error a ``matrun.err``.

También es posible tener una sesion interactiva de matlab, solo limitados en cuanto a la visualización de graficos.

.. code-block:: shell

    $ module load matlab-R2014b
    $ matlab -nodisplay -nosplash 
                       < M A T L A B (R) >
              Copyright 1984-2014 The MathWorks, Inc.
              R2014b (8.4.0.150421) 64-bit (glnxa64)
                        September 15, 2014


      To get started, type one of these: helpwin, helpdesk, or demo.
      For product information, visit www.mathworks.com.
      >>     


Ejecución de MATLAB por lotes (batch mode) 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Suponiendo que tenemos ya nuestro script de matlab que vamos a ejecutar, como ejemplo el siguiente script ``griddedInterpol2D.m`` 

.. code-block:: matlab

    %% griddedInterpolant in Two Dimensions
    % This example shows how to create and plot a 2-D interpolant using
    % |griddedInterpolant|.
    % Copyright 2015 The MathWorks, Inc.

    %%
    % In two dimensions and higher, you can specify the sample coordinates as
    % an |ndgrid|, a compact grid, or a default grid. In this example, we'll
    % supply an |ndgrid|.

    %%
    % Create a coarse grid and sample values.
    [X,Y] = ndgrid(-1:.3:1,-2:.3:2);
    V = 0.75*Y.^3 - 3*Y - 2*X.^2;

    %%
    % Construct the |griddedInterpolant|.
    F = griddedInterpolant(X,Y,V,'spline');

    %%
    % Interpolate over finer intervals with 0.1 spacing.
    [Xq,Yq] = ndgrid(-1:.1:1,-2:.1:2);
    Vq = F(Xq,Yq);

    %%
    % Plot the result.
    figure('Visible','off')
    surf(X,Y,V);
    view(65,60)
    title('Sample Data');
    saveas(gcf,'sampleData.png','png')

    figure('Visible','off')
    surf(Xq,Yq,Vq);
    view(65,60)
    title('Refined with Spline');
    saveas(gcf,'refinedData.png','png')


Ahora necesitamos crear un script de bash que contenga las instrucciones para el manejador de colas PBS y para el shell, como el cambio de directorios, cargado de modulos, etc.   Ejemplo : ``submitmjob.sh``

.. code-block:: bash

    #!/bin/bash
    #PBS -N matlabjob
    #PBS -q default
    #PBS -l nodes=1
    #PBS -j oe

    module load matlab-R2014b

    set -x

    # La variable de entorno $PBS_O_WORKDIR es el directorio desde el que se ejecuta "qsub submitmjob.sh"
    cd $PBS_O_WORKDIR

    echo Inicio: `date` > tiempo.log
    start=`date +%s`

    matlab -nosplash -nodisplay -nodesktop -r "griddedInterpol2D ; exit;"

    echo Final : `date` >> tiempo.log
    end=`date +%s`
    echo Tiempo ejecución : $((end-start)) seg >> tiempo.log


Este script lo que hace es primero reservar 1 nodo de computo en la cola default y dirigir la salida de consola y de error al mismo archivo (Linea ``#PBS -j oe``). 

Carga el modulo de matlab, nos dirigue a nuestro directorio de trabajo y ahi genera un archivo tiempo.log que contendra la fecha de inicio del script, fecha final y el tiempo que consumio.

La ejecución de nuestro script de matlab se da en la linea ``matlab -nosplash -nodisplay -nodesktop -r "griddedInterpol2D ; exit;"``.

Para enviar la tarea al manejador de colas ejecutamos: 

.. code-block:: shell

    $ qsub submitmjob.sh 

Al finalizar esta tarea tendremos 2 archivos png (sampleData.png, refinedData.png) que son los resultados graficos del script de matlab, un archivo tiempo.log y un archivo submitmjob.o#### donde ##### sera el Job ID que nos arrojo el manejador de colas.

.. note::
    
    Podemos revisar el estado de nuestra tarea al ejecutar ``qstat MiJobID -n`` e incluso en otra sesión entrar por ssh al nodo que el manejador de colas nos reservo para monitorear que nuestra tarea este produciendo los resultados que esperamos.


Anaconda Python
---------------

¿Que versión de python esta disponible?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

En chaman se encuentran instaladas las versiones 2.7 y 3.0 de python, con la distribución anaconda.

Para cargar python 2.7 usar:

.. code-block:: shell

    $ module load anaconda-python-2.7

Para python 3.0 usar:

.. code-block:: shell

    $ module load anaconda-python-3

TODO
