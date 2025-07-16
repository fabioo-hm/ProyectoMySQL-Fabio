# Ejercicios

## 1. Consultas SQL Especializadas

1. Como analista, quiero listar todos los productos con su empresa asociada y el precio más bajo por ciudad.
```sql
SELECT 
    pro.name AS Producto,
    em.name AS Empresa,
    com.name AS Ciudad,
    cp.price AS PrecioMasBajo
FROM companyproducts cp
JOIN products pro ON cp.product_id = pro.id
JOIN companies em ON cp.company_id = em.id
JOIN citiesormunicipalities com ON em.city_id = com.code
WHERE (cp.product_id, em.city_id, cp.price) IN (
    SELECT 
        cp_2.product_id,
        em_2.city_id,
        MIN(cp_2.price)
    FROM companyproducts cp_2
    JOIN companies em_2 ON cp_2.company_id = em_2.id
    GROUP BY cp_2.product_id, em_2.city_id
);
```

2. Como administrador, deseo obtener el top 5 de clientes que más productos han calificado en los últimos 6 meses.
```sql
SELECT 
    cu.name AS cliente,
    .email AS Correo,
    COUNT(qp.product_id) AS ProdCalificados
FROM quality_products qp
JOIN customers cu ON qp.customer_id = cu.id
WHERE qp.daterating >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
GROUP BY cu.id, cu.name, cu.email
ORDER BY ProdCalificados DESC
LIMIT 5;
```

3. Como gerente de ventas, quiero ver la distribución de productos por categoría y unidad de medida.
```sql
SELECT 
    ca.description AS CategoríaProducto,
    um.description AS UnidadDeMedida,
    COUNT(DISTINCT cp.product_id) AS CantidadProductos
FROM companyproducts cp
JOIN products pro ON cp.product_id = pro.id
JOIN categories ca ON pro.category_id = ca.id
JOIN unitofmeasure um ON cp.unitmeasure_id = um.id
GROUP BY CategoríaProducto, UnidadDeMedida;
```

4. Como cliente, quiero saber qué productos tienen calificaciones superiores al promedio general.
```sql
SELECT DISTINCT 
    p.name AS producto
FROM quality_products qp
JOIN products p ON qp.product_id = p.id
WHERE qp.rating > (
    SELECT AVG(rating)
    FROM quality_products
);
```

5. Como auditor, quiero conocer todas las empresas que no han recibido ninguna calificación.
```sql
SELECT 
    c.name AS empresa
FROM companies c
WHERE c.id NOT IN (
    SELECT DISTINCT qp.company_id
    FROM quality_products qp
);
```

6. Como operador, deseo obtener los productos que han sido añadidos como favoritos por más de 10 clientes distintos.
```sql
SELECT 
    p.name AS producto
FROM products p
WHERE p.id IN (
    SELECT df.product_id
    FROM details_favorites df
    JOIN favorites f ON df.favorite_id = f.id
    GROUP BY df.product_id
    HAVING COUNT(DISTINCT f.customer_id) > 10
);
```

7. Como gerente regional, quiero obtener todas las empresas activas por ciudad y categoría.
```sql
SELECT 
    ci.name AS ciudad,
    cat.description AS categoria,
    c.name AS empresa
FROM companies c
JOIN citiesormunicipalities ci ON c.city_id = ci.code
JOIN categories cat ON c.category_id = cat.id
WHERE c.is_active = TRUE;
```

8. Como especialista en marketing, deseo obtener los 10 productos más calificados en cada ciudad.
```sql
SELECT 
    sub.ciudad,
    sub.producto,
    sub.promedio_calificacion
FROM (
    SELECT 
        ci.name AS ciudad,
        p.name AS producto,
        AVG(qp.rating) AS promedio_calificacion
    FROM quality_products qp
    JOIN products p ON qp.product_id = p.id
    JOIN companies c ON qp.company_id = c.id
    JOIN citiesormunicipalities ci ON c.city_id = ci.code
    GROUP BY ci.name, p.id, p.name
) AS sub
WHERE (
    SELECT COUNT(qp2.product_id)
    FROM (
        SELECT 
            qp2.product_id,
            AVG(qp2.rating) AS promedio
        FROM quality_products qp2
        JOIN companies c2 ON qp2.company_id = c2.id
        JOIN citiesormunicipalities ci2 ON c2.city_id = ci2.code
        WHERE ci2.name = sub.ciudad
        GROUP BY qp2.product_id
        HAVING AVG(qp2.rating) > sub.promedio_calificacion
    ) AS superiores
) < 10
ORDER BY sub.ciudad, sub.promedio_calificacion DESC;
```

9. Como técnico, quiero identificar productos sin unidad de medida asignada.
```sql
SELECT 
    p.name AS producto
FROM companyproducts cp
JOIN products p ON cp.product_id = p.id
WHERE cp.unitmeasure_id IS NULL;
```

10. Como gestor de beneficios, deseo ver los planes de membresía sin beneficios registrados.
```sql
SELECT m.id, m.name AS membresia
FROM memberships m
WHERE m.id NOT IN (
    SELECT DISTINCT mb.membership_id
    FROM membershipbenefits mb
);
```

11. Como supervisor, quiero obtener los productos de una categoría específica con su promedio de calificación.
```sql
SELECT
    p.name AS producto,
    c.description AS categoria,
    ROUND(AVG(qp.rating), 2) AS promedio_calificacion
FROM products p
JOIN categories c ON p.category_id = c.id
JOIN quality_products qp ON p.id = qp.product_id
WHERE c.id = 1
GROUP BY p.id, p.name, c.description;
```

12. Como asesor, deseo obtener los clientes que han comprado productos de más de una empresa.
```sql
SELECT
    cu.name AS cliente
FROM customers cu
WHERE cu.id IN (
    SELECT qp.customer_id
    FROM quality_products qp
    GROUP BY qp.customer_id
    HAVING COUNT(DISTINCT qp.company_id) > 1
);
```

13. Como director, quiero identificar las ciudades con más clientes activos.
```sql
SELECT 
    ci.name AS ciudad,
    COUNT(c.id) AS total_clientes_activos
FROM customers c
JOIN citiesormunicipalities ci ON c.city_id = ci.code
WHERE c.is_active = TRUE
GROUP BY ci.name
ORDER BY total_clientes_activos DESC;
```

14. Como analista de calidad, deseo obtener el ranking de productos por empresa basado en la media de quality_products.
```sql
SELECT 
    c.name AS empresa,
    p.name AS producto,
    ROUND(sub.promedio_rating, 2) AS promedio_calificacion
FROM (
    SELECT 
        qp.company_id,
        qp.product_id,
        AVG(qp.rating) AS promedio_rating
    FROM quality_products qp
    GROUP BY qp.company_id, qp.product_id
) AS sub
JOIN companies c ON sub.company_id = c.id
JOIN products p ON sub.product_id = p.id
ORDER BY sub.promedio_rating DESC;
```

15. Como administrador, quiero listar empresas que ofrecen más de cinco productos distintos.
```sql
SELECT 
    c.name AS empresa,
    COUNT(DISTINCT cp.product_id) AS total_productos
FROM companyproducts cp
JOIN companies c ON cp.company_id = c.id
GROUP BY c.id, c.name
HAVING COUNT(DISTINCT cp.product_id) > 5
ORDER BY total_productos DESC;
```

16. Como cliente, deseo visualizar los productos favoritos que aún no han sido calificados.
```sql
SELECT
    p.name AS producto
FROM details_favorites df
JOIN favorites f ON df.favorite_id = f.id
JOIN products p ON df.product_id = p.id
WHERE df.product_id NOT IN (
    SELECT DISTINCT qp.product_id
    FROM quality_products qp
);
```

17. Como desarrollador, deseo consultar los beneficios asignados a cada audiencia junto con su descripción.
```sql
SELECT
    a.description AS audiencia,
    b.description AS beneficio,
    b.detail AS detalle
FROM audiencebenefits ab
JOIN audiences a ON ab.audience_id = a.id
JOIN benefits b ON ab.benefit_id = b.id;
```

18. Como operador logístico, quiero saber en qué ciudades hay empresas sin productos asociados.
```sql
SELECT 
    ci.name AS ciudad,
    c.name AS empresa
FROM companies c
JOIN citiesormunicipalities ci ON c.city_id = ci.code
WHERE c.id NOT IN (
    SELECT DISTINCT cp.company_id
    FROM companyproducts cp
);
```

19. Como técnico, deseo obtener todas las empresas con productos duplicados por nombre.
```sql
SELECT 
    c.id AS empresa_id,
    c.name AS empresa,
    p.name AS nombre_producto,
    COUNT(p.id) AS cantidad_productos
FROM companyproducts cp
JOIN products p ON cp.product_id = p.id
JOIN companies c ON cp.company_id = c.id
GROUP BY c.id, c.name, p.name
HAVING COUNT(p.id) > 1;
```

20. Como analista, quiero una vista resumen de clientes, productos favoritos y promedio de calificación recibido.
```sql
SELECT 
    cu.name AS cliente,
    p.name AS producto,
    ROUND(AVG(qp.rating), 2) AS promedio_calificacion
FROM customers cu
JOIN favorites f ON cu.id = f.customer_id
JOIN details_favorites df ON f.id = df.favorite_id
JOIN products p ON df.product_id = p.id
LEFT JOIN quality_products qp ON p.id = qp.product_id
GROUP BY cu.id, cu.name, p.id, p.name
ORDER BY cu.name, p.name;
```

## 2. Subconsultas

1. Como gerente, quiero ver los productos cuyo precio esté por encima del promedio de su categoría.
```sql
SELECT 
    p.name AS producto,
    p.price AS precio,
    cat.description AS categoria
FROM products p
JOIN categories cat ON p.category_id = cat.id
WHERE 
    p.price > (
        SELECT AVG(p2.price)
        FROM products AS p2
        WHERE p2.category_id = p.category_id
    );
```

2. Como administrador, deseo listar las empresas que tienen más productos que la media de empresas.
```sql
SELECT 
    c.name AS empresa,
    COUNT(cp.product_id) AS total_productos
FROM companies c
JOIN companyproducts cp ON c.id = cp.company_id
GROUP BY c.id
HAVING 
    COUNT(cp.product_id) > (
        SELECT AVG(productos_por_empresa) 
        FROM (
            SELECT COUNT(product_id) AS productos_por_empresa
            FROM companyproducts
            GROUP BY company_id
        ) AS subquery
    );
```

3. Como cliente, quiero ver mis productos favoritos que han sido calificados por otros clientes.
```sql
SELECT 
    p.name AS producto,
    p.detail AS product_detail
FROM products p
WHERE 
    p.id IN (
        SELECT df.product_id
        FROM details_favorites df
        WHERE df.favorite_id IN (
            SELECT f.id
            FROM favorites f
            WHERE f.customer_id = 1
        )
    )
    AND EXISTS (
        SELECT 1
        FROM quality_products q
        WHERE q.product_id = p.id AND q.customer_id <> 2
    );
```

4. Como supervisor, deseo obtener los productos con el mayor número de veces añadidos como favoritos.
```sql
SELECT 
    p.name AS producto,
    (
        SELECT COUNT(product_id)
        FROM details_favorites df
        WHERE df.product_id = p.id
    ) AS veces_favorito
FROM products p
WHERE 
    p.id IN (
        SELECT DISTINCT df.product_id
        FROM details_favorites df
    )
ORDER BY veces_favorito DESC;
```
5. Como técnico, quiero listar los clientes cuyo correo no aparece en la tabla rates ni en quality_products.
```sql
SELECT 
    c.name AS cliente,
    c.email
FROM customers c
WHERE 
    c.id NOT IN (
        SELECT DISTINCT r.customer_id
        FROM rates r
    )
    AND c.id NOT IN (
        SELECT DISTINCT q.customer_id
        FROM quality_products q
    );
```

