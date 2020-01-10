# Índice
En un create table se puede expecificar en que espacio va a estar esa tabla, que tamaño va a tener o puede llegar a tener, etc.
## 1. Creación y uso de índices
Los índicen ordenan las tablas en la base de datos para que su búsqueda sea más fácil. 
~~~
CREATE INDEX nombreindice
ON nombretabla(listacolumnas)[ASC|DESC]
[TABLESPACE nombre_ts];
~~~

Por defecto ORACLE crea un índice con la clave primaria (y las claves candidatas) y la posición, ordenado por orden alfabético.

Muy útil en: datos que se concultan mucho. Pero si hay más de la cuenta va más lento (los insert son más lentos). 

> No es recomendable crear un índice cuando la base de datos tiene mucha carga.


## 2. Claúsulas de almacenamiento

## 3. Gestión de espacios de tablas

## 4. Gestión de enlaces de bases de datos

## 5. Gestión de clusters

## 6. Creación y uso de secuencias
> Creación de campos autonuméricos.
