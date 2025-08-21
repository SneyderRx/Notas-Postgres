## Orígenes

PostgreSQL, abreviatura de "Postgres Structured Query Language," se originó en la Universidad de California, Berkeley, en la década de 1980. Michael Stonebraker, un destacado científico de la computación, lideró un equipo de investigación que desarrolló un sistema de gestión de bases de datos relacional (DBMS) llamado Ingres. Posteriormente, algunos miembros del equipo, incluido Stonebraker, se separaron y formaron el proyecto Postgres con el objetivo de avanzar en la tecnología de bases de datos.

## **Características principales**

PostgreSQL se diferencia de otras bases de datos relacionales en varios aspectos:

1. **Licencia de Código Abierto:** PostgreSQL se distribuye bajo una licencia de código abierto (PostgreSQL License) que permite su uso gratuito, modificación y redistribución. Esto lo hace accesible para empresas y proyectos de todos los tamaños.
2. **Arquitectura Extensible:** PostgreSQL permite a los desarrolladores crear sus propias funciones y tipos de datos personalizados, lo que lo convierte en una base de datos altamente extensible.
3. **Soporte para Transacciones Complejas:** PostgreSQL ofrece un sólido soporte para transacciones ACID (Atomicidad, Consistencia, Aislamiento y Durabilidad), lo que garantiza la integridad de los datos.
4. **Capacidad de Escalabilidad:** PostgreSQL puede escalarse horizontal y verticalmente para adaptarse a diferentes cargas de trabajo.
5. **Soporte de Búsqueda de Texto Completo:** Ofrece características avanzadas de búsqueda de texto completo, lo que lo hace adecuado para aplicaciones que requieren búsqueda de texto enriquecido.

## Diferencias con MySQL

MySQL se destaca por su velocidad, conectividad y seguridad, siendo ideal para acceder a bases de datos en entornos web. Por otro lado, PostgreSQL se destaca por su potencia y robustez, especialmente en escenarios con grandes volúmenes de datos y alta concurrencia de usuarios, aunque presenta desafíos en cuanto a velocidad en inserciones y actualizaciones. La elección entre ambos SGBD debe basarse en los requisitos específicos del sistema y el tipo de transacciones que se llevarán a cabo.

### **Criterio**

Velocidad

### **MySQL**

Más rápido que la mayoría de la competencia. Soporte multiprocesador.

### **PostgreSQL**

Ofrece una velocidad constante con bases de datos grandes. Aprovecha multiprocesos.

### **Criterio**

Fiabilidad

### **MySQL**

Fácil de usar y administrar, potente.

### **PostgreSQL**

Potente y confiable, con código fuente disponible.

### **Criterio**

Extensibilidad

### **MySQL**

Código fuente disponible sin costo.

### **PostgreSQL**

Código fuente disponible sin costo.

### **Criterio**

Manejo de transacciones

### **MySQL**

Agrupación de transacciones, limitaciones.

### **PostgreSQL**

Soporte para transacciones con consistencia.

### **Criterio**

Concurrencia

### **MySQL**

Alta concurrencia sin bloqueos.

### **PostgreSQL**

Alta concurrencia sin bloqueos y consistencia.

### **Criterio**

Tamaño en registros

### **MySQL**

Hasta 50 millones de registros.

### **PostgreSQL**

Ilimitado (depende del sistema de almacenamiento).

### **Criterio**

Respaldo de Bases de Datos

### **MySQL**

Comandos específicos, algunas fallas.

### **PostgreSQL**

Comandos específicos, confiable.

### **Criterio**

Permisos de usuarios

### **MySQL**

Seguro y cifrado de contrase

### **PostgreSQL**

Seguro y con sistema de contraseñas desde hace años.

### **Criterio**

Drivers para JDBC

### **MySQL**

Disponible para MySQL.

### **PostgreSQL**

Disponible para PostgreSQL.

### **Criterio**

Conexiones con .NET

### **MySQL**