6. Como gestor de calidad, quiero obtener los productos con una calificación inferior al mínimo de su categoría.
```sql
SELECT 
    p.name AS producto,
    cat.description AS categoria,
    (
        SELECT AVG(qp.rating)
        FROM quality_products qp
        WHERE qp.product_id = p.id
    ) AS promedio_producto
FROM products p
JOIN categories cat ON p.category_id = cat.id
WHERE 
    (
        SELECT AVG(qp.rating)
        FROM quality_products qp
        WHERE qp.product_id = p.id
    ) < (
        SELECT MIN(promedio_categoria)
        FROM (
            SELECT AVG(qp2.rating) AS promedio_categoria
            FROM products p2
            JOIN quality_products qp2 ON qp2.product_id = p2.id
            WHERE p2.category_id = p.category_id
            GROUP BY p2.id
        ) AS subquery
    );
```

7. Como desarrollador, deseo listar las ciudades que no tienen clientes registrados.
```sql
SELECT c.name AS Ciudad
FROM citiesormunicipalities c
WHERE NOT EXISTS (
    SELECT cu.city_id
    FROM customers cu
    WHERE cu.city_id = c.code
);
```

8. Como administrador, quiero ver los productos que no han sido evaluados en ninguna encuesta.
```sql
SELECT 
    p.name AS producto,
    p.detail,
    p.price
FROM products p
WHERE 
    p.id NOT IN (
        SELECT DISTINCT qp.product_id
        FROM quality_products qp
    );
```

9. Como auditor, quiero listar los beneficios que no están asignados a ninguna audiencia.
```sql
SELECT 
    b.description AS beneficio,
    b.detail
FROM benefits b
WHERE 
    b.id NOT IN (
        SELECT DISTINCT ab.benefit_id
        FROM audiencebenefits ab
    );
```

10. Como cliente, deseo obtener mis productos favoritos que no están disponibles actualmente en ninguna empresa.
```sql
SELECT 
    p.name AS Producto_NO_disponible
FROM details_favorites df
JOIN favorites f ON df.favorite_id = f.id
JOIN products p ON df.product_id = p.id
WHERE f.customer_id = 1
  AND p.id NOT IN (
      SELECT cp.product_id
      FROM companyproducts cp
      WHERE cp.is_available = TRUE
  );
```

11. Como director, deseo consultar los productos vendidos en empresas cuya ciudad tenga menos de tres empresas registradas.
```sql
SELECT DISTINCT 
    p.name AS producto, 
    c.name AS empresa, 
    ci.name AS ciudad
FROM companyproducts cp
JOIN companies c ON cp.company_id = c.id
JOIN citiesormunicipalities ci ON c.city_id = ci.code
JOIN products p ON cp.product_id = p.id
WHERE c.city_id IN (
    SELECT c.city_id
    FROM companies c
    GROUP BY c.city_id
    HAVING COUNT(c.id) < 3
);
```

12. Como analista, quiero ver los productos con calidad superior al promedio de todos los productos.
```sql
SELECT
    p.name AS producto,
    qp.rating AS calificacion
FROM products p
JOIN quality_products qp ON qp.product_id = p.id
WHERE qp.rating > (
        SELECT AVG(qp2.rating)
        FROM quality_products qp2
    );
```

13. Como gestor, quiero ver empresas que sólo venden productos de una única categoría.
```sql
SELECT 
    c.name AS empresa
FROM companies c
WHERE c.id IN (
    SELECT cp.company_id
    FROM companyproducts cp
    JOIN products p ON cp.product_id = p.id
    GROUP BY cp.company_id
    HAVING COUNT(DISTINCT p.category_id) = 1
);
```

14. Como gerente comercial, quiero consultar los productos con el mayor precio entre todas las empresas.
```sql
SELECT 
    p.name AS producto, 
    cp.price, 
    c.name AS empresa
FROM companyproducts cp
JOIN products p ON cp.product_id = p.id
JOIN companies c ON cp.company_id = c.id
WHERE cp.price = (
    SELECT MAX(price)
    FROM companyproducts
);
```

15. Como cliente, quiero saber si algún producto de mis favoritos ha sido calificado por otro cliente con más de 4 estrellas.
```sql
SELECT DISTINCT 
    p.name AS producto
FROM details_favorites df
JOIN favorites f ON df.favorite_id = f.id
JOIN products p ON df.product_id = p.id
WHERE f.customer_id = 3
    AND p.id IN (
        SELECT qp.product_id
        FROM quality_products qp
        WHERE qp.customer_id != 3 AND qp.rating > 4
  );
```

16. Como operador, quiero saber qué productos no tienen imagen asignada pero sí han sido calificados.
```sql
SELECT 
    p.name AS producto
FROM products p
WHERE (p.image IS NULL)
    AND p.id IN (
        SELECT DISTINCT qp.product_id
        FROM quality_products qp
  );

```

17. Como auditor, quiero ver los planes de membresía sin periodo vigente.
```sql
SELECT 
    m.name AS membresia
FROM memberships m
WHERE m.id NOT IN (
    SELECT DISTINCT mp.membership_id
    FROM membershipperiods mp
);
```

18. Como especialista, quiero identificar los beneficios compartidos por más de una audiencia.
```sql
SELECT 
b.description AS beneficio
FROM benefits b
WHERE b.id IN (
    SELECT ab.benefit_id
    FROM audiencebenefits ab
    GROUP BY ab.benefit_id
    HAVING COUNT(DISTINCT ab.audience_id) > 1
);
```

19. Como técnico, quiero encontrar empresas cuyos productos no tengan unidad de medida definida.
```sql
SELECT
    c.name AS empresa
FROM companies c
WHERE c.id IN (
    SELECT cp.company_id
    FROM companyproducts cp
    WHERE cp.unitmeasure_id IS NULL
);
```

20. Como gestor de campañas, deseo obtener los clientes con membresía activa y sin productos favoritos.
```sql
SELECT
    c.name AS cliente
FROM customers c
WHERE c.membership_active = TRUE
  AND c.id NOT IN (
      SELECT DISTINCT f.customer_id
      FROM favorites f
  );
```

## 3. Funciones Agregadas

1. Obtener el promedio de calificación por producto
```sql
SELECT 
    p.name AS producto,
    AVG(qp.rating) AS promedio_calificacion
FROM products p
JOIN quality_products qp ON p.id = qp.product_id
GROUP BY p.id, p.name
ORDER BY promedio_calificacion DESC;
```

2. Contar cuántos productos ha calificado cada cliente
```sql
SELECT 
    c.name AS cliente,
    COUNT(DISTINCT qp.product_id) AS total_productos_calificados
FROM customers c
JOIN quality_products qp ON c.id = qp.customer_id
GROUP BY c.id, c.name
ORDER BY total_productos_calificados DESC;
```

3. Sumar el total de beneficios asignados por audiencia
```sql
SELECT 
    a.description AS audiencia,
    COUNT(ab.benefit_id) AS total_beneficios_asignados
FROM audiences a
JOIN audiencebenefits ab ON a.id = ab.audience_id
GROUP BY a.id, a.description
ORDER BY total_beneficios_asignados DESC;
```

4. Calcular la media de productos por empresa
```sql
SELECT 
    AVG(productos_por_empresa) AS media_productos_por_empresa
FROM (
    SELECT 
        company_id, 
        COUNT(product_id) AS productos_por_empresa
    FROM companyproducts
    GROUP BY company_id
) AS subconsulta;
```
5. Contar el total de empresas por ciudad
```sql
SELECT 
    cm.name AS ciudad,
    COUNT(*) AS total_empresas
FROM companies c
JOIN citiesormunicipalities cm ON c.city_id = cm.code
GROUP BY c.city_id, cm.name
ORDER BY total_empresas DESC;
```

6. Calcular el promedio de precios por unidad de medida
```sql
SELECT 
    u.description AS unidad_medida,
    AVG(cp.price) AS promedio_precio
FROM companyproducts cp
JOIN unitofmeasure u ON cp.unitmeasure_id = u.id
WHERE cp.unitmeasure_id IS NOT NULL
GROUP BY u.id, u.description
ORDER BY promedio_precio DESC;
```

7. Contar cuántos clientes hay por ciudad
```sql
SELECT 
    cm.name AS ciudad,
    COUNT(*) AS total_clientes
FROM customers cu
JOIN citiesormunicipalities cm ON cu.city_id = cm.code
GROUP BY cu.city_id, cm.name
ORDER BY total_clientes DESC;
```

8. Calcular planes de membresía por periodo
```sql
SELECT 
    p.name AS periodo,
    COUNT(mp.membership_id) AS total_membresias
FROM membershipperiods mp
JOIN periods p ON mp.period_id = p.id
GROUP BY p.id, p.name
ORDER BY total_membresias DESC;
```

9. Ver el promedio de calificaciones dadas por un cliente a sus favoritos
```sql
SELECT 
    cu.name AS cliente,
    AVG(qp.rating) AS promedio_calificacion_favoritos
FROM favorites f
JOIN customers cu ON f.customer_id = cu.id
JOIN details_favorites df ON f.id = df.favorite_id
JOIN quality_products qp ON df.product_id = qp.product_id AND qp.customer_id = f.customer_id
GROUP BY f.customer_id, cu.name;
```

10. Consultar la fecha más reciente en que se calificó un producto
```sql
SELECT 
    p.name AS producto,
    MAX(qp.daterating) AS fecha_ultima_calificacion
FROM products p
JOIN quality_products qp ON p.id = qp.product_id
GROUP BY p.id, p.name;
```

11. Obtener la desviación estándar de precios por categoría
```sql
SELECT 
    c.description AS categoria,
    STDDEV(cp.price) AS desviacion_estandar_precio
FROM companyproducts cp
JOIN products p ON cp.product_id = p.id
JOIN categories c ON p.category_id = c.id
GROUP BY c.id, c.description
ORDER BY desviacion_estandar_precio DESC;
```

12. Contar cuántas veces un producto fue favorito
```sql
SELECT 
    p.name AS producto,
    COUNT(df.id) AS veces_favorito
FROM details_favorites df
JOIN products p ON df.product_id = p.id
GROUP BY p.id, p.name
ORDER BY veces_favorito DESC;
```

13. Calcular el porcentaje de productos evaluados
```sql
SELECT 
    ROUND(
        (COUNT(DISTINCT qp.product_id) * 100.0 / 
        (SELECT COUNT(*) FROM products)), 
        2
    ) AS porcentaje_evaluados
FROM quality_products qp;
```

14. Ver el promedio de rating por encuesta
```sql
SELECT 
	p.name AS encuesta, 
	AVG(r.rating) AS promedio
FROM rates AS r
JOIN polls AS p ON r.poll_id = p.id
GROUP BY p.name;
```

15. Calcular el promedio y total de beneficios por plan
```sql
SELECT 
    m.name AS membresia,
    COUNT(mb.benefit_id) AS total_beneficios,
    AVG(b.value) AS promedio_ponderacion
FROM 
    membershipbenefits mb
JOIN memberships m ON mb.membership_id = m.id
JOIN benefits b ON mb.benefit_id = b.id
GROUP BY m.id, m.name;
```

