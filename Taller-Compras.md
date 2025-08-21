# Taller Diseño de base de datos - Postgres

````sql
-- Opcional: crea y usa el esquema

DROP SCHEMA IF EXISTS miscompras CASCADE;
CREATE SCHEMA IF NOT EXISTS miscompras;
SET search_path TO miscompras;

CREATE TABLE clientes (
    id                 VARCHAR(20)  PRIMARY KEY,
    nombre             VARCHAR(40)  NOT NULL,
    apellidos          VARCHAR(100) NOT NULL,
    celular            NUMERIC(10,0),
    direccion          VARCHAR(80),
    correo_electronico VARCHAR(70)
);

CREATE TABLE miscompras.categorias (
    id_categoria  INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    descripcion   VARCHAR(45) NOT NULL,
    estado        SMALLINT     NOT NULL DEFAULT 1,
    CONSTRAINT categorias_estado_chk CHECK (estado IN (0,1))
);

CREATE TABLE miscompras.productos (
    id_producto    INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nombre         VARCHAR(45)   NOT NULL,
    id_categoria   INT           NOT NULL,
    codigo_barras  VARCHAR(150),
    precio_venta   NUMERIC(16,2) NOT NULL,
    cantidad_stock INT           NOT NULL DEFAULT 0,
    estado         SMALLINT      NOT NULL DEFAULT 1,
    CONSTRAINT productos_precio_chk   CHECK (precio_venta >= 0),
    CONSTRAINT productos_stock_chk    CHECK (cantidad_stock >= 0),
    CONSTRAINT productos_estado_chk   CHECK (estado IN (0,1)),
    CONSTRAINT productos_fk_categoria FOREIGN KEY (id_categoria)
        REFERENCES miscompras.categorias(id_categoria)
        ON UPDATE CASCADE
        ON DELETE RESTRICT
);

-- Unico por cÃ³digo de barras si se usa, permite varios NULL
CREATE UNIQUE INDEX IF NOT EXISTS ux_productos_codigo_barras
    ON miscompras.productos (codigo_barras)
    WHERE codigo_barras IS NOT NULL;

CREATE INDEX IF NOT EXISTS idx_productos_id_categoria
    ON miscompras.productos (id_categoria);


CREATE TABLE miscompras.compras (
    id_compra    INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    id_cliente   VARCHAR(20)  NOT NULL,
    fecha        TIMESTAMP    NOT NULL DEFAULT NOW(),
    medio_pago   CHAR(1)      NOT NULL,
    comentario   VARCHAR(300),
    estado       CHAR(1)      NOT NULL,
    CONSTRAINT compras_fk_cliente FOREIGN KEY (id_cliente)
        REFERENCES miscompras.clientes(id)
        ON UPDATE CASCADE
        ON DELETE RESTRICT
);

-- Indice para busquedas por cliente
CREATE INDEX IF NOT EXISTS idx_compras_id_cliente
    ON miscompras.compras (id_cliente);

CREATE TABLE miscompras.compras_productos (
    id_compra    INT           NOT NULL,
    id_producto  INT           NOT NULL,
    cantidad     INT           NOT NULL,
    total        NUMERIC(16,2) NOT NULL,
    estado       SMALLINT      NOT NULL DEFAULT 1,
    CONSTRAINT compras_productos_pk PRIMARY KEY (id_compra, id_producto),
    CONSTRAINT compras_productos_cantidad_chk CHECK (cantidad > 0),
    CONSTRAINT compras_productos_total_chk    CHECK (total >= 0),
    CONSTRAINT compras_productos_estado_chk   CHECK (estado IN (0,1)),
    CONSTRAINT compras_productos_fk_compra FOREIGN KEY (id_compra)
        REFERENCES miscompras.compras(id_compra)
        ON UPDATE CASCADE
        ON DELETE CASCADE,
    CONSTRAINT compras_productos_fk_producto FOREIGN KEY (id_producto)
        REFERENCES miscompras.productos(id_producto)
        ON UPDATE CASCADE
        ON DELETE RESTRICT
);

