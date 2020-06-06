Información del cluster lamb
=======================================

Caracteristicas
---------------

* 1 nodo de entrada y de envios de trabajo: lamb
* 29 nodos de procesamiento

    * 24 nodos Intel con 10 nucleos
    * 5 nodos AMD con 16 nucleos

* 4 nodos con coprocesador Xeon Phi
* Total 720 cores
* Almacenamiento de 61 TB (lustre + infiniband)
* Sistema Linux Centos 6.7

.. image:: lamb-schematic.png
    :scale: 60 %
    :alt: Esquema LAMB
    :align: center

Manejador de colas
------------------
La implementación del sistema de colas PBS permite el uso optimo del cluster chaman, cualquier ejecucion de un trabajo serial o paralelo debe hacerse a traves del sistema PBS.

Colas definidas en chaman
^^^^^^^^^^^^^^^^^^^^^^^^^

+---------+----------------------------------------+-------------------------------------+-------------------------------------+
| memsup  | 30 nodos (840 cores) c61-c89           | Por nodo dos procesadores 14 cores. | 30 procesadores * X GFLOPS (c/u).   |
|         |                                        | intel E5-2680 v4 @ 2.40Ghz y.       | = X TFLOPS.                         |
|         |                                        | 256 GB de RAM.                      |                                     |
+---------+----------------------------------------+-------------------------------------+-------------------------------------+
| mem256  | 10 nodos (240 cores) c30-c34 y c40-c44 | Por nodo dos procesadores 12 cores. | 20 procesadores * 365 GFLOPS (c/u). |
|         |                                        | intel E5-2670 v3 @ 2.30Ghz y.       | = 7.3 TFLOPS.                       |
|         |                                        | 256 GB de RAM.                      |                                     |
+---------+----------------------------------------+-------------------------------------+-------------------------------------+
| mem128  | 20 nodos (480 cores) c35-c39 y c45-c59 | Por nodo dos procesadores 12 cores. | 40 procesadores * 365 GFLOPS (c/u). |
|         |                                        | intel E5-2670 v3 @ 2.30 GHz y.      | = 14.6 TFLOPS.                      |
|         |                                        | 128 GB de RAM.                      |                                     |
+---------+----------------------------------------+-------------------------------------+-------------------------------------+
| mem96   | 20 nodos (400 cores) c0-c19            | Por nodo dos procesadores 10 cores. | 40 procesadores * 200 GFLOPS (c/u). |
|         |                                        | intel E5-2670 v2 @ 2.50 GHz y.      | = 8 TFLOPS.                         |
|         |                                        | 96 GB de RAM.                       |                                     |
+---------+----------------------------------------+-------------------------------------+-------------------------------------+
| mem64   | 10 nodos (200 cores) c20-c29           | Por nodo dos procesadores 10 cores. | 20 procesadores * 352 GFLOPS (c/u). |
|         |                                        | intel E5-2660 v3 @ 2.60 GHz y.      | = 7 TFLOPS                          |
|         |                                        | 64 GB de RAM                        |                                     |
+---------+----------------------------------------+-------------------------------------+-------------------------------------+
| default |                                        |                                     |                                     |
| (memsup+|                                        |                                     |                                     |
| mem256+ |                                        |                                     |                                     |
| mem128+ |                                        |                                     |                                     |
| mem96+  |                                        |                                     |                                     |
| mem64)  | 90 nodos (2164 cores)                  |                                     |   Total X TFLOPS                    |
+---------+----------------------------------------+-------------------------------------+-------------------------------------+
| phi     | 1 nodo (24 cores) + 2 tarjetas.        | 2 tarjetas PHI de 57 cores cada una | ..                                  |
|         | Xeon Phi                               |                                     |                                     |
+---------+----------------------------------------+-------------------------------------+-------------------------------------+

.. note::
    Sumando los nucleos de los 33 nodos de procesamiento obtenemos 720 cores a nuestra disposición.