16. Obtener media y varianza de precios por empresa
```sql
SELECT 
    co.name AS empresa,
    AVG(cp.price) AS promedio_precio,
    VARIANCE(cp.price) AS varianza_precio,
    MIN(cp.price) AS precio_minimo,
    MAX(cp.price) AS precio_maximo
FROM companyproducts cp
JOIN companies co ON cp.company_id = co.id
GROUP BY cp.company_id, co.name;
```

17. Ver total de productos disponibles en la ciudad del cliente
```sql
SELECT 
    cu.name AS cliente,
    ci.name AS ciudad,
    COUNT(DISTINCT cp.product_id) AS total_productos_disponibles
FROM customers cu
JOIN citiesormunicipalities ci ON cu.city_id = ci.code
JOIN companies co ON co.city_id = cu.city_id
JOIN companyproducts cp ON cp.company_id = co.id
WHERE cp.is_available = TRUE
GROUP BY cu.id, cu.name, ci.name;
```

18. Contar productos únicos por tipo de empresa
```sql
SELECT 
    cat.description AS tipo_empresa,
    COUNT(DISTINCT cp.product_id) AS productos_unicos
FROM companies c
JOIN categories cat ON c.category_id = cat.id
JOIN companyproducts cp ON cp.company_id = c.id
GROUP BY cat.id, cat.description;
```

19. Ver total de clientes sin correo electrónico registrado
```sql
SELECT 
    COUNT(*) AS total_clientes_sin_email
FROM customers
WHERE email IS NULL;
```

20. Empresa con más productos calificados
```sql
SELECT 
    c.name AS nombre_empresa,
    COUNT(DISTINCT r.company_id, r.product_id) AS productos_calificados
FROM companies c
JOIN rates r ON r.company_id = c.id
GROUP BY c.id, c.name
ORDER BY productos_calificados DESC
LIMIT 1;
```

## 4. Procedimientos Almacenados

1.  Registrar una nueva calificación y actualizar el promedio
```sql
DELIMITER $$

CREATE PROCEDURE sp_registrar_calificacion_y_actualizar_promedio (
    IN p_product_id INT,
    IN p_customer_id INT,
    IN p_company_id VARCHAR(20),
    IN p_poll_id INT,
    IN p_rating DOUBLE
)
BEGIN
    INSERT INTO rates (customer_id, company_id, poll_id, daterating, rating, created_at)
    VALUES (p_customer_id, p_company_id, p_poll_id, NOW(), p_rating, NOW());

    DECLARE v_avg_rating DOUBLE;

    SELECT AVG(r.rating)
    INTO v_avg_rating
    FROM rates r
    JOIN companyproducts cp ON r.company_id = cp.company_id
    WHERE cp.product_id = p_product_id;

    UPDATE products
    SET average_rating = v_avg_rating
    WHERE id = p_product_id;
END$$

DELIMITER ;
```

2. Insertar empresa y asociar productos por defecto
```sql
DELIMITER $$

CREATE PROCEDURE sp_insertar_empresa_y_asociar_huerfanos (
    IN p_company_id VARCHAR(20),
    IN p_type_id INT,
    IN p_name VARCHAR(80),
    IN p_category_id INT,
    IN p_city_id VARCHAR(7),
    IN p_audience_id INT,
    IN p_cellphone VARCHAR(15),
    IN p_email VARCHAR(80),
    IN p_is_active BOOLEAN
)
BEGIN
    INSERT INTO companies (id, type_id, name, category_id, city_id, audience_id, cellphone, email, is_active, updated_at)
    VALUES (p_company_id, p_type_id, p_name, p_category_id, p_city_id, p_audience_id, p_cellphone, p_email, p_is_active, NOW());

    INSERT INTO companyproducts (company_id, product_id, price, unitmeasure_id, is_available)
    SELECT
        p_company_id,
        p.id,
        p.price,   -- o precio fijo por defecto
        1,
        TRUE
    FROM products p
    LEFT JOIN companyproducts cp ON p.id = cp.product_id
    WHERE cp.product_id IS NULL;
END$$

DELIMITER ;
```

3. Añadir producto favorito validando duplicados
```sql
DELIMITER $$

CREATE PROCEDURE sp_agregar_producto_favorito (
    IN p_customer_id INT,
    IN p_company_id VARCHAR(20),
    IN p_product_id INT
)
BEGIN
    DECLARE v_favorite_id INT;

    SELECT id INTO v_favorite_id
    FROM favorites
    WHERE customer_id = p_customer_id AND company_id = p_company_id;

    IF v_favorite_id IS NULL THEN
        INSERT INTO favorites (customer_id, company_id)
        VALUES (p_customer_id, p_company_id);
        SET v_favorite_id = LAST_INSERT_ID();
    END IF;

    IF NOT EXISTS (
        SELECT 1 
        FROM details_favorites 
        WHERE favorite_id = v_favorite_id AND product_id = p_product_id
    ) THEN
        INSERT INTO details_favorites (favorite_id, product_id)
        VALUES (v_favorite_id, p_product_id);
    END IF;

END$$

DELIMITER ;
```

4. Generar resumen mensual de calificaciones por empresa
```sql
DELIMITER $$

CREATE PROCEDURE sp_generar_resumen_mensual_calificaciones ()
BEGIN
    DECLARE v_year INT;
    DECLARE v_month INT;
    SET v_year = YEAR(CURDATE());
    SET v_month = MONTH(CURDATE());

    INSERT INTO resumen_calificaciones (company_id, promedio_calificacion, total_calificaciones, mes_resumen)
    SELECT 
        company_id,
        AVG(rating) AS promedio,
        COUNT(*) AS total,
        DATE_FORMAT(CURDATE(), '%Y-%m-01') AS mes_resumen
    FROM rates
    WHERE 
        YEAR(daterating) = v_year
        AND MONTH(daterating) = v_month
    GROUP BY company_id;

END$$

DELIMITER ;
```

5. Calcular beneficios activos por membresía
```sql
DELIMITER $$

CREATE PROCEDURE sp_beneficios_activos_membresia ()
BEGIN
    SELECT 
        mb.membership_id,
        mb.period_id,
        mb.benefit_id,
        b.description AS beneficio,
        mp.start_date,
        mp.end_date,
        b.is_active
    FROM 
        membershipbenefits mb
        INNER JOIN membershipperiods mp ON mb.membership_id = mp.membership_id AND mb.period_id = mp.period_id
        INNER JOIN benefits b ON mb.benefit_id = b.id
    WHERE 
        mp.start_date <= CURDATE()
        AND mp.end_date >= CURDATE()
        AND b.is_active = TRUE;
END$$

DELIMITER ;
```

6. Eliminar productos huérfanos
```sql
DELIMITER $$

CREATE PROCEDURE sp_eliminar_productos_huerfanos ()
BEGIN
    DELETE FROM products
    WHERE id IN (
        SELECT p.id
        FROM (
            SELECT p.id
            FROM products p
            LEFT JOIN rates r ON p.id = r.product_id
            LEFT JOIN companyproducts cp ON p.id = cp.product_id
            WHERE r.product_id IS NULL AND cp.product_id IS NULL
        ) AS subconsulta
    );
END$$

DELIMITER ;
```

7. Actualizar precios de productos por categoría
```sql
DELIMITER $$

CREATE PROCEDURE sp_actualizar_precios_categoria (
    IN p_categoria_id INT,
    IN p_factor DECIMAL(5,2)
)
BEGIN
    UPDATE companyproducts cp
    JOIN products p ON cp.product_id = p.id
    SET cp.price = cp.price * p_factor
    WHERE p.category_id = p_categoria_id;
END$$

DELIMITER ;
```

8. Validar inconsistencia entre rates y quality_products
```sql
DELIMITER $$

CREATE PROCEDURE sp_validar_inconsistencias_rates_quality ()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE v_customer_id INT;
    DECLARE v_company_id VARCHAR(20);
    DECLARE v_poll_id INT;
    DECLARE v_desc TEXT;
    DECLARE cur CURSOR FOR 
        SELECT r.customer_id, r.company_id, r.poll_id
        FROM rates r
        LEFT JOIN quality_products qp 
            ON r.customer_id = qp.customer_id 
            AND r.company_id = qp.company_id 
            AND r.poll_id = qp.poll_id
        WHERE qp.poll_id IS NULL;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO v_customer_id, v_company_id, v_poll_id;
        IF done THEN
            LEAVE read_loop;
        END IF;

        SET v_desc = CONCAT(
            'Inconsistencia: rates sin quality_products → customer_id=', 
            v_customer_id, ', company_id=', v_company_id, ', poll_id=', v_poll_id
        );

        INSERT INTO errores_log (tipo_error, descripcion, fecha)
        VALUES ('INCONSISTENCIA_RATES_QP', v_desc, NOW());
    END LOOP;

    CLOSE cur;
END$$

DELIMITER ;
```

9. Asignar beneficios a nuevas audiencias
```sql
DELIMITER $$

CREATE PROCEDURE sp_asignar_beneficio_audiencia (
    IN p_benefit_id INT,
    IN p_audience_id INT
)
BEGIN
    DECLARE total INT DEFAULT 0;

    SELECT COUNT(*) INTO total
    FROM audiencebenefits
    WHERE benefit_id = p_benefit_id AND audience_id = p_audience_id;

    IF total = 0 THEN
        INSERT INTO audiencebenefits (audience_id, benefit_id)
        VALUES (p_audience_id, p_benefit_id);
    END IF;
END$$

DELIMITER ;
```

10. Activar planes de membresía vencidos con pago confirmado
```sql
DELIMITER $$

CREATE PROCEDURE sp_activar_membresias_vencidas ()
BEGIN
    UPDATE membershipperiods
    SET status = 'ACTIVA'
    WHERE end_date < CURDATE()
      AND pago_confirmado = TRUE
      AND status <> 'ACTIVA';
END$$

DELIMITER ;
```

11. Listar productos favoritos del cliente con su calificación
```sql
DELIMITER $$

CREATE PROCEDURE sp_listar_favoritos_con_rating (
  IN p_customer_id INT
)
BEGIN
  SELECT 
    df.product_id,
    p.name AS nombre_producto,
    IFNULL(AVG(r.rating), 0) AS promedio_rating
  FROM favorites f
  JOIN details_favorites df ON df.favorite_id = f.id
  JOIN products p ON p.id = df.product_id
  LEFT JOIN rates r 
    ON r.customer_id = f.customer_id 
   AND r.company_id = f.company_id 
   AND r.poll_id IN (
      SELECT poll_id 
      FROM quality_products 
      WHERE product_id = df.product_id AND customer_id = f.customer_id
   )
  WHERE f.customer_id = p_customer_id
  GROUP BY df.product_id, p.name;
END$$

DELIMITER ;
```

12. Registrar encuesta y sus preguntas asociadas
```sql
DELIMITER $$

CREATE PROCEDURE sp_registrar_encuesta_y_preguntas (
  IN p_name VARCHAR(80),
  IN p_description TEXT,
  IN p_isactive BOOLEAN,
  IN p_categorypoll_id INT,
  IN p_questions TEXT
)
BEGIN
  DECLARE v_poll_id INT;
  DECLARE v_question TEXT;
  DECLARE delim CHAR(1) DEFAULT ';';

  INSERT INTO polls (name, description, isactive, categorypoll_id)
  VALUES (p_name, p_description, p_isactive, p_categorypoll_id);

  SET v_poll_id = LAST_INSERT_ID();

  DECLARE v_pos INT DEFAULT 1;
  DECLARE v_len INT;

  SET v_len = LENGTH(p_questions);

  WHILE v_pos > 0 DO
    SET v_pos = LOCATE(delim, p_questions);

    IF v_pos > 0 THEN
      SET v_question = TRIM(SUBSTRING(p_questions, 1, v_pos - 1));
      SET p_questions = SUBSTRING(p_questions, v_pos + 1);
    ELSE
      SET v_question = TRIM(p_questions);
    END IF;

    IF v_question != '' THEN
      INSERT INTO poll_questions (poll_id, question_text)
      VALUES (v_poll_id, v_question);
    END IF;
  END WHILE;

END$$

DELIMITER ;
```

