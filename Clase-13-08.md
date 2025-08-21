# PostgreSQL

## PostgreSQL con Docker
````bash
docker run -d --name postgres_container -e POSTGRES_USERNAME=admin -e POSTGRES_PASSWORD:1234 -e POSTGRES_DB=campus -p 5433:5432 -v pgdata:/var/lib/postgresql/data --restart=unless-stopped postgres:15
````

## Conectar al contenedor Docker
````bash
docker exec -it postgres_container bash 
````

## Conectar con PostgreSQL bajo Consola
````bash
psql  --host=localhost --username=admin -d campus --password

psql -h localhost -U admin -d campus -W
````

## Para usuario por defecto
````bash
psql ... --username=postgres ...
````

## Comandos PSQL
- \l : Lista las bases de datos
- \c db_name : Cambiar a una base de datos existente
- \d : Describe las tablas de la base de datos
- \d table_name : Describe la tabla
- \dt : Listado de las tablas de la base de datos
- \ds : Secuencias que se crean con el tipo de datos
- serial
- \di : Listar los indices
- \dp \z : Listado de privilegios de las tablas
- \dn : Lista los schemas de la base de datos
- \dv : Listar las vistas del schema de la base de datos

### Funciones
¡Claro\! Con gusto, aquí tienes una versión más completa de la tabla con otras funciones de PostgreSQL muy útiles, incluyendo funciones de ventana, de agregación, de cadena y condicionales.

| Función | Tipo de Función | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- | :--- |
| **`LAG()`** | De ventana | Accede a una fila que precede a la fila actual en la misma partición. Útil para comparar el valor de la fila actual con el de la fila anterior. | `SELECT product, revenue, LAG(revenue, 1) OVER (ORDER BY date) FROM sales;` |
| **`LEAD()`** | De ventana | Accede a una fila que sigue a la fila actual en la misma partición. Es lo opuesto a `LAG()`, útil para comparar el valor de la fila actual con el de la siguiente. | `SELECT product, revenue, LEAD(revenue, 1) OVER (ORDER BY date) FROM sales;` |
| **`NTILE()`** | De ventana | Divide las filas de una partición en un número específico de grupos. Es ideal para análisis de percentiles o cuartiles. | `SELECT name, score, NTILE(4) OVER (ORDER BY score DESC) FROM students;` |
| **`FIRST_VALUE()`** | De ventana | Devuelve el valor de una expresión de la primera fila en la partición de la ventana. | `SELECT product, price, FIRST_VALUE(price) OVER (PARTITION BY category ORDER BY price) FROM products;` |
| **`LAST_VALUE()`** | De ventana | Devuelve el valor de una expresión de la última fila en la partición de la ventana. | `SELECT product, price, LAST_VALUE(price) OVER (PARTITION BY category ORDER BY price) FROM products;` |
| **`COUNT()`** | De agregación | Cuenta el número de filas en un conjunto o el número de valores no nulos en una columna específica. | `SELECT COUNT(*) FROM employees;` \<br\> `SELECT COUNT(DISTINCT department) FROM employees;` |
| **`SUM()`** | De agregación | Calcula la suma de todos los valores en la columna o expresión dada. | `SELECT SUM(amount) FROM transactions;` |
| **`AVG()`** | De agregación | Calcula el valor promedio de los valores en la columna o expresión dada. | `SELECT AVG(price) FROM products;` |
| **`MAX()`** | De agregación | Encuentra el valor máximo en la columna o expresión dada. | `SELECT MAX(salary) FROM employees;` |
| **`MIN()`** | De agregación | Encuentra el valor mínimo en la columna o expresión dada. | `SELECT MIN(birth_date) FROM customers;` |
| **`CASE WHEN`** | Condicional | Permite definir una lógica `IF-THEN-ELSE`. Evalúa condiciones y devuelve un valor cuando se cumple la primera condición. | `SELECT product, CASE WHEN price > 100 THEN 'Caro' ELSE 'Barato' END FROM products;` |
| **`COALESCE()`** | Condicional/General | Retorna el primer valor no nulo de una lista de expresiones. Útil para manejar valores nulos. | `SELECT COALESCE(phone, email, 'No disponible') FROM contacts;` |
| **`CONCAT()`** | De cadena | Concatena dos o más cadenas de texto. | `SELECT CONCAT(first_name, ' ', last_name) FROM users;` |
| **`LOWER()`** | De cadena | Convierte todos los caracteres de una cadena a minúsculas. | `SELECT LOWER(product_name) FROM products;` |
| **`UPPER()`** | De cadena | Convierte todos los caracteres de una cadena a mayúsculas. | `SELECT UPPER(product_name) FROM products;` |
| **`SUBSTRING()`** | De cadena | Extrae una subcadena de una cadena de texto, a partir de una posición y longitud dadas. | `SELECT SUBSTRING(email FROM 1 FOR POSITION('@' IN email) - 1) FROM users;` |
| **`NOW()`** | De fecha/hora | Devuelve la fecha y hora actuales con la zona horaria. | `SELECT NOW();` |
| **`EXTRACT()`** | De fecha/hora | Extrae un subcampo (como año, mes, día) de un valor de fecha/hora. | `SELECT EXTRACT(YEAR FROM birth_date) FROM customers;` |