-- Indice adicional para acelerar consultas por producto
CREATE INDEX IF NOT EXISTS idx_cp_id_producto
    ON miscompras.compras_productos (id_producto);
````

## Datos
````sql
SET search_path TO miscompras;

INSERT INTO miscompras.clientes (id, nombre, apellidos, celular, direccion, correo_electronico) VALUES
('CC1001', 'Camila',   'Ramírez Gómez',     3004567890, 'Cra 12 #34-56, Bogotá',      'camila.ramirez@example.com'),
('CC1002', 'Andrés',   'Pardo Salinas',     3109876543, 'Cl 45 #67-12, Medellín',     'andres.pardo@example.com'),
('CC1003', 'Valeria',  'Gutiérrez Peña',    3012223344, 'Av 7 #120-15, Bogotá',       'valeria.gutierrez@example.com'),
('CC1004', 'Juan',     'Soto Cárdenas',     3155556677, 'Cl 9 #8-20, Cali',           'juan.soto@example.com'),
('CC1005', 'Luisa',    'Fernández Ortiz',   3028889911, 'Cra 50 #10-30, Bucaramanga', 'luisa.fernandez@example.com'),
('CC1006', 'Carlos',   'Muñoz Prieto',      3014567890, 'Cl 80 #20-10, Barranquilla', 'carlos.munoz@example.com'),
('CC1007', 'Diana',    'Rojas Castillo',    3126665544, 'Cra 15 #98-45, Bogotá',      'diana.rojas@example.com'),
('CC1008', 'Miguel',   'Vargas Rincón',     3201234567, 'Cl 33 #44-21, Cartagena',    'miguel.vargas@example.com');


INSERT INTO miscompras.categorias (descripcion, estado) VALUES
('Café',       1),
('Lácteos',    1),
('Panadería',  1),
('Aseo',       1),
('Snacks',     1),
('Bebidas',    1);

INSERT INTO miscompras.productos (nombre, id_categoria, codigo_barras, precio_venta, cantidad_stock, estado) VALUES
('Café de Colombia 500g',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Café'),
 '7701234567001', 28000.00,  250, 1),
('Café Orgánico Sierra Nevada 250g',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Café'),
 '7701234567002', 22000.00,  180, 1),
('Leche entera 1L',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Lácteos'),
 '7701234567003',  4200.00,  600, 1),
('Yogur natural 1L',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Lácteos'),
 '7701234567004',  6000.00,  400, 1),
('Pan campesino',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Panadería'),
 '7701234567005',  3500.00,  320, 1),
('Croissant mantequilla',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Panadería'),
 '7701234567006',  2500.00,  500, 1),
('Detergente líquido 1L',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Aseo'),
 '7701234567007', 12000.00,  260, 1),
('Jabón en barra 3un',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Aseo'),
 '7701234567008',  8000.00,  300, 1),
('Papas fritas 150g',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Snacks'),
 '7701234567009',  5500.00,  700, 1),
('Maní salado 200g',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Snacks'),
 '7701234567010',  7000.00,  420, 1),
('Gaseosa cola 1.5L',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Bebidas'),
 '7701234567011',  6500.00,  800, 1),
('Agua sin gas 600ml',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Bebidas'),
 '7701234567012',  2200.00, 1200, 1),
('Té verde botella 500ml',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Bebidas'),
 '7701234567013',  3800.00,  650, 1),
('Chocolate de mesa 250g',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Panadería'),
 '7701234567014',  9000.00,  240, 1),
('Mermelada fresa 300g',
 (SELECT id_categoria FROM miscompras.categorias WHERE descripcion='Panadería'),
 '7701234567015',  7500.00,  260, 1);