13. Eliminar favoritos antiguos sin calificaciones
```sql
DELIMITER $$

CREATE PROCEDURE sp_eliminar_favoritos_antiguos_sin_calificacion ()
BEGIN
  DELETE df
  FROM details_favorites df
  JOIN favorites f ON f.id = df.favorite_id
  LEFT JOIN rates r 
    ON r.customer_id = f.customer_id
   AND r.company_id = f.company_id
   AND r.poll_id IN (
      SELECT poll_id 
      FROM quality_products 
      WHERE product_id = df.product_id AND customer_id = f.customer_id
   )
  WHERE r.customer_id IS NULL
    AND df.fecha_agregado < DATE_SUB(NOW(), INTERVAL 1 YEAR);
END$$

DELIMITER ;
```

14. Asociar beneficios automáticamente por audiencia
```sql
DELIMITER $$

CREATE PROCEDURE sp_asociar_beneficios_por_audiencia (
  IN p_audience_id INT
)
BEGIN
  INSERT INTO audiencebenefits (audience_id, benefit_id)
  SELECT 
    p_audience_id, b.id
  FROM benefits b
  WHERE b.is_active = TRUE
    AND NOT EXISTS (
      SELECT 1 
      FROM audiencebenefits ab 
      WHERE ab.audience_id = p_audience_id AND ab.benefit_id = b.id
    );
END$$

DELIMITER ;
```

15. Historial de cambios de precio
```sql
DELIMITER $$

CREATE PROCEDURE sp_actualizar_precio_y_guardar_historial (
  IN p_company_id VARCHAR(20),
  IN p_product_id INT,
  IN p_nuevo_precio DOUBLE
)
BEGIN
  DECLARE v_precio_anterior DOUBLE;

  SELECT price INTO v_precio_anterior
  FROM companyproducts
  WHERE company_id = p_company_id AND product_id = p_product_id;

  IF v_precio_anterior IS NOT NULL AND v_precio_anterior <> p_nuevo_precio THEN

    UPDATE companyproducts
    SET price = p_nuevo_precio
    WHERE company_id = p_company_id AND product_id = p_product_id;

    INSERT INTO historial_precios (
      company_id, product_id, precio_anterior, precio_nuevo
    ) VALUES (
      p_company_id, p_product_id, v_precio_anterior, p_nuevo_precio
    );

  END IF;

END$$

DELIMITER ;
```

16. Registrar encuesta activa automáticamente
```sql
DELIMITER $$

CREATE PROCEDURE sp_registrar_encuesta_activa (
  IN p_name VARCHAR(80),
  IN p_description TEXT,
  IN p_categorypoll_id INT
)
BEGIN
  INSERT INTO polls (name, description, isactive, categorypoll_id, created_at)
  VALUES (p_name, p_description, TRUE, p_categorypoll_id, NOW());
END$$

DELIMITER ;
```

17. Actualizar unidad de medida de productos sin afectar ventas
```sql
DELIMITER $$

CREATE PROCEDURE sp_actualizar_unitmeasure_sin_ventas (
  IN p_product_id INT,
  IN p_new_unit_id INT
)
BEGIN
  DECLARE v_count INT;

  SELECT COUNT(*) INTO v_count
  FROM order_details
  WHERE product_id = p_product_id;

  IF v_count = 0 THEN
    UPDATE companyproducts
    SET unitmeasure_id = p_new_unit_id
    WHERE product_id = p_product_id;

    SELECT 'Unidad de medida actualizada correctamente.' AS mensaje;
  ELSE
    SELECT 'No se puede actualizar: el producto tiene ventas registradas.' AS mensaje;
  END IF;

END$$

DELIMITER ;
```

18. Recalcular promedios de calidad semanalmente
```sql
DELIMITER $$

CREATE PROCEDURE sp_recalcular_promedios_calidad ()
BEGIN
  UPDATE products p
  SET p.average_rating = (
    SELECT IFNULL(AVG(qp.rating), 0)
    FROM quality_products qp
    WHERE qp.product_id = p.id
  );
END$$

DELIMITER ;
```

19. Validar claves foráneas entre calificaciones y encuestas
```sql
DELIMITER $$

CREATE PROCEDURE sp_validar_fk_rates_polls ()
BEGIN
  DECLARE done INT DEFAULT 0;
  DECLARE v_customer_id INT;
  DECLARE v_company_id VARCHAR(20);
  DECLARE v_poll_id INT;
  DECLARE v_desc TEXT;

  DECLARE cur CURSOR FOR
    SELECT r.customer_id, r.company_id, r.poll_id
    FROM rates r
    LEFT JOIN polls p ON r.poll_id = p.id
    WHERE p.id IS NULL;

  DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

  OPEN cur;

  read_loop: LOOP
    FETCH cur INTO v_customer_id, v_company_id, v_poll_id;
    IF done THEN
      LEAVE read_loop;
    END IF;

    SET v_desc = CONCAT(
      'Inconsistencia: rates con poll_id inexistente → customer_id=',
      v_customer_id, ', company_id=', v_company_id, ', poll_id=', v_poll_id
    );

    INSERT INTO errores_log (tipo_error, descripcion, fecha)
    VALUES ('FK_RATES_POLLS', v_desc, NOW());
  END LOOP;

  CLOSE cur;

END$$

DELIMITER ;
```

20. Generar el top 10 de productos más calificados por ciudad
```sql
DELIMITER $$

CREATE PROCEDURE sp_top10_productos_mas_calificados_por_ciudad ()
BEGIN
  SELECT *
  FROM (
    SELECT
      c.city_id,
      cp.product_id,
      COUNT(*) AS total_calificaciones,
      ROW_NUMBER() OVER (PARTITION BY c.city_id ORDER BY COUNT(*) DESC) AS ranking
    FROM
      rates r
      JOIN companies c ON r.company_id = c.id
      JOIN companyproducts cp ON cp.company_id = c.id
    WHERE
      cp.product_id IS NOT NULL
    GROUP BY
      c.city_id, cp.product_id
  ) ranked
  WHERE ranked.ranking <= 10;
END$$

DELIMITER ;
```

## 5. Triggers

1. Actualizar la fecha de modificación de un producto
```sql
DELIMITER $$

CREATE TRIGGER trg_update_product_timestamp
BEFORE UPDATE ON products
FOR EACH ROW
BEGIN
   SET NEW.updated_at = NOW();
END$$

DELIMITER ;
```

2. Registrar log cuando un cliente califica un producto
```sql
DELIMITER $$

CREATE TRIGGER trg_log_calificacion_cliente
AFTER INSERT ON rates
FOR EACH ROW
BEGIN
    INSERT INTO log_acciones (accion, descripcion, fecha)
    VALUES (
        'Calificación de producto',
        CONCAT('El cliente con ID ', NEW.customer_id,
               ' calificó un producto de la empresa ', NEW.company_id,
               ' en la encuesta ', NEW.poll_id,
               ' con un puntaje de ', NEW.rating),
        NOW()
    );
END$$

DELIMITER ;
```

3. Impedir insertar productos sin unidad de medida
```sql
DELIMITER $$

CREATE TRIGGER trg_prevent_null_unitmeasure
BEFORE INSERT ON companyproducts
FOR EACH ROW
BEGIN
    IF NEW.unitmeasure_id IS NULL THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'No se permite insertar un producto sin unidad de medida.';
    END IF;
END$$

DELIMITER ;
```

4. Validar calificaciones no mayores a 5
```sql
DELIMITER $$

CREATE TRIGGER trg_validar_calificacion_maxima
BEFORE INSERT ON rates
FOR EACH ROW
BEGIN
    IF NEW.rating > 5 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'La calificación no puede ser mayor a 5.';
    END IF;
END$$

DELIMITER ;
```

5. Actualizar estado de membresía cuando vence
```sql
DELIMITER $$

CREATE TRIGGER trg_actualizar_estado_membresia
BEFORE UPDATE ON membershipperiods
FOR EACH ROW
BEGIN
    IF NEW.end_date < CURDATE() THEN
        SET NEW.status = 'INACTIVA';
    END IF;
END$$

DELIMITER ;

```

6. Evitar duplicados de productos por empresa
```sql
DELIMITER $$

CREATE TRIGGER trg_prevenir_producto_duplicado
BEFORE INSERT ON companyproducts
FOR EACH ROW
BEGIN
    DECLARE nombre_producto VARCHAR(60);
    DECLARE id_existente INT;

    SELECT name INTO nombre_producto
    FROM products
    WHERE id = NEW.product_id;

    SELECT cp.product_id INTO id_existente
    FROM companyproducts cp
    JOIN products p ON cp.product_id = p.id
    WHERE cp.company_id = NEW.company_id AND p.name = nombre_producto
    LIMIT 1;

    IF id_existente IS NOT NULL THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Error: Ya existe un producto con ese nombre en esta empresa.';
    END IF;
END$$

DELIMITER ;
```

7. Enviar notificación al añadir un favorito
```sql
DELIMITER $$

CREATE TRIGGER trg_notificar_favorito
AFTER INSERT ON details_favorites
FOR EACH ROW
BEGIN
    DECLARE cliente_id INT;
    DECLARE producto_nombre VARCHAR(60);

    SELECT customer_id INTO cliente_id
    FROM favorites
    WHERE id = NEW.favorite_id;

    SELECT name INTO producto_nombre
    FROM products
    WHERE id = NEW.product_id;

    INSERT INTO notificaciones (mensaje, fecha)
    VALUES (
        CONCAT('El cliente con ID ', cliente_id, 
               ' añadió el producto "', producto_nombre, 
               '" a sus favoritos.'),
        NOW()
    );
END$$

DELIMITER ;
```

8. Insertar fila en quality_products tras calificación
```sql
DELIMITER $$

CREATE TRIGGER trg_insertar_quality_al_calificar
AFTER INSERT ON rates
FOR EACH ROW
BEGIN
    DECLARE producto_id INT;

    SELECT product_id INTO producto_id
    FROM quality_products
    WHERE poll_id = NEW.poll_id
      AND company_id = NEW.company_id
    LIMIT 1;

    IF producto_id IS NOT NULL THEN
        INSERT INTO quality_products (
            product_id, customer_id, poll_id, company_id, daterating, rating
        )
        VALUES (
            producto_id, NEW.customer_id, NEW.poll_id, NEW.company_id, NEW.daterating, NEW.rating
        );
    END IF;
END$$

DELIMITER ;
```

9. Eliminar favoritos si se elimina el producto
```sql
DELIMITER $$

CREATE TRIGGER trg_eliminar_favoritos_al_borrar_producto
AFTER DELETE ON products
FOR EACH ROW
BEGIN
    DELETE FROM details_favorites
    WHERE product_id = OLD.id;
END$$

DELIMITER ;
```