### Funciones para fechas y horas

| Función | Tipo de Función | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- | :--- |
| **`NOW()`** | De fecha/hora | Devuelve la fecha y hora actuales con la zona horaria del servidor. | `SELECT NOW();` |
| **`CURRENT_DATE`** | De fecha/hora | Devuelve solo la fecha actual. | `SELECT CURRENT_DATE;` |
| **`CURRENT_TIME`** | De fecha/hora | Devuelve solo la hora actual con la zona horaria. | `SELECT CURRENT_TIME;` |
| **`AGE()`** | De fecha/hora | Calcula el "intervalo" o la diferencia entre dos marcas de tiempo (timestamps). | `SELECT AGE('2025-08-20', '1990-05-15');` |
| **`EXTRACT()`** | De fecha/hora | Extrae un subcampo específico de un valor de fecha/hora, como el año, mes, día, hora, minuto o segundo. | `SELECT EXTRACT(YEAR FROM birth_date) FROM users;` |
| **`DATE_PART()`** | De fecha/hora | Es una alternativa a `EXTRACT()`, con la sintaxis `DATE_PART(campo, origen)`. | `SELECT DATE_PART('month', order_date) FROM orders;` |
| **`TO_DATE()`** | De formato | Convierte una cadena de texto a un valor de fecha. Requiere un formato de plantilla para la conversión. | `SELECT TO_DATE('20/08/2025', 'DD/MM/YYYY');` |
| **`TO_CHAR()`** | De formato | Formatea un valor de fecha/hora o numérico a una cadena de texto, útil para presentaciones o informes. | `SELECT TO_CHAR(NOW(), 'YYYY-MM-DD HH24:MI:SS');` |
| **`DATE_TRUNC()`** | De redondeo | Trunca una marca de tiempo a la precisión especificada (por ejemplo, al inicio del año, mes, o día). | `SELECT DATE_TRUNC('month', '2025-08-20 10:30:00');` |
| **`JUSTIFY_DAYS()`** | De intervalo | Convierte un intervalo que contiene meses y días en un formato más comprensible, asumiendo 30 días por mes. | `SELECT JUSTIFY_DAYS(INTERVAL '40 days');` |

-----

### Operadores de fecha y hora

Además de las funciones, PostgreSQL permite operaciones aritméticas directas con fechas y horas.

| Operador | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| **`+`** | Suma un intervalo a una fecha/hora. | `SELECT NOW() + INTERVAL '1 hour';` \<br\> `SELECT CURRENT_DATE + 7;` |
| **`-`** | Resta un intervalo de una fecha/hora o calcula la diferencia entre dos. | `SELECT NOW() - INTERVAL '30 minutes';` \<br\> `SELECT '2025-08-20' - '2025-08-15';` |
| **`*`** | Multiplica un intervalo por un número. | `SELECT INTERVAL '1 day' * 3;` |
| **`/`** | Divide un intervalo por un número. | `SELECT INTERVAL '10 hours' / 2;` |

### Enumeradores
````sql
CREATE TYPE sexo AS ENUM('Masculino', 'Femenino', 'Otro');

CREATE TABLE camper(
    name varchar(100) NOT NULL,
    sexo_camper sexo NOT NULL
);
````