[ADO.NET](http://ado.net/) para MySQL.

### **PostgreSQL**

[ADO.NET](http://ado.net/) para PostgreSQL.

### **Criterio**

Interfaz

### **MySQL**

Varias interfaces disponibles.

### **PostgreSQL**

C e interfaces embebidas.

### **Criterio**

Plataforma soportada

### **MySQL**

Multiplataforma.

### **PostgreSQL**

Multiplataforma.

## Arquitectura de PostgreSQL

La arquitectura en PostgreSQL esta compuesta por tres partes el Postmaster, Backend y el Sistema de Almacenamiento, estas trabajan en conjunto cuando se realiza una petición de tipo DML, DDL y DCL.

- **Proceso Postmaster**: Es el proceso principal que gestiona las conexiones de los clientes, los procesos de trabajo y la asignación de recursos.
- **Procesos Backend:** Cada conexión de cliente tiene un proceso backend dedicado en el servidor.
- **Sistema de Almacenamiento:** Usa un modelo de almacenamiento basado en discos con soporte para índices B-tree, hash, GiST, y otros.

![](https://khc-sistema-v2.s3.amazonaws.com/editor/173229894245045522daff8ae3/attachment-1.png.png)

Dado lo anterior, una base de datos PostgreSQL es un conjunto organizado de datos relacionados que se almacenan y gestionan en un servidor de bases de datos PostgreSQL, para crear, administrar y organizar conjuntos de datos. De ahora en adelante se usarán un conjunto de términos que permiten estructurar los datos.

1. **Clave Primaria (Primary Key):** Una clave primaria es una columna o conjunto de columnas en una tabla que se utiliza para identificar de manera única cada fila en la tabla. Garantiza la unicidad de los registros sirve para asegurar que cada fila tenga una identificación única y para facilitar la búsqueda y relacionar datos entre tablas.
2. **Consulta SQL (SQL Query):** Una consulta SQL es una instrucción que se utiliza para recuperar, insertar, actualizar o eliminar datos de una base de datos PostgreSQL. Permite interactuar con la base de datos y realizar operaciones como consultas, inserciones, actualizaciones y eliminaciones de datos.
3. **Transacciones:** Una transacción es una serie de operaciones SQL que se ejecutan de manera conjunta y se consideran una unidad lógica. Las transacciones garantizan la consistencia y la integridad de los datos. Sirve para agrupar operaciones y garantizar que se realicen de manera completa o que se reviertan si alguna operación falla.
4. **Esquema (Schema):** Un esquema es un contenedor lógico que agrupa tablas y otros objetos de la base de datos. Ayuda a organizar y separar objetos relacionados. Permite estructurar y organizar la base de datos en proyectos o aplicaciones más grandes.

La primera instrucción que se realiza una vez ya tiene instalado PostgreSQL en el equipo será crear una base de datos dentro del servidor definido por defecto.

## Indicadores de PostgresSQL

En las imágenes se observa algunos casos de uso de los indicadores mencionados.

![](https://khc-sistema-v2.s3.amazonaws.com/editor/1732309510369f6475b4e4f552/attachment-2.png.png)

**Indicador**

=#

**Significado**

Espera una nueva sentencia

**Indicador**

- #

**Significado**

La sentencia aún no se ha terminado con ";" o \g

**Indicador**

"#

**Significado**

Una cadena en comillas dobles no se ha cerrado

**Indicador**

'#

**Significado**

Una cadena en comillas simples no se ha cerrado

**Indicador**

(#

**Significado**

Un paréntesis no se ha cerrado

**Indicador**

\?

**Significado**

Muestra todas las variantes de comandos, q para terminar

## **Comandos principales para hacer consultas en las bases de datos y tablas**

**Comando**

\l

**Descripción**

Describe las tablas de la base de datos en uso

**Comando**

\ds

**Descripción**

Lista las secuencias, estas se crean automáticamente cuando se declaran columnas de tipo serial.

**Comando**

\di

**Descripción**

Lista los índices

**Comando**

\dv

**Descripción**

Lista las vistas

**Comando**

\dp \z

**Descripción**

Lista los privilegios sobre las tablas

**Comando**

\da

**Descripción**

Lista las funciones de agregados

**Comando**

\df

**Descripción**

Lista las funciones

**Comando**

\g archivo

**Descripción**

Ejecuta los comandos de archivo

**Comando**

\H

**Descripción**

Cambia el modo de salida HTML

**Comando**

\ ! Comando

**Descripción**

Ejecuta un comando del sistema operativo

## Tipos de datos permitidos

- **Tipos de Datos Primitivos**: Incluyen números enteros, de punto flotante, numéricos, serial, booleans, y más.
- **Tipos de Datos de Carácter:** Incluyen CHAR, VARCHAR, y TEXT.
- **Tipos de Datos de Fecha y Hora:** Soporta DATE, TIME, TIMESTAMP, y INTERVAL.
- **Tipos de Datos Geométricos y de Red:** Soporte para datos espaciales y de redes.

Para poder visualizar los tipos de datos inicialmente se debe crear una tabla en PostgreSQL de la siguiente forma:

### **Tipos de Datos Primitivos**

1. **Números enteros:**

Ejemplo que crea una tabla que tiene un dato de tipo número entero.

```
CREATE TABLE ejemplo_numeros (
    numero_entero INT
);
INSERT INTO ejemplo_numeros (numero_entero) VALUES (42);

```

**2.Números de punto flotante:**

Ejemplo que crea una tabla que tiene un dato de tipo punto flotante.

```
CREATE TABLE ejemplo_punto_flotante (
    numero_flotante FLOAT
);
INSERT INTO ejemplo_punto_flotante (numero_flotante) VALUES (3.14);

```

**3.Tipo numérico (decimal):**

Ejemplo que crea una tabla que tiene un dato de tipo numérico decimal.

```
CREATE TABLE ejemplo_numero_decimal (
    numero_decimal NUMERIC(10, 2)
);
INSERT INTO ejemplo_numero_decimal (numero_decimal) VALUES (1234.56);

```

**4.Serial (autoincremento):**

Ejemplo que crea una tabla que tiene un dato auto incremental.

```
CREATE TABLE ejemplo_autoincremento (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(50)
);
INSERT INTO ejemplo_autoincremento (nombre) VALUES ('Ejemplo 1');

```

**5.Booleanos:**

Ejemplo que crea una tabla que tiene un dato de tipo booleano.

```
CREATE TABLE ejemplo_booleano (
es_verdadero BOOLEAN
);
INSERT INTO ejemplo_booleano (es_verdadero) VALUES (TRUE);

```

### **Tipos de Datos de Carácter**

**1.CHAR (cadena de caracteres de longitud fija):**

Ejemplo que crea una tabla que tiene un dato de tipo cadena de caracteres de longitud fija.

```
CREATE TABLE ejemplo_char (
nombre CHAR(10)
);
INSERT INTO ejemplo_char (nombre) VALUES ('Juan');

```

**2.VARCHAR (cadena de caracteres de longitud variable):**

Ejemplo que crea una tabla que tiene un dato de tipo cadena de caracteres de longitud variable.

```
CREATE TABLE ejemplo_varchar (
    descripcion VARCHAR(255)
);
INSERT INTO ejemplo_varchar (descripcion) VALUES ('Esto es una descripción larga');

```

**3.TEXT (texto sin límite de longitud):**

Ejemplo que crea una tabla que tiene un dato de tipo texto sin límite de longitud

```
CREATE TABLE ejemplo_texto (
    contenido TEXT
);
INSERT INTO ejemplo_texto (contenido) VALUES ('Este es un texto largo sin restricciones de longitud.');

```

### **Tipos de Datos de Fecha y Hora**

**1.DATE (fecha):**

Ejemplo que crea una tabla que tiene un dato de tipo fecha.

```
CREATE TABLE ejemplo_fecha (
    fecha_nacimiento DATE
);
INSERT INTO ejemplo_fecha (fecha_nacimiento) VALUES ('1990-05-15');

```

**2.TIME(hora):**

Ejemplo que crea una tabla que tiene un dato de tipo TIME(hora)

```
CREATE TABLE ejemplo_hora (
    hora_registro TIME
);
INSERT INTO ejemplo_hora (hora_registro) VALUES ('14:30:00');

```

**3.TIMESTAMP (fecha y hora):**

Ejemplo que crea una tabla que tiene un dato de tipo fecha y hora

```
CREATE TABLE ejemplo_fecha_hora (
    fecha_hora_registro TIMESTAMP
);
INSERT INTO ejemplo_fecha_hora (fecha_hora_registro) VALUES ('2023-12-13 10:45:00');

```

**4.INTERVAL (intervalo de tiempo):**

Ejemplo que crea una tabla que tiene un dato de tipo intervalo de tiempo

```
CREATE TABLE ejemplo_intervalo (
duracion INTERVAL
);
INSERT INTO ejemplo_intervalo (duracion) VALUES ('1 day 3 hours');

```

### **Tipos de Datos Geométricos y de Red**

**1.Datos espaciales (ejemplo con PostGIS):**

Ejemplo que crea una tabla que tiene un dato de tipo dato especial

```
CREATE EXTENSION postgis;
CREATE TABLE ejemplo_geometria (
    ubicacion GEOMETRY(Point)
);
INSERT INTO ejemplo_geometria (ubicacion) VALUES (ST_GeomFromText('POINT(1 2)'));

```

**2.Datos de redes (ejemplo con PgRouting):**

Ejemplo que crea una tabla que tiene un dato de tipo para redes

```
CREATE EXTENSION pgrouting;
CREATE TABLE ejemplo_ruta (
    red geometry,
    source integer,
    target integer,
    cost double precision
);
INSERT INTO ejemplo_ruta (red, source, target, cost) VALUES (ST_GeomFromText('LINESTRING(0 0, 1 1)'), 1, 2, 1.41);
```