10. Bloquear modificación de audiencias activas
```sql
DELIMITER $$

CREATE TRIGGER trg_bloquear_modificacion_audiencia_activa
BEFORE UPDATE ON audiences
FOR EACH ROW
BEGIN
    DECLARE en_uso INT DEFAULT 0;

    SELECT COUNT(c.id) INTO en_uso
    FROM customers c
    WHERE c.audience_id = OLD.id;

    IF en_uso = 0 THEN
        SELECT COUNT(comp.id) INTO en_uso
        FROM companies comp
        WHERE comp.audience_id = OLD.id;
    END IF;

    IF en_uso = 0 THEN
        SELECT COUNT(mb.audience_id) INTO en_uso
        FROM membershipbenefits mb
        WHERE mb.audience_id = OLD.id;
    END IF;

    IF en_uso > 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'No se puede modificar una audiencia que está actualmente en uso.';
    END IF;
END$$

DELIMITER ;
```

11. Recalcular promedio de calidad del producto tras nueva evaluación
```sql
DELIMITER $$

CREATE TRIGGER trg_actualizar_promedio_calidad
AFTER INSERT ON rates
FOR EACH ROW
BEGIN
    DECLARE producto_id INT;
    DECLARE nuevo_promedio DOUBLE;

    SELECT product_id INTO producto_id
    FROM quality_products
    WHERE poll_id = NEW.poll_id
      AND company_id = NEW.company_id
      AND customer_id = NEW.customer_id
    LIMIT 1;

    SELECT AVG(rating) INTO nuevo_promedio
    FROM quality_products
    WHERE product_id = producto_id;

    UPDATE products
    SET average_rating = nuevo_promedio
    WHERE id = producto_id;
END$$

DELIMITER ;
```

12. Registrar asignación de nuevo beneficio
```sql
DELIMITER $$

CREATE TRIGGER trg_log_beneficio_membresia
AFTER INSERT ON membershipbenefits
FOR EACH ROW
BEGIN
    INSERT INTO bitacora_beneficios (accion)
    VALUES (
        CONCAT(
            'Se asignó beneficio ID ', NEW.benefit_id,
            ' a membresía ID ', NEW.membership_id,
            ' para audiencia ID ', NEW.audience_id,
            ' en periodo ID ', NEW.period_id
        )
    );
END$$

DELIMITER ;

DELIMITER $$

CREATE TRIGGER trg_log_beneficio_audiencia
AFTER INSERT ON audiencebenefits
FOR EACH ROW
BEGIN
    INSERT INTO bitacora_beneficios (accion)
    VALUES (
        CONCAT(
            'Se asignó beneficio ID ', NEW.benefit_id,
            ' directamente a audiencia ID ', NEW.audience_id
        )
    );
END$$

DELIMITER ;
```

13. Impedir doble calificación por parte del cliente
```sql
DELIMITER $$

CREATE TRIGGER trg_prevenir_calificacion_duplicada
BEFORE INSERT ON rates
FOR EACH ROW
BEGIN
    DECLARE producto_id INT;
    DECLARE existe INT;

    SELECT product_id INTO producto_id
    FROM quality_products
    WHERE poll_id = NEW.poll_id
      AND company_id = NEW.company_id
      AND customer_id = NEW.customer_id
    LIMIT 1;

    SELECT COUNT(product_id) INTO existe
    FROM quality_products
    WHERE product_id = producto_id
      AND customer_id = NEW.customer_id;

    IF existe > 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Ya has calificado este producto anteriormente.';
    END IF;
END$$

DELIMITER ;
```

14. Validar correos duplicados en clientes
```sql
DELIMITER $$

CREATE TRIGGER trg_validar_email_cliente
BEFORE INSERT ON customers
FOR EACH ROW
BEGIN
    DECLARE existe INT;

    SELECT COUNT(email) INTO existe
    FROM customers
    WHERE email = NEW.email;

    IF existe > 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'El correo ya está registrado para otro cliente.';
    END IF;
END$$

DELIMITER ;
```

15. Eliminar detalles de favoritos huérfanos
```sql
DELIMITER $$

CREATE TRIGGER trg_eliminar_detalles_huerfanos
AFTER DELETE ON favorites
FOR EACH ROW
BEGIN
    DELETE FROM details_favorites
    WHERE favorite_id = OLD.id;
END$$

DELIMITER ;
```

16. Actualizar campo updated_at en companies
```sql
DELIMITER $$

CREATE TRIGGER trg_actualizar_fecha_company
BEFORE UPDATE ON companies
FOR EACH ROW
BEGIN
    SET NEW.updated_at = NOW();
END$$

DELIMITER ;
```

17. Impedir borrar ciudad si hay empresas activas
```sql
DELIMITER $$

CREATE TRIGGER trg_prevenir_borrado_ciudad_con_empresas
BEFORE DELETE ON citiesormunicipalities
FOR EACH ROW
BEGIN
    DECLARE empresas_activas INT;

    SELECT COUNT(id) INTO empresas_activas
    FROM companies
    WHERE city_id = OLD.code;

    IF empresas_activas > 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'No se puede eliminar la ciudad: existen empresas activas registradas en ella.';
    END IF;
END$$

DELIMITER ;
```

18. Registrar cambios de estado en encuestas
```sql
DELIMITER $$

CREATE TRIGGER trg_log_cambio_estado_poll
AFTER UPDATE ON polls
FOR EACH ROW
BEGIN
    IF OLD.isactive <> NEW.isactive THEN
        INSERT INTO log_polls_status (poll_id, new_status, changed_at)
        VALUES (NEW.id, NEW.isactive, NOW());
    END IF;
END$$

DELIMITER ;
```

19. Sincronizar rates y quality_products
```sql
DELIMITER $$

CREATE TRIGGER trg_sincronizar_quality_products
AFTER INSERT ON rates
FOR EACH ROW
BEGIN
    DECLARE existe INT;

    SELECT COUNT(product_id) INTO existe
    FROM quality_products
    WHERE product_id = (
        SELECT product_id 
        FROM companyproducts 
        WHERE company_id = NEW.company_id
        LIMIT 1
    )
    AND customer_id = NEW.customer_id
    AND poll_id = NEW.poll_id;

    IF existe > 0 THEN
        UPDATE quality_products
        SET rating = NEW.rating,
            daterating = NEW.daterating
        WHERE product_id = (
            SELECT product_id 
            FROM companyproducts 
            WHERE company_id = NEW.company_id
            LIMIT 1
        )
        AND customer_id = NEW.customer_id
        AND poll_id = NEW.poll_id;
    ELSE
        INSERT INTO quality_products (product_id, customer_id, poll_id, company_id, daterating, rating)
        VALUES (
            (SELECT product_id 
             FROM companyproducts 
             WHERE company_id = NEW.company_id 
             LIMIT 1),
            NEW.customer_id,
            NEW.poll_id,
            NEW.company_id,
            NEW.daterating,
            NEW.rating
        );
    END IF;
END$$

DELIMITER ;
```

20. Eliminar productos sin relación a empresas
```sql
DELIMITER $$

CREATE TRIGGER trg_eliminar_producto_sin_empresa
AFTER DELETE ON companyproducts
FOR EACH ROW
BEGIN
    DECLARE relaciones_restantes INT;

    SELECT COUNT(company_id) INTO relaciones_restantes
    FROM companyproducts
    WHERE product_id = OLD.product_id;

    IF relaciones_restantes = 0 THEN
        DELETE FROM products
        WHERE id = OLD.product_id;
    END IF;
END$$

DELIMITER ;
```

## 6. Events

1. Borrar productos sin actividad cada 6 meses
```sql
DELIMITER $$

CREATE PROCEDURE borrar_productos_inactivos()
BEGIN
    DELETE FROM products
    WHERE id NOT IN (SELECT DISTINCT product_id FROM companyproducts)
      AND id NOT IN (SELECT DISTINCT product_id FROM details_favorites)
      AND id NOT IN (SELECT DISTINCT product_id FROM quality_products);
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_borrar_productos_inactivos
ON SCHEDULE EVERY 6 MONTH
DO
    CALL borrar_productos_inactivos();
```

2. Recalcular el promedio de calificaciones semanalmente
```sql
DELIMITER $$

CREATE PROCEDURE actualizar_promedio_rating()
BEGIN
    UPDATE products p
    JOIN (
        SELECT qp.product_id, AVG(qp.rating) AS promedio
        FROM quality_products qp
        GROUP BY qp.product_id
    ) sub ON p.id = sub.product_id
    SET p.average_rating = sub.promedio;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_actualizar_promedio_rating
ON SCHEDULE EVERY 1 WEEK
DO
    CALL actualizar_promedio_rating();
```

3. Actualizar precios según inflación mensual
```sql
DELIMITER $$

CREATE PROCEDURE actualizar_precios_inflacion()
BEGIN
    UPDATE companyproducts
    SET price = price * 1.03;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_actualizar_precios_inflacion
ON SCHEDULE EVERY 1 MONTH
DO
    CALL actualizar_precios_inflacion();
```

4. Crear backups lógicos diariamente
```sql
DELIMITER $$

CREATE PROCEDURE realizar_backup_diario()
BEGIN
    DELETE FROM products_backup;
    DELETE FROM rates_backup;

    INSERT INTO products_backup (
        id, name, detail, price, category_id, image, updated_at, average_rating
    )
    SELECT id, name, detail, price, category_id, image, updated_at, average_rating
    FROM products;

    INSERT INTO rates_backup (
        customer_id, company_id, poll_id, daterating, rating
    )
    SELECT customer_id, company_id, poll_id, daterating, rating
    FROM rates;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_backup_diario
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_DATE + INTERVAL 0 HOUR
DO
    CALL realizar_backup_diario();
```

5. Notificar sobre productos favoritos sin calificar
```sql
DELIMITER $$

CREATE PROCEDURE recordar_favoritos_no_calificados()
BEGIN
    DELETE FROM user_reminders WHERE DATE(fecha) = CURDATE();

    INSERT INTO user_reminders (
        customer_id,
        product_id,
        mensaje,
        fecha
    )
    SELECT
        f.customer_id,
        df.product_id,
        CONCAT('Recuerda calificar el producto con ID ', df.product_id),
        NOW()
    FROM details_favorites df
    JOIN favorites f ON df.favorite_id = f.id
    LEFT JOIN quality_products qp 
        ON qp.customer_id = f.customer_id 
        AND qp.product_id = df.product_id
    WHERE qp.product_id IS NULL;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_recordar_favoritos_sin_calificar
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_DATE + INTERVAL 1 HOUR
DO
    CALL recordar_favoritos_no_calificados();
```

6. Revisar inconsistencias entre empresa y productos
```sql
DELIMITER $$

CREATE PROCEDURE revisar_inconsistencias_empresas_productos()
BEGIN
    DELETE FROM errores_log WHERE DATE(fecha) = CURDATE();

    INSERT INTO errores_log (
        tipo_error,
        descripcion,
        fecha
    )
    SELECT
        'Producto sin empresa',
        CONCAT('El producto con ID ', p.id, ' - "', p.name, '" no está asociado a ninguna empresa.'),
        NOW()
    FROM products p
    WHERE NOT EXISTS (
        SELECT cp.product_id
        FROM companyproducts cp
        WHERE cp.product_id = p.id
    );

    INSERT INTO errores_log (
        tipo_error,
        descripcion,
        fecha
    )
    SELECT
        'Empresa sin productos',
        CONCAT('La empresa con ID ', c.id, ' - "', c.name, '" no tiene productos asociados.'),
        NOW()
    FROM companies c
    WHERE NOT EXISTS (
        SELECT cp.company_id
        FROM companyproducts cp
        WHERE cp.company_id = c.id
    );
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_revisar_inconsistencias_empresas_productos
ON SCHEDULE EVERY 1 WEEK
STARTS CURRENT_DATE + INTERVAL (7 - WEEKDAY(CURRENT_DATE)) DAY
DO
    CALL revisar_inconsistencias_empresas_productos();
```