INSERT INTO miscompras.compras (id_cliente, fecha, medio_pago, comentario, estado) VALUES
('CC1001', '2025-07-02 10:15:23', 'T', 'Compra semanal',            'A'),
('CC1002', '2025-07-03 18:45:10', 'E', 'Para oficina',              'A'),
('CC1003', '2025-07-05 09:05:00', 'C', NULL,                        'A'),
('CC1001', '2025-07-10 14:22:40', 'T', 'Reabastecimiento café',     'A'),
('CC1004', '2025-07-12 11:11:11', 'E', 'Desayuno fin de semana',    'A'),
('CC1005', '2025-07-15 19:35:05', 'T', 'Compras del mes',           'A'),
('CC1006', '2025-07-18 08:55:30', 'C', 'Limpieza y bebidas',        'A'),
('CC1007', '2025-07-20 16:01:00', 'T', 'Merienda en familia',       'A'),
('CC1008', '2025-07-25 12:20:45', 'E', 'Reunión con amigos',        'A'),
('CC1002', '2025-08-01 17:05:12', 'T', 'Compras para semana',       'A'),
('CC1003', '2025-08-02 10:40:33', 'T', 'Bebidas y snacks',          'A'),
('CC1004', '2025-08-05 13:50:00', 'C', 'Dulces y panadería',        'A');

-- CC1001
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1001' AND fecha='2025-07-02 10:15:23'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Café de Colombia 500g'), 2, 56000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1001' AND fecha='2025-07-02 10:15:23'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Leche entera 1L'), 3, 12600.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1001' AND fecha='2025-07-02 10:15:23'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Pan campesino'), 2, 7000.00, 1);

-- CC1002
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1002' AND fecha='2025-07-03 18:45:10'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Gaseosa cola 1.5L'), 4, 26000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1002' AND fecha='2025-07-03 18:45:10'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Papas fritas 150g'), 5, 27500.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1002' AND fecha='2025-07-03 18:45:10'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Maní salado 200g'), 2, 14000.00, 1);

-- CC1003
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1003' AND fecha='2025-07-05 09:05:00'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Detergente líquido 1L'), 1, 12000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1003' AND fecha='2025-07-05 09:05:00'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Jabón en barra 3un'), 1,  8000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1003' AND fecha='2025-07-05 09:05:00'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Agua sin gas 600ml'), 6, 13200.00, 1);

-- CC1001
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1001' AND fecha='2025-07-10 14:22:40'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Café Orgánico Sierra Nevada 250g'), 1, 22000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1001' AND fecha='2025-07-10 14:22:40'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Mermelada fresa 300g'), 1,  7500.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1001' AND fecha='2025-07-10 14:22:40'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Pan campesino'), 1,  3500.00, 1);

-- CC1004
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1004' AND fecha='2025-07-12 11:11:11'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Yogur natural 1L'), 2, 12000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1004' AND fecha='2025-07-12 11:11:11'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Té verde botella 500ml'), 3, 11400.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1004' AND fecha='2025-07-12 11:11:11'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Chocolate de mesa 250g'), 1,  9000.00, 1);

-- CC1005
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1005' AND fecha='2025-07-15 19:35:05'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Café de Colombia 500g'), 1, 28000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1005' AND fecha='2025-07-15 19:35:05'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Leche entera 1L'), 4, 16800.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1005' AND fecha='2025-07-15 19:35:05'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Gaseosa cola 1.5L'), 2, 13000.00, 1);

-- CC1006
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1006' AND fecha='2025-07-18 08:55:30'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Detergente líquido 1L'), 2, 24000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1006' AND fecha='2025-07-18 08:55:30'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Jabón en barra 3un'), 2, 16000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1006' AND fecha='2025-07-18 08:55:30'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Agua sin gas 600ml'), 12, 26400.00, 1);

-- CC1007
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1007' AND fecha='2025-07-20 16:01:00'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Croissant mantequilla'), 6, 15000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1007' AND fecha='2025-07-20 16:01:00'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Café Orgánico Sierra Nevada 250g'), 2, 44000.00, 1);

-- CC1008
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1008' AND fecha='2025-07-25 12:20:45'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Papas fritas 150g'), 10, 55000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1008' AND fecha='2025-07-25 12:20:45'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Gaseosa cola 1.5L'), 5, 32500.00, 1);