````sql
CREATE TABLE ejemplo(
    id serial PRIMARY KEY,
    nombre varchar(100) NOT NULL,
    descripción text NULL,
    precio numeric(10, 2) NOT NULL,
    en_stock boolean NOT NULL,
    fecha_creacion date NOT NULL,
    hora_creacion time NOT NULL,
    fecha_hora timestamp NOT NULL,
    fecha_hora_zona timestamp with time zone,
    duracion interval,
    direccion_io inet,
    direccion_mac macaddr,
    punto_geometrico point,
    datos_json json,
    datos_jsonb jsonb,
    identificador_unico uuid,
    cantidad_monetario money,
    rangos int4range,
    colores_preferidos varchar(20)[]
);
````

### Insert para 'ejemplo'

````sql
INSERT INTO ejemplo(
    id,
    nombre,
    descripción,
    precio,
    en_stock,
    fecha_creacion,
    hora_creacion,
    fecha_hora,
    fecha_hora_zona,
    duracion,
    direccion_io,
    direccion_mac,
    punto_geometrico,
    datos_json,
    datos_jsonb,
    identificador_unico,
    cantidad_monetario,
    rangos,
    colores_preferidos)
VALUES(
    'Ejemplo A', 'Lorem ipsum...', 9990.99, true, '2025-07-10', '20:30:10', '2025-07-10 20:30:10', '2025-07-10 20:30:10-05', '1 day', '192.168.0.1', '08:00:27:00:00:00', '(10, 20)', '{"key":"value"}', '{"key":"value"}', 'e4e6fbc5-3dc4-4f79-8853-aa6c56cc798d', 100.00', '[10, 20)', ARRAY['rojo', 'verde', 'azul', 'otro']
);
````

## Definicion de Constraints (Restrcciones)
### Table ejemplo
````sql
CREATE TABLE empleados(
    id serial,
    nombre varchar(100) NOT NULL,
    edad integer NOT NULL,
    salario numeric(10, 2) NOT NULL,
    fecha_contrato date,
    vigente boolean DEFAULT true
);

CREATE TABLE departamentos(
    id serial,
    nombre varchar(100) NOT NULL,
    vigente boolean DEFAULT true,
    PRIMARY KEY(id)
);

ALTER TABLE empleados ADD COLUMN departamento_id integer NOT NULL;
````

## Constraints a Tablas Existentes
### Primary Key
````sql
ALTER TABLE empleados ADD PRIMARY KEY(id);
````

### Foreign Key
````sql
ALTER TABLE empleados ADD CONSTRAINT fk_departamento FOREIGN KEY(departamento_id) REFERENCES departamentos(id);
````

### Unique
````sql
ALTER TABLE empleados ADD CONSTRAINT unique_nombre UNIQUE(nombre);
````

### Check
````sql
ALTER TABLE empleados ADD CONSTRAINT check_edad CHECK(edad >= 18);
````

### Deafult
````sql
ALTER TABLE empleados ALTER COLUMN salario SET DEFAULT 400.00;
````

### Not Null
````sql
ALTER TABLE empleados ALTER COLUMN salario SET NOT NULL;
````

## Taller de Constraints
> Definir los Constraints de Primary Key, Foreign Key, Not Null, Default
````sql
CREATE TABLE country(
    id serial,
    name varchar(50)
);

CREATE TABLE region(
    id serial,
    name varchar(50),
    idcountry integer
);

CREATE TABLE city(
    id serial,
    name varchar(50),
    idregion integer
);
````
### Solucion
````sql
ALTER TABLE country ADD PRIMARY KEY(id);
ALTER TABLE region ADD PRIMARY KEY(id);
ALTER TABLE city ADD PRIMARY KEY(id);

ALTER TABLE region ADD CONSTRAINT fk_country FOREIGN KEY(idcountry) REFERENCES country(id);
ALTER TABLE city ADD CONSTRAINT fk_region FOREIGN KEY(idregion) REFERENCES region(id);

ALTER TABLE country ALTER COLUMN name SET NOT NULL;
ALTER TABLE region ALTER COLUMN name SET NOT NULL;
ALTER TABLE city ALTER COLUMN name SET NOT NULL;
````

## Eliminar Defaults
````sql
ALTER TABLE country ALTER COLUMN name DROP DEFAULT;
ALTER TABLE region ALTER COLUMN name DROP DEFAULT;
ALTER TABLE city ALTER COLUMN name DROP DEFAULT;
````