7. Archivar membresías vencidas diariamente
```sql
DELIMITER $$

CREATE PROCEDURE archivar_membresias_vencidas()
BEGIN
    UPDATE membershipperiods
    SET status = 'INACTIVA'
    WHERE end_date < CURDATE()
      AND status != 'INACTIVA';
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_archivar_membresias_vencidas
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_DATE + INTERVAL 1 DAY
DO
    CALL archivar_membresias_vencidas();
```

8. Notificar beneficios nuevos a usuarios semanalmente
```sql
DELIMITER $$

CREATE PROCEDURE notificar_beneficios_nuevos()
BEGIN
    INSERT INTO notificaciones (mensaje, fecha)
    SELECT 
        CONCAT('Nuevo beneficio: ', description, ' - ', detail),
        NOW()
    FROM benefits
    WHERE created_at >= NOW() - INTERVAL 7 DAY;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_notificar_beneficios_nuevos
ON SCHEDULE EVERY 1 WEEK
STARTS CURRENT_DATE + INTERVAL 1 WEEK
DO
    CALL notificar_beneficios_nuevos();
```

9. Calcular cantidad de favoritos por cliente mensualmente
```sql
DELIMITER $$

CREATE PROCEDURE calcular_favoritos_mensual()
BEGIN
    INSERT INTO favoritos_resumen (customer_id, total_favoritos, mes_resumen)
    SELECT 
        f.customer_id,
        COUNT(df.product_id),
        CURDATE()
    FROM favorites f
    JOIN details_favorites df ON f.id = df.favorite_id
    GROUP BY f.customer_id;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_calcular_favoritos_mensual
ON SCHEDULE EVERY 1 MONTH
STARTS CURRENT_DATE + INTERVAL 1 MONTH
DO
    CALL calcular_favoritos_mensual();
```

10. Validar claves foráneas semanalmente
```sql
DELIMITER $$

CREATE PROCEDURE validar_claves_foraneas()
BEGIN
    INSERT INTO inconsistencias_fk (tabla_afectada, descripcion)
    SELECT 
        'companyproducts',
        CONCAT('Producto con ID ', cp.product_id, ' no existe en products.')
    FROM companyproducts cp
    LEFT JOIN products p ON cp.product_id = p.id
    WHERE p.id IS NULL;

    INSERT INTO inconsistencias_fk (tabla_afectada, descripcion)
    SELECT 
        'favorites',
        CONCAT('Cliente con ID ', f.customer_id, ' no existe en customers.')
    FROM favorites f
    LEFT JOIN customers c ON f.customer_id = c.id
    WHERE c.id IS NULL;

    INSERT INTO inconsistencias_fk (tabla_afectada, descripcion)
    SELECT 
        'details_favorites',
        CONCAT('Producto con ID ', df.product_id, ' no existe en products.')
    FROM details_favorites df
    LEFT JOIN products p ON df.product_id = p.id
    WHERE p.id IS NULL;

    INSERT INTO inconsistencias_fk (tabla_afectada, descripcion)
    SELECT 
        'rates',
        CONCAT('Cliente con ID ', r.customer_id, ' no existe en customers.')
    FROM rates r
    LEFT JOIN customers c ON r.customer_id = c.id
    WHERE c.id IS NULL;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_validar_claves_foraneas
ON SCHEDULE EVERY 1 WEEK
STARTS CURRENT_DATE + INTERVAL 1 WEEK
DO
    CALL validar_claves_foraneas();
```

11. Eliminar calificaciones inválidas antiguas
```sql
DELIMITER $$

CREATE PROCEDURE eliminar_calificaciones_invalidas()
BEGIN
    DELETE FROM rates
    WHERE (rating IS NULL OR rating < 0)
      AND created_at < NOW() - INTERVAL 3 MONTH;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_eliminar_calificaciones_invalidas
ON SCHEDULE
    EVERY 1 MONTH
    STARTS CURRENT_DATE + INTERVAL 1 MONTH
DO
    CALL eliminar_calificaciones_invalidas();
```

12. Cambiar estado de encuestas inactivas automáticamente
```sql
DELIMITER $$

CREATE PROCEDURE inactivar_encuestas_antiguas()
BEGIN
    UPDATE polls
    SET isactive = FALSE
    WHERE isactive = TRUE
      AND id NOT IN (
          SELECT DISTINCT poll_id
          FROM rates
          WHERE daterating >= NOW() - INTERVAL 6 MONTH
      );
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_inactivar_encuestas_antiguas
ON SCHEDULE 
    EVERY 1 MONTH
    STARTS CURRENT_DATE + INTERVAL 1 MONTH
DO
    CALL inactivar_encuestas_antiguas();
```

13. Registrar auditorías de forma periódica
```sql
DELIMITER $$

CREATE PROCEDURE registrar_auditoria_diaria()
BEGIN
    INSERT INTO auditorias_diarias (
        fecha,
        total_productos,
        total_clientes,
        total_empresas,
        total_calificaciones
    )
    SELECT
        CURDATE(),
        (SELECT COUNT(id) FROM products),
        (SELECT COUNT(id) FROM customers),
        (SELECT COUNT(id) FROM companies),
        (SELECT COUNT(rating) FROM rates);
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_registrar_auditoria_diaria
ON SCHEDULE
    EVERY 1 DAY
    STARTS CURRENT_DATE + INTERVAL 1 DAY
DO
    CALL registrar_auditoria_diaria();

```

14. Notificar métricas de calidad a empresas
```sql
DELIMITER $$

CREATE PROCEDURE notificar_metricas_calidad()
BEGIN
    INSERT INTO notificaciones_empresa (
        company_id,
        product_id,
        promedio_calidad,
        fecha_envio
    )
    SELECT
        qp.company_id,
        qp.product_id,
        AVG(qp.rating) AS promedio_calidad,
        NOW()
    FROM quality_products qp
    GROUP BY qp.company_id, qp.product_id;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_notificar_metricas_calidad
ON SCHEDULE
    EVERY 1 WEEK
    STARTS CURRENT_DATE + INTERVAL (7 - WEEKDAY(CURRENT_DATE)) DAY
DO
    CALL notificar_metricas_calidad();
```

15. Recordar renovación de membresías
```sql
DELIMITER $$

CREATE PROCEDURE recordar_renovacion_membresias()
BEGIN
    INSERT INTO recordatorios_membresia (
        membership_id,
        period_id,
        mensaje,
        fecha_recordatorio
    )
    SELECT
        mp.membership_id,
        mp.period_id,
        CONCAT('Tu membresía vencerá el ', DATE_FORMAT(mp.end_date, '%Y-%m-%d'), '. ¡Renuévala a tiempo!'),
        NOW()
    FROM membershipperiods mp
    WHERE mp.end_date BETWEEN CURDATE() AND CURDATE() + INTERVAL 7 DAY;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_recordar_renovacion_membresias
ON SCHEDULE
    EVERY 1 DAY
    STARTS CURRENT_DATE + INTERVAL 1 DAY
DO
    CALL recordar_renovacion_membresias();
```

16. Reordenar estadísticas generales cada semana
```sql
DELIMITER $$

CREATE PROCEDURE actualizar_estadisticas()
BEGIN
    INSERT INTO estadisticas (
        fecha,
        total_productos,
        total_empresas,
        total_clientes,
        total_membresias_activas
    )
    SELECT
        NOW(),
        (SELECT COUNT(p.id) FROM products p),
        (SELECT COUNT(c.id) FROM companies c),
        (SELECT COUNT(cu.id) FROM customers cu),
        (SELECT COUNT(mp.membership_id) 
         FROM membershipperiods mp 
         WHERE mp.status = 'ACTIVA');
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_actualizar_estadisticas
ON SCHEDULE
    EVERY 1 WEEK
    STARTS CURRENT_DATE + INTERVAL 1 WEEK
DO
    CALL actualizar_estadisticas();
```

17. Crear resúmenes temporales de uso por categoría
```sql
DELIMITER $$

CREATE PROCEDURE generar_resumen_uso_por_categoria()
BEGIN
    INSERT INTO resumen_uso_categorias (
        categoria_id,
        nombre_categoria,
        cantidad_calificados,
        fecha
    )
    SELECT
        c.id,
        c.description,
        COUNT(qp.product_id),
        NOW()
    FROM quality_products qp
    JOIN products p ON qp.product_id = p.id
    JOIN categories c ON p.category_id = c.id
    GROUP BY c.id, c.description;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_resumen_uso_por_categoria
ON SCHEDULE
    EVERY 1 WEEK
    STARTS CURRENT_DATE + INTERVAL 1 WEEK
DO
    CALL generar_resumen_uso_por_categoria();
```

18. Actualizar beneficios caducados
```sql
DELIMITER $$

CREATE PROCEDURE actualizar_beneficios_caducados()
BEGIN
    UPDATE benefits
    SET is_active = FALSE
    WHERE expires_at IS NOT NULL
      AND expires_at < CURDATE()
      AND is_active = TRUE;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_actualizar_beneficios_caducados
ON SCHEDULE
    EVERY 1 DAY
    STARTS CURRENT_DATE + INTERVAL 1 DAY
DO
    CALL actualizar_beneficios_caducados();
```

19. Alertar productos sin evaluación anual
```sql
DELIMITER $$

CREATE PROCEDURE alertar_productos_sin_evaluacion_anual()
BEGIN
    INSERT INTO alertas_productos (product_id, mensaje, fecha_alerta)
    SELECT p.id,
           CONCAT('El producto "', p.name, '" no ha sido evaluado en el último año.') AS mensaje,
           NOW() AS fecha_alerta
    FROM products p
    WHERE NOT EXISTS (
        SELECT q.product_id
        FROM quality_products q
        WHERE q.product_id = p.id
          AND q.daterating >= CURDATE() - INTERVAL 365 DAY
    );
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_alertar_productos_sin_evaluacion_anual
ON SCHEDULE
    EVERY 1 DAY
    STARTS CURRENT_DATE + INTERVAL 1 DAY
DO
    CALL alertar_productos_sin_evaluacion_anual();
```

20. Actualizar precios con índice externo
```sql
DELIMITER $$

CREATE PROCEDURE actualizar_precios_con_indice()
BEGIN
    DECLARE indice DOUBLE;

    SELECT valor INTO indice
    FROM inflacion_indice
    ORDER BY fecha_aplicacion DESC
    LIMIT 1;

    UPDATE companyproducts
    SET price = price * indice;
END$$

DELIMITER ;

CREATE EVENT IF NOT EXISTS evt_actualizar_precios_con_indice
ON SCHEDULE
    EVERY 1 MONTH
    STARTS CURRENT_DATE + INTERVAL 1 MONTH
DO
    CALL actualizar_precios_con_indice();
```

## 7. Historias de Usuario con JOINs

1. Ver productos con la empresa que los vende
```sql
SELECT 
    co.name AS empresa,
    p.name AS producto,
    cp.price AS precio
FROM companyproducts cp
INNER JOIN companies co ON cp.company_id = co.id
INNER JOIN products p ON cp.product_id = p.id;

```