-- CC1002
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1002' AND fecha='2025-08-01 17:05:12'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Leche entera 1L'), 8, 33600.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1002' AND fecha='2025-08-01 17:05:12'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Yogur natural 1L'), 4, 24000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1002' AND fecha='2025-08-01 17:05:12'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Pan campesino'), 4, 14000.00, 1);

-- CC1003
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1003' AND fecha='2025-08-02 10:40:33'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Té verde botella 500ml'), 5, 19000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1003' AND fecha='2025-08-02 10:40:33'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Agua sin gas 600ml'), 10, 22000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1003' AND fecha='2025-08-02 10:40:33'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Maní salado 200g'), 3, 21000.00, 1);

-- CC1004
INSERT INTO miscompras.compras_productos (id_compra, id_producto, cantidad, total, estado) VALUES
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1004' AND fecha='2025-08-05 13:50:00'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Chocolate de mesa 250g'), 2, 18000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1004' AND fecha='2025-08-05 13:50:00'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Mermelada fresa 300g'), 2, 15000.00, 1),
((SELECT id_compra FROM miscompras.compras WHERE id_cliente='CC1004' AND fecha='2025-08-05 13:50:00'),
 (SELECT id_producto FROM miscompras.productos WHERE nombre='Café de Colombia 500g'), 1, 28000.00, 1);
````

## Funciones y operadores

1. Top 10 productos mas vendidos y su ingreso total
    - SUN()
    - USING()

````sql
SELECT p.id_producto, p.nombre,
    SUM(cp.cantidad) AS unidades,
    SUM(cp.total) AS ingreso_total,
FROM miscompras.compras_productos cp
JOIN miscompras.productos p USING(id_producto)
GROUP BY p.id_producto, p.nombre,
ORDER BY unidades DESC
LIMIT 10;
````

2. Venta promedio por compra y mediana aproximada
    - PERCENTILE_CONT(...) WITHIN GROUP(ORDER BY ...)
    - ROUND()
    - USING
````sql
SELECT ROUND(AVG(t.total_compra), 2) AS promedio_compra,
PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY t.total_compra) AS mediana
FROM(
    SELECT c.id_compra, SUM(cp.total) as total_compra
    FROM miscompras.compras c
    JOIN miscompras.compras_productos cp USING(id_compra)
    GROUP BY c.id_compra
) t;
````

3. Compras por cliente y ranking
    - COUNT
    - SUM
    - RANK() OVER(ORDER BY ... ASC/DESC) ASC/DESC

````sql
SELECT cl.id, cl.nombre || ' ' || cl.apellidos AS cliente,
    COUNT(DISTINCT c.id_compra) AS compras,
    SUM(cp.total) AS gasto_total,
    RANK() OVER(ORDER BY SUM(cp.total) DESC) AS ranking_gasto
FROM miscompras.clientes cl
JOIN miscompras.compras c ON cl.id = c.id_cliente
JOIN miscompras.compras_productos cp USING(id_compra)
GROUP BY cl.id, cliente
ORDER BY ranking_gasto;
````

4. ticket por compra
    - COUNT
    - ROUND
    - SUM
    - WITH args AS

````sql
WITH t AS(
    SELECT c.id_compra, c.fecha::date as dia, SUM(cp.total) as total_compra
FROM miscompras.compras c
JOIN miscompras.compras_productos cp USING(id_compra)
GROUP BY c.id_compra, c.fecha::date
)
SELECT dia,
    COUNT(*) as numero_compras,
    ROUND(AVG(total_compra), 2) as promedio,
    SUM(total_compra) as total_compra
FROM t
GROUP BY dia
ORDER BY dia;
````

5. Busqueda "tipo e-commerce": productos activos, disponibles y que empiecen por 'Caf'
    - ILIKE

````sql
SELECT p.id_producto, p.nombre, p.precio_venta, p.cantidad_stock
FROM miscompras.productos p
WHERE p.estado = 1
AND p.cantidad_stock > 0
AND p.nombre ILIKE 'caf%';
````

