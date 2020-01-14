# Índice
En un create table se puede expecificar en que espacio va a estar esa tabla, que tamaño va a tener o puede llegar a tener, etc.
### 1. Creación y uso de índices
Los índicen ordenan las tablas en la base de datos para que su búsqueda sea más fácil. 
~~~
CREATE INDEX nombreindice
ON nombretabla(listacolumnas)[ASC|DESC]
[TABLESPACE nombre_ts];
~~~

Por defecto ORACLE crea un índice con la clave primaria (y las claves candidatas) y la posición, ordenado por orden alfabético.

Muy útil en: datos que se concultan mucho. Pero si hay más de la cuenta va más lento (los insert son más lentos). 

> No es recomendable crear un índice cuando la base de datos tiene mucha carga.

Borrar índices:
~~~
DROP INDEX nombreindice ON nombretabla(listacolumnas);
~~~

Para que el usuario pueda ver los índices propios: USER_INDEXES, USER_IND_COLUMNS.

#### Vistas de los índices en el diccionario de datos
**DBA_INDEXES**
**DBA_IND_COLUMNS**


### 2. Claúsulas de almacenamiento
~~~
CREATE TABLE nombretabla
    ( columnas y restricciones)
STORAGE
    ( claúsulas de almacenamiento)
TABLESPACE nombre_ts;
~~~

Oracle tiene una función por defecto, que se desactiva en la instalación, para automatizar la colocación de las tablas en disco. Esto no es muy fiable. 

Clausulas de almacenamiento:
- INITIAL (Primer trozo de disco para la tabla) (en K o M)
- NEXT (tamaño 2a extensión) (las tabla tiene extensiones)
- PCTINCREASE (porcentaje de incremento para la 3ª y siguientes)
- MINEXTENTS (extensiones reservadas en principio) 
- MAXEXTENTS (número total de extensiones como máximo)


### 3. Gestión de espacios de tablas
Un tablespace es una unidad lógica de almacenamiento de datos que se corresponde con uno o varios ficheros del sistema operativo. 

Al instalar ORACLE se crean varios tablespaces:
- SYSTEM: Se guarda el diccionario de datos y otros objetos del sistema. NO usar para guardar otras cosas.
- SYSAUX: Componentes opcionales de ORACLE. Se crea solo si se ha instalado ORACLE con determinadas opciones. Solo información de herramientas auxiliares.
- USERS: Objetos de los usuarios. Esto normalmente se cambia y se pone un tablespace por cada departamento. 
- TEMP: Para guardar objetos temporales (sorts, productos cartesianos, etc...) Para hacer operaciones complejas que necesiten mucha memoria y se coge de disco. Esto es algo muy crítico. Por defecto se crea un tablespace temp pero se deben crear más.
- UNDOTBS01: Segmento de ROLLBACK. 
~~~
CREATE [TEMPORARY|UNDO] TABLESPACE nombrets
[DATAFILE|TEMPFILE] ruta_absoluta_fichero [SIZE nº [K|M]][REUSE][AUTOEXTEND ON [MAXSIZE n M]] #autoextend crece automaticamente y puede dejar de crecer con maxsize
… (podemos añadir más ficheros separando con comas)...
[LOGGING|NOLOGGING] # para que los tablespace entren o no en el fichero de diario, que es un fichero donde se guardan los cambios que se han hecho desde la última copia de seguridad
[PERMANENT|TEMPORARY]
[DEFAULT STORAGE
(clausulas de almacenamiento)] # si en el create table no se indican las clausulas de almacenamiento, la tabla se crea con las por defecto de aquí
[OFFLINE];
~~~

> Si se crea un tablespace temporary hay que opner tempfile y si no se pone esta opción datafile.

OFFLINE es muy útil para restaurar las bases de datos:
~~~
ALTER TABLESPACE nombrets OFFLINE;
~~~

Para añadirle otro datafile:
~~~
ALTER TABLESPACE nombrets
ADD DATAFILE …
~~~

Para cambiar la ruta de un fichero:
~~~
ALTER TABLESPACE nombrets
RENAME DATAFILE nombre1 TO nombre2
~~~

Para borrarlo:
~~~
DROP TABLESPACE nombrets [INCLUDING CONTENTS][AND DATAFILES];
~~~

#### Bloques de datos, extensiones y segmentos
Bloques de datos se parece mucho a un bloque de datos de un sistema operativo. Se define con **DB_CLOCK_SIZE**.

Extensión, conjunto de bloques de datos contiguos reservados de una vez.

Un tablespace se divide en segmentos, uno para cada objeto existente en el rablespace.

TRUNCATE de una tabla, es como el DELETE de un dato, pero no solo lo borra sino que libera el espacio del tablespace.

#### Vistas en el diccionario de datos 
Las vistas más útiles para gestionar el espacio de almacenamiento son:
- DBA_TABLESPACES: Descripción de todos los tablespaces.
- DBA_TS_QUOTAS: Cuotas de usuario en cada tablespace
- DBA_DATA_FILES: Información de los ficheros de datos.
- DBA_FREE_SPACE: Extensiones disponibles en cada tablespace.
- DBA_EXTENTS: Extensiones de cada segmento.
- DBA_ROLLBACK_SEGS: Segmentos de rollback
- DBA_SEGMENTS: Segmentos de cada tablespace.
- DBA_TEMP_FILES: Ficheros para los tablespaces temporales.

### 5. Gestión de clusters
Un cluster de tablas es un objeto de la base de datos que almacena en un mismo área del disco varias tablas que contienen una o varias columnas en común.
~~~
CREATE CLUSTER nombrecluster (columna tipo)
[TABLESPACE nombrets]
[STORAGE (claúsulas de almacenamiento)];
~~~

Se debe crear un ínidice al cluster:
~~~
CREATE INDEX nombreidx ON CLUSTER nombrecluster;
~~~

Para que una tabla se cree dentro de un cluster:
~~~
CREATE TABLE nombretabla
( lista columnas y restricciones)
CLUSTER nombrecluster(columnadelcluster);
~~~

### 6. Creación y uso de secuencias
> Creación de campos autonuméricos.

La sintaxis es la siguiente:
~~~
CREATE SEQUENCE nombresec
[INCREMENT BY numero]
[START WITH numero]
[MAXVALUE numero]
[MINVALUE numero]
[CYCLE | NOCYCLE]
[ORDER | NOORDER];
~~~