2. Mostrar productos favoritos con su empresa y categoría
```sql
SELECT 
    p.name AS producto,
    cat.description AS categoria,
    c.name AS empresa
FROM favorites f
JOIN details_favorites df ON f.id = df.favorite_id
JOIN products p ON df.product_id = p.id
JOIN categories cat ON p.category_id = cat.id
JOIN companyproducts cp ON cp.product_id = p.id
JOIN companies c ON cp.company_id = c.id;
```

3. Ver empresas aunque no tengan productos
```sql
SELECT 
    C.name AS empresa
FROM companies c
LEFT JOIN companyproducts cp ON c.id = cp.company_id
GROUP BY c.name
HAVING COUNT(cp.product_id) = 0;
```

4. Ver productos que fueron calificados (o no)
```sql
SELECT 
    p.name AS producto,
    r.rating
FROM quality_products r
RIGHT JOIN products p ON p.id = r.product_id;
```

5. Ver productos con promedio de calificación y empresa
```sql
SELECT 
    c.name AS empresa,
    p.name AS producto,
    AVG(qp.rating) AS promedio
FROM products p
JOIN companyproducts cp ON p.id = cp.product_id
JOIN companies c ON cp.company_id = c.id
JOIN quality_products qp ON qp.product_id = p.id AND qp.company_id = c.id
GROUP BY c.name, p.name;

```

6. Ver clientes y sus calificaciones (si las tienen)
```sql
SELECT 
    cl.name AS cliente,
    r.rating AS calificacion
FROM customers cl
LEFT JOIN rates r ON cl.id = r.customer_id;
```

7. Ver favoritos con la última calificación del cliente
```sql
SELECT
    c.name AS nombre_cliente,
    prod.name AS nombre_producto_fav,
    comp.name AS empresa,
    cat.description AS categoria,
    MAX(r.daterating) AS ultima_calificacion_fecha,
    r.rating AS calificacion
FROM customers c
JOIN favorites fav ON c.id = fav.customer_id
JOIN details_favorites df ON fav.id = df.favorite_id
JOIN products prod ON df.product_id = prod.id
JOIN companyproducts cp ON prod.id = cp.product_id
JOIN companies comp ON cp.company_id = comp.id
JOIN categories cat ON prod.category_id = cat.id
LEFT JOIN rates r ON c.id = r.customer_id AND comp.id = r.company_id
WHERE fav.customer_id = 1
GROUP BY fav.id, prod.id, comp.id, r.rating
LIMIT 1;
```

8. Ver beneficios incluidos en cada plan de membresía
```sql
SELECT
    m.name AS membresia,
    m.description AS descripcion,
    b.description AS beneficions,
    b.detail AS detalles
FROM membershipbenefits msb
JOIN memberships m ON m.id = msb.membership_id
JOIN benefits b ON b.id = msb.benefit_id;
```

9. Ver clientes con membresía activa y sus beneficios
```sql
SELECT
    c.name AS customer_name,
    m.name AS membership_name,
    p.name AS period_name,
    b.description AS benefit_description,
    b.detail AS benefit_detail
FROM customers c
JOIN audiences a ON c.audience_id = a.id
JOIN membershipbenefits mb ON mb.audience_id = a.id
JOIN memberships m ON mb.membership_id = m.id
JOIN periods p ON mb.period_id = p.id
JOIN benefits b ON mb.benefit_id = b.id
WHERE c.membership_active = TRUE;
```

10. Ver ciudades con cantidad de empresas
```sql
SELECT
    cm.name AS Ciudad_Municipio,
    COUNT(c.id) AS Cantidad_Empresas
FROM citiesormunicipalities cm
JOIN companies c ON cm.code = c.city_id
GROUP BY(cm.code);
```

11. Ver encuestas con calificaciones
```sql
SELECT
    p.name AS encuesta,
    p.description AS descripcion,
    r.rating AS calificacion
FROM polls p
JOIN rates r ON p.id = r.poll_id;
```

12. Ver productos evaluados con datos del cliente
```sql
SELECT 
    p.name AS producto,
    c.name AS cliente,
    qp.daterating AS fecha_evaluacion,
    qp.rating AS calificacion
FROM quality_products qp
JOIN products p ON qp.product_id = p.id
JOIN customers c ON qp.customer_id = c.id;
```

13. Ver productos con audiencia de la empresa
```sql
SELECT 
    p.name AS producto,
    c.name AS empresa,
    a.description AS audiencia_objetivo
FROM products p
JOIN companyproducts cp ON p.id = cp.product_id
JOIN companies c ON cp.company_id = c.id
JOIN audiences a ON c.audience_id = a.id;
```

14. Ver clientes con sus productos favoritos
```sql
SELECT 
    c.name AS cliente,
    p.name AS producto_favorito
FROM customers c
JOIN favorites f ON f.customer_id = c.id
JOIN details_favorites df ON df.favorite_id = f.id
JOIN products p ON df.product_id = p.id;
```

15. Ver planes, periodos, precios y beneficios
```sql
SELECT
    m.name AS membresia,
    p.name AS periodo,
    b.description AS beneficio,
    b.detail AS detalle_beneficio
FROM membershipbenefits msb
JOIN memberships m ON m.id = msb.membership_id
JOIN benefits b ON b.id = msb.benefit_id
JOIN periods p ON p.id = msb.period_id;
```

16. Ver combinaciones empresa-producto-cliente calificados
```sql
SELECT 
    cu.name AS cliente,
    p.name AS producto,
    c.name AS empresa,
    qp.rating AS calificacion
FROM quality_products qp
JOIN products p ON qp.product_id = p.id
JOIN companies c ON qp.company_id = c.id
JOIN customers cu ON qp.customer_id = cu.id;
```

17. Comparar favoritos con productos calificados
```sql
SELECT
    f.customer_id,
    p.name AS producto,
    qp.rating AS calificacion,
    qp.daterating AS fecha
FROM favorites f
JOIN details_favorites df ON f.id = df.favorite_id
JOIN products p ON df.product_id = p.id
JOIN quality_products qp ON qp.product_id = p.id AND qp.customer_id = f.customer_id
WHERE f.customer_id = 1
ORDER BY fecha DESC;

```

18. Ver productos ordenados por categoría
```sql
SELECT
    p.name AS producto,
    c.description AS categoria
FROM products p
JOIN categories c ON c.id = category_id;
```

19. Ver beneficios por audiencia, incluso vacíos
```sql
SELECT 
    a.description AS audiencia,
    b.description AS beneficio,
    b.detail AS detalle
FROM audiences a
LEFT JOIN audiencebenefits ab ON a.id = ab.audience_id
LEFT JOIN benefits b ON ab.benefit_id = b.id;
```

20. Ver datos cruzados entre calificaciones, encuestas, productos y clientes
```sql
SELECT 
    cu.name AS cliente,
    p.name AS producto,
    c.name AS empresa,
    poll.name AS encuesta,
    qp.rating AS calificacion,
    qp.daterating AS fecha
FROM quality_products qp
JOIN products p ON qp.product_id = p.id
JOIN polls poll ON qp.poll_id = poll.id
JOIN companies c ON qp.company_id = c.id
JOIN customers cu ON qp.customer_id = cu.id;

```

## 8. Historias de Usuario con Funciones Definidas por el Usuario (UDF)

1. Como analista, quiero una función que calcule el promedio ponderado de calidad de un producto basado en sus calificaciones y fecha de evaluación.
```sql
DELIMITER $$

CREATE FUNCTION calcular_promedio_ponderado(p_product_id INT)
RETURNS DOUBLE
DETERMINISTIC
BEGIN
  DECLARE promedio DOUBLE DEFAULT 0;

  SELECT 
    IFNULL(
      SUM(rating * (1 / (DATEDIFF(NOW(), daterating) + 1))) / 
      SUM(1 / (DATEDIFF(NOW(), daterating) + 1)),
    0)
  INTO promedio
  FROM quality_products
  WHERE product_id = p_product_id;

  RETURN promedio;
END$$

DELIMITER ;
```

2. Como auditor, deseo una función que determine si un producto ha sido calificado recientemente (últimos 30 días).
```sql
DELIMITER $$

CREATE FUNCTION es_calificacion_reciente(p_fecha DATETIME)
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
  RETURN p_fecha >= (NOW() - INTERVAL 30 DAY);
END$$

DELIMITER ;
```

3. Como desarrollador, quiero una función que reciba un product_id y devuelva el nombre completo de la empresa que lo vende.
```sql
DELIMITER $$

CREATE FUNCTION obtener_empresa_producto(p_product_id INT)
RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
  DECLARE v_nombre_empresa VARCHAR(255);

  SELECT c.name
  INTO v_nombre_empresa
  FROM companyproducts cp
  JOIN companies c ON cp.company_id = c.id
  WHERE cp.product_id = p_product_id
  LIMIT 1;

  RETURN v_nombre_empresa;
END$$

DELIMITER ;
```

4. Como operador, deseo una función que, dado un customer_id, me indique si el cliente tiene una membresía activa.
```sql
DELIMITER $$

CREATE FUNCTION tiene_membresia_activa(p_customer_id INT)
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
  DECLARE v_count INT;

  SELECT COUNT(*)
  INTO v_count
  FROM membershipperiods mp
  JOIN customers_memberships cm ON cm.membership_id = mp.membership_id
  WHERE cm.customer_id = p_customer_id
    AND CURDATE() BETWEEN mp.start_date AND mp.end_date
    AND mp.status = 'ACTIVA';

  RETURN v_count > 0;
END$$

DELIMITER ;
```

5. Como administrador, quiero una función que valide si una ciudad tiene más de X empresas registradas, recibiendo la ciudad y el número como parámetros.
```sql
DELIMITER $$

CREATE FUNCTION ciudad_supera_empresas(p_city_id VARCHAR(7), p_limite INT)
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
  DECLARE v_total INT;

  SELECT COUNT(*)
  INTO v_total
  FROM companies
  WHERE city_id = p_city_id;

  RETURN v_total > p_limite;
END$$

DELIMITER ;
```

6. Como gerente, deseo una función que, dado un rate_id, me devuelva una descripción textual de la calificación (por ejemplo, “Muy bueno”, “Regular”).
```sql
DELIMITER $$

CREATE FUNCTION descripcion_calificacion(p_valor DOUBLE)
RETURNS VARCHAR(50)
DETERMINISTIC
BEGIN
  DECLARE v_desc VARCHAR(50);

  SET v_desc = CASE
    WHEN p_valor = 5 THEN 'Excelente'
    WHEN p_valor = 4 THEN 'Muy bueno'
    WHEN p_valor = 3 THEN 'Bueno'
    WHEN p_valor = 2 THEN 'Regular'
    WHEN p_valor = 1 THEN 'Malo'
    ELSE 'Sin calificar'
  END;

  RETURN v_desc;
END$$

DELIMITER ;
```

7. Como técnico, quiero una función que devuelva el estado de un producto en función de su evaluación (ej. “Aceptable”, “Crítico”).
```sql
DELIMITER $$

CREATE FUNCTION estado_producto(p_product_id INT)
RETURNS VARCHAR(20)
DETERMINISTIC
BEGIN
  DECLARE v_avg DOUBLE;
  DECLARE v_estado VARCHAR(20);

  SELECT AVG(rating)
  INTO v_avg
  FROM rates
  WHERE customer_id IS NOT NULL
    AND poll_id IS NOT NULL
    AND EXISTS (
      SELECT 1
      FROM companyproducts cp
      WHERE cp.product_id = p_product_id
    )
    AND EXISTS (
      SELECT 1
      FROM products p
      WHERE p.id = p_product_id
    )
    AND EXISTS (
      SELECT 1
      FROM quality_products qp
      WHERE qp.product_id = p_product_id
    )
    AND product_id = p_product_id;

  SET v_estado = CASE
    WHEN v_avg IS NULL THEN 'Sin datos'
    WHEN v_avg < 2.5 THEN 'Crítico'
    WHEN v_avg >= 2.5 AND v_avg < 4 THEN 'Aceptable'
    ELSE 'Óptimo'
  END;

  RETURN v_estado;
END$$

DELIMITER ;
```

8. Como cliente, deseo una función que indique si un producto está entre mis favoritos, recibiendo el product_id y mi customer_id.
```sql
DELIMITER $$

CREATE FUNCTION es_favorito(p_customer_id INT, p_product_id INT)
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
  DECLARE v_count INT;

  SELECT COUNT(*)
  INTO v_count
  FROM favorites f
  JOIN details_favorites df ON df.favorite_id = f.id
  WHERE f.customer_id = p_customer_id
    AND df.product_id = p_product_id;

  RETURN v_count > 0;
END$$

DELIMITER ;
```

9. Como gestor de beneficios, quiero una función que determine si un beneficio está asignado a una audiencia específica, retornando verdadero o falso.
```sql
DELIMITER $$

CREATE FUNCTION beneficio_asignado_audiencia(p_benefit_id INT, p_audience_id INT)
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
  DECLARE v_count INT;

  SELECT COUNT(*)
  INTO v_count
  FROM audiencebenefits
  WHERE benefit_id = p_benefit_id
    AND audience_id = p_audience_id;

  RETURN v_count > 0;
END$$

DELIMITER ;
```

10. Como auditor, deseo una función que reciba una fecha y determine si se encuentra dentro de un rango de membresía activa.
```sql
DELIMITER $$

CREATE FUNCTION fecha_en_membresia(p_fecha DATE, p_customer_id INT)
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
  DECLARE v_count INT;

  SELECT COUNT(*)
  INTO v_count
  FROM membershipperiods mp
  JOIN customers_memberships cm ON cm.membership_id = mp.membership_id
  WHERE cm.customer_id = p_customer_id
    AND p_fecha BETWEEN mp.start_date AND mp.end_date
    AND mp.status = 'ACTIVA';

  RETURN v_count > 0;
END$$

DELIMITER ;
```

11. Como desarrollador, quiero una función que calcule el porcentaje de calificaciones positivas de un producto respecto al total.
```sql
DELIMITER $$

CREATE FUNCTION porcentaje_positivas(p_product_id INT)
RETURNS DECIMAL(5,2)
DETERMINISTIC
BEGIN
  DECLARE total INT DEFAULT 0;
  DECLARE positivas INT DEFAULT 0;
  DECLARE resultado DECIMAL(5,2);

  SELECT COUNT(*) INTO total
  FROM rates
  WHERE poll_id IN (
    SELECT poll_id FROM quality_products
    WHERE product_id = p_product_id
  );

  SELECT COUNT(*) INTO positivas
  FROM rates
  WHERE poll_id IN (
    SELECT poll_id FROM quality_products
    WHERE product_id = p_product_id
  )
  AND rating >= 4;

  IF total = 0 THEN
    SET resultado = 0;
  ELSE
    SET resultado = (positivas * 100.0) / total;
  END IF;

  RETURN resultado;
END$$

DELIMITER ;
```

12. Como supervisor, deseo una función que calcule la edad de una calificación, en días, desde la fecha actual.
```sql
DELIMITER $$

CREATE FUNCTION edad_calificacion(
  p_customer_id INT,
  p_company_id VARCHAR(20),
  p_poll_id INT
)
RETURNS INT
DETERMINISTIC
BEGIN
  DECLARE v_dias INT DEFAULT NULL;
  DECLARE v_fecha DATETIME;

  SELECT created_at INTO v_fecha
  FROM rates
  WHERE customer_id = p_customer_id
    AND company_id = p_company_id
    AND poll_id = p_poll_id
  LIMIT 1;

  IF v_fecha IS NOT NULL THEN
    SET v_dias = DATEDIFF(CURRENT_DATE, v_fecha);
  ELSE
    SET v_dias = NULL; -- o 0, si prefieres
  END IF;

  RETURN v_dias;
END$$

DELIMITER ;
```

13. Como operador, quiero una función que, dado un company_id, devuelva la cantidad de productos únicos asociados a esa empresa.
```sql
DELIMITER $$

CREATE FUNCTION productos_por_empresa(p_company_id VARCHAR(20))
RETURNS INT
DETERMINISTIC
BEGIN
  DECLARE v_total INT DEFAULT 0;

  SELECT COUNT(DISTINCT product_id)
  INTO v_total
  FROM companyproducts
  WHERE company_id = p_company_id;

  RETURN v_total;
END$$

DELIMITER ;
```

14. Como gerente, deseo una función que retorne el nivel de actividad de un cliente (frecuente, esporádico, inactivo), según su número de calificaciones.
```sql
DELIMITER $$

CREATE FUNCTION nivel_actividad_cliente(p_customer_id INT)
RETURNS VARCHAR(20)
DETERMINISTIC
BEGIN
  DECLARE v_count INT DEFAULT 0;
  DECLARE v_nivel VARCHAR(20);

  SELECT COUNT(*)
  INTO v_count
  FROM rates
  WHERE customer_id = p_customer_id;

  IF v_count >= 10 THEN
    SET v_nivel = 'Frecuente';
  ELSEIF v_count >= 1 THEN
    SET v_nivel = 'Esporádico';
  ELSE
    SET v_nivel = 'Inactivo';
  END IF;

  RETURN v_nivel;
END$$

DELIMITER ;
```

15. Como administrador, quiero una función que calcule el precio promedio ponderado de un producto, tomando en cuenta su uso en favoritos.
```sql
DELIMITER $$

CREATE FUNCTION precio_promedio_ponderado(p_product_id INT)
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
  DECLARE v_total_favoritos INT DEFAULT 0;
  DECLARE v_suma_ponderada DECIMAL(20,2) DEFAULT 0;
  DECLARE v_precio_promedio DECIMAL(10,2);

  SELECT COUNT(*)
  INTO v_total_favoritos
  FROM details_favorites
  WHERE product_id = p_product_id;

  IF v_total_favoritos = 0 THEN
    SELECT AVG(price) INTO v_precio_promedio
    FROM companyproducts
    WHERE product_id = p_product_id;
  ELSE
    SELECT SUM(cp.price * fav_count.cant)
    INTO v_suma_ponderada
    FROM companyproducts cp
    JOIN (
      SELECT product_id, COUNT(*) AS cant
      FROM details_favorites
      WHERE product_id = p_product_id
      GROUP BY product_id
    ) fav_count ON cp.product_id = fav_count.product_id;

    SET v_precio_promedio = v_suma_ponderada / v_total_favoritos;
  END IF;

  RETURN ROUND(v_precio_promedio, 2);
END$$

DELIMITER ;
```

16. Como técnico, deseo una función que me indique si un benefit_id está asignado a más de una audiencia o membresía (valor booleano).
```sql
DELIMITER $$

CREATE FUNCTION beneficio_asignado_multiple(p_benefit_id INT)
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
  DECLARE v_audiencias INT DEFAULT 0;
  DECLARE v_membresias INT DEFAULT 0;

  SELECT COUNT(DISTINCT audience_id)
  INTO v_audiencias
  FROM audiencebenefits
  WHERE benefit_id = p_benefit_id;

  SELECT COUNT(DISTINCT membership_id)
  INTO v_membresias
  FROM membershipbenefits
  WHERE benefit_id = p_benefit_id;

  IF v_audiencias > 1 OR v_membresias > 1 THEN
    RETURN TRUE;
  ELSE
    RETURN FALSE;
  END IF;
END$$

DELIMITER ;
```

17. Como cliente, quiero una función que, dada mi ciudad, retorne un índice de variedad basado en número de empresas y productos.
```sql
DELIMITER $$

CREATE FUNCTION indice_variedad_ciudad(p_city_id VARCHAR(7))
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
  DECLARE v_empresas INT DEFAULT 0;
  DECLARE v_productos INT DEFAULT 0;
  DECLARE v_indice DECIMAL(10,2);

  SELECT COUNT(*)
  INTO v_empresas
  FROM companies
  WHERE city_id = p_city_id;

  SELECT COUNT(DISTINCT cp.product_id)
  INTO v_productos
  FROM companyproducts cp
  JOIN companies c ON c.id = cp.company_id
  WHERE c.city_id = p_city_id;

  SET v_indice = (v_empresas + v_productos) / 2;

  RETURN ROUND(v_indice, 2);
END$$

DELIMITER ;
```

18. Como gestor de calidad, deseo una función que evalúe si un producto debe ser desactivado por tener baja calificación histórica.
```sql
DELIMITER $$

CREATE FUNCTION producto_baja_calificacion(p_product_id INT, p_umbral DECIMAL(3,2))
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
  DECLARE v_promedio DECIMAL(3,2);

  SELECT AVG(r.rating)
  INTO v_promedio
  FROM rates r
  JOIN companyproducts cp ON cp.product_id = p_product_id AND cp.company_id = r.company_id
  WHERE r.poll_id IS NOT NULL -- opcional: filtrar por encuesta válida

  GROUP BY r.product_id;

  IF v_promedio IS NULL THEN
    RETURN FALSE;
  END IF;

  RETURN v_promedio < p_umbral;
END$$

DELIMITER ;
```

19. Como desarrollador, quiero una función que calcule el índice de popularidad de un producto (combinando favoritos y ratings).
```sql
DELIMITER $$

CREATE FUNCTION indice_popularidad_producto(p_product_id INT)
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
  DECLARE v_fav_count INT DEFAULT 0;
  DECLARE v_rating_avg DECIMAL(3,2) DEFAULT 0.0;
  DECLARE v_popularity DECIMAL(10,2);

  -- Contar veces que fue favorito
  SELECT COUNT(*)
  INTO v_fav_count
  FROM details_favorites
  WHERE product_id = p_product_id;

  -- Promedio de calificaciones
  SELECT AVG(r.rating)
  INTO v_rating_avg
  FROM rates r
  JOIN companyproducts cp ON r.company_id = cp.company_id AND r.poll_id = r.poll_id
  WHERE cp.product_id = p_product_id;

  SET v_popularity = (0.4 * LEAST(v_fav_count / 100, 1)) + (0.6 * (v_rating_avg / 5));

  RETURN ROUND(v_popularity * 100, 2); -- escala 0 a 100
END$$

DELIMITER ;
```

20. Como auditor, deseo una función que genere un código único basado en el nombre del producto y su fecha de creación.
```sql
DELIMITER $$

CREATE FUNCTION generar_codigo_unico(producto_id INT)
RETURNS VARCHAR(64)
DETERMINISTIC
BEGIN
  DECLARE v_nombre VARCHAR(60);
  DECLARE v_fecha DATE;
  DECLARE v_codigo VARCHAR(64);

  SELECT name, created_at
  INTO v_nombre, v_fecha
  FROM products
  WHERE id = producto_id;

  SET @fecha_str = DATE_FORMAT(v_fecha, '%Y%m%d');

  SET @base_str = CONCAT(v_nombre, '_', @fecha_str);

  SET v_codigo = MD5(@base_str);

  RETURN v_codigo;
END$$

DELIMITER ;
```