11. Devuelve los 2 productos más vendidos por categoría
    - ROW_NUMBER()
    - OVER (PARTITION BY ... ORDER BY SUM(...) DESC)

````sql
WITH ranking_productos AS (
    SELECT
        p.id_producto,
        p.nombre,
        cat.descripcion AS categoria,
        SUM(cp.cantidad) AS unidades_vendidas,
        ROW_NUMBER() OVER (
            PARTITION BY p.id_categoria
            ORDER BY SUM(cp.cantidad) DESC
        ) as ranking
    FROM
        miscompras.productos p
    JOIN
        miscompras.compras_productos cp USING(id_producto)
    JOIN
        miscompras.categorias cat USING(id_categoria)
    GROUP BY
        p.id_producto, cat.descripcion
)
SELECT
    categoria,
    nombre,
    unidades_vendidas,
    ranking
FROM
    ranking_productos
WHERE
    ranking <= 2
ORDER BY
    categoria, ranking;
````

12. Cálculo de ventas mensuales
    - DATE_TRUNC('month', fecha)

````sql
SELECT
    DATE_TRUNC('month', c.fecha)::date AS mes,
    COUNT(DISTINCT c.id_compra) AS numero_compras,
    SUM(cp.total) AS ventas_totales
FROM
    miscompras.compras c
JOIN
    miscompras.compras_productos cp USING(id_compra)
GROUP BY
    mes
ORDER BY
    mes;
````

13. Productos que nunca se han vendido

````sql
SELECT
    id_producto,
    nombre,
    cantidad_stock
FROM
    miscompras.productos p
WHERE NOT EXISTS (
    SELECT 1 -- '1' es una optimización, puede ser cualquier cosa
    FROM miscompras.compras_productos cp
    WHERE cp.id_producto = p.id_producto
);
````

14. Clientes que compraron "café" y "pan" en la misma compra

````sql
SELECT DISTINCT
    cl.id AS id_cliente,
    cl.nombre || ' ' || cl.apellidos AS cliente,
    c.id_compra,
    c.fecha
FROM
    miscompras.clientes cl
JOIN miscompras.compras c USING (id_cliente)
JOIN miscompras.compras_productos cp USING (id_compra)
JOIN miscompras.productos p USING (id_producto)
WHERE
    p.nombre ILIKE '%café%'
    AND EXISTS (
        SELECT 1
        FROM
            miscompras.compras_productos cp2
        JOIN
            miscompras.productos p2 USING (id_producto)
        WHERE
            cp2.id_compra = c.id_compra AND p2.nombre ILIKE '%pan%'
    );
````

20. Vista materializada mensual

````sql
DROP VIEW IF EXISTS miscompras.reporte_mes;

CREATE MATERIALIZED VIEW IF NOT EXISTS miscompras.reporte_mes AS
SELECT DATE_TRUNC('month', c.fecha) as mes,
    SUM(cp.total) AS total_ventas
FROM miscompras.compras c
JOIN miscompras.compras_productos cp USING(id_compra)
GROUP BY mes;

SELECT * FROM miscompras.reporte_mes;

REFRESH MATERIALIZED VIEW miscompras.reporte_mes;
````

21. Realizar un "UPSERT" de un producto

````sql
-- Caso 1: El código de barras '7701234567001' ya existe. Esto actualizará el producto.
INSERT INTO miscompras.productos (nombre, id_categoria, codigo_barras, precio_venta, cantidad_stock)
VALUES ('Café de Colombia Edición Especial', 1, '7701234567001', 32000.00, 100)
ON CONFLICT (codigo_barras) DO UPDATE
SET
    nombre = EXCLUDED.nombre,
    precio_venta = EXCLUDED.precio_venta;

-- Caso 2: Código de barras nuevo. Esto insertará un nuevo producto.
INSERT INTO miscompras.productos (nombre, id_categoria, codigo_barras, precio_venta, cantidad_stock)
VALUES ('Pan de Chocolate', 3, '7709876543210', 4500.00, 150)
ON CONFLICT (codigo_barras) DO UPDATE
SET
    nombre = EXCLUDED.nombre,
    precio_venta = EXCLUDED.precio_venta;
````

23. Funcion: Total de una compra (retorna NUMERIC)
    - COALESCE
    - SUM

````sql
CREATE OR REPLACE FUNCTION miscompras.fn_total_compra(p_id_compra INT)
RETURNS NUMERIC LANGUAGE plpgsql AS $$
DECLARE v_total NUMERIC(16,2);
BEGIN
    SELECT COALESCE(SUM(total), 0)
    INTO v_total
    FROM miscompras.compras_productos
    WHERE id_compra = p_id_compra;

    RETURN v_total;
END
$$;
-- SELECT miscompras.fn_total_compra(1);
````

24. Trigger al insertar detalle de compra, descuenta stock
    - GREATEST

````sql
CREATE OR REPLACE FUNCTION miscompras.trg_descuenta_stock()
RETURNS TRIGGER LANGUAGE plpgsql AS
$$
BEGIN
    UPDATE miscompras.productos
    SET cantidad_stock = GREATEST(0, cantidad_stock - NEW.cantidad)
    WHERE id_producto = NEW.id_producto;
    RETURN NEW;
END
$$;

DROP TRIGGER IF EXISTS compras_productos_descuento_stock ON miscompras.compras_productos;

CREATE TRIGGER comras_productos_descuentos_stock
AFTER INSERT ON miscompras.compras_productos
FOR EACH ROW EXECUTE FUNCTION miscompras.trg_descuenta_stock();
````

26. Gasto por compra, gasto anterior y delta entre compras por cliente
    - LAG()

````sql
WITH gasto_diario_cliente AS (
    SELECT
        c.id_cliente,
        c.fecha::date AS dia,
        SUM(cp.total) AS gasto_diario
    FROM
        miscompras.compras c
    JOIN
        miscompras.compras_productos cp USING (id_compra)
    GROUP BY
        c.id_cliente, dia
)
SELECT
    id_cliente,
    dia,
    gasto_diario,
    LAG(gasto_diario, 1, 0) OVER (
        PARTITION BY id_cliente ORDER BY dia
    ) AS gasto_anterior,
    gasto_diario - LAG(gasto_diario, 1, 0) OVER (
        PARTITION BY id_cliente ORDER BY dia
    ) AS delta_vs_anterior
FROM
    gasto_diario_cliente
ORDER BY
    id_cliente, dia;
````

27. Funcion: Mostrar el valor total en formato moneda
    - TO_CHAR

````sql
SELECT nombre, miscompras.toMoney(precio_venta) as precio
FROM miscompras.productos;

CREATE OR REPLACE FUNCTION miscompras.toMoney(p_numeric NUMERIC)
RETURNS VARCHAR LANGUAGE plpgsql AS
$$
DECLARE valor VARCHAR(255);
BEGIN
    SELECT CONCAT('$ ', TO_CHAR(p_numeric, 'FM999G999G999D00'))
    INTO valor;
    RETURN valor;
END;
$$;
````

28. Procedure: Registrar nuevos clientes
    - INITCAP()

````sql
-- Actualizar nombres
CREATE OR REPLACE PROCEDURE miscompras.pc_registrar_nuevo_cliente(p_nombre VARCHAR(100), p_apellido VARCHAR(80), p_email VARCHAR(70))
LANGUAGE plpgsql AS
$$
BEGIN
    INSERT INTO miscompas.cliente(nombre, apellidos, celular, direccion, correo_electronico)
    VALUES(INITCAP(TRIM(p_nombre)), INITCAP(TRIM(p_apellido), p_celular, TRIM(p_direccion), TRIM(p_email)));
    RAISE NOTICE 'Se registro el usuario % exitosmente', p_email;
EXCEPTION
    WHEN OTHERS THEN
        RAISE EXCEPTION 'Error al registrar el nuevo usuario, el usuario ya
        se encuentra registrado';
END;
$$;
````