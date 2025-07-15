# Ejercicios

## 1. Consultas SQL Especializadas

- 1. Como analista, quiero listar todos los productos con su empresa asociada y el precio más bajo por ciudad.
```sql
```

- 2. Como administrador, deseo obtener el top 5 de clientes que más productos han calificado en los últimos 6 meses.
```sql
```

- 3. Como gerente de ventas, quiero ver la distribución de productos por categoría y unidad de medida.
```sql
```

- 4. Como cliente, quiero saber qué productos tienen calificaciones superiores al promedio general.
```sql
```
- 5. Como auditor, quiero conocer todas las empresas que no han recibido ninguna calificación.
```sql
```

- 6. Como operador, deseo obtener los productos que han sido añadidos como favoritos por más de 10 clientes distintos.
```sql
```

- 7. Como gerente regional, quiero obtener todas las empresas activas por ciudad y categoría.
```sql
```

- 8. Como especialista en marketing, deseo obtener los 10 productos más calificados en cada ciudad.
```sql
```

- 9. Como técnico, quiero identificar productos sin unidad de medida asignada.
```sql
```

- 10. Como gestor de beneficios, deseo ver los planes de membresía sin beneficios registrados.
```sql
```

- 11. Como supervisor, quiero obtener los productos de una categoría específica con su promedio de calificación.
```sql
```

- 12. Como asesor, deseo obtener los clientes que han comprado productos de más de una empresa.
```sql
```

- 13. Como director, quiero identificar las ciudades con más clientes activos.
```sql
```

- 14. Como analista de calidad, deseo obtener el ranking de productos por empresa basado en la media de quality_products.
```sql
```

- 15. Como administrador, quiero listar empresas que ofrecen más de cinco productos distintos.
```sql
```

- 16. Como cliente, deseo visualizar los productos favoritos que aún no han sido calificados.
```sql
```

- 17. Como desarrollador, deseo consultar los beneficios asignados a cada audiencia junto con su descripción.
```sql
```

- 18. Como operador logístico, quiero saber en qué ciudades hay empresas sin productos asociados.
```sql
```

- 19. Como técnico, deseo obtener todas las empresas con productos duplicados por nombre.
```sql
```

- 20. Como analista, quiero una vista resumen de clientes, productos favoritos y promedio de calificación recibido.
```sql
```

## 2. Subconsultas

- 1. Como gerente, quiero ver los productos cuyo precio esté por encima del promedio de su categoría.
```sql
```

- 2. Como administrador, deseo listar las empresas que tienen más productos que la media de empresas.
```sql
```

- 3. Como cliente, quiero ver mis productos favoritos que han sido calificados por otros clientes.
```sql
```

- 4. Como supervisor, deseo obtener los productos con el mayor número de veces añadidos como favoritos.
```sql
```
- 5. Como técnico, quiero listar los clientes cuyo correo no aparece en la tabla rates ni en quality_products.
```sql
```

- 6. Como gestor de calidad, quiero obtener los productos con una calificación inferior al mínimo de su categoría.
```sql
```

- 7. Como desarrollador, deseo listar las ciudades que no tienen clientes registrados.
```sql
```

- 8. Como administrador, quiero ver los productos que no han sido evaluados en ninguna encuesta.
```sql
```

- 9. Como auditor, quiero listar los beneficios que no están asignados a ninguna audiencia.
```sql
```

- 10. Como cliente, deseo obtener mis productos favoritos que no están disponibles actualmente en ninguna empresa.
```sql
```

- 11. Como director, deseo consultar los productos vendidos en empresas cuya ciudad tenga menos de tres empresas registradas.
```sql
```

- 12. Como analista, quiero ver los productos con calidad superior al promedio de todos los productos.
```sql
```

- 13. Como gestor, quiero ver empresas que sólo venden productos de una única categoría.
```sql
```

- 14. Como gerente comercial, quiero consultar los productos con el mayor precio entre todas las empresas.
```sql
```

- 15. Como cliente, quiero saber si algún producto de mis favoritos ha sido calificado por otro cliente con más de 4 estrellas.
```sql
```

- 16. Como operador, quiero saber qué productos no tienen imagen asignada pero sí han sido calificados.
```sql
```

- 17. Como auditor, quiero ver los planes de membresía sin periodo vigente.
```sql
```

- 18. Como especialista, quiero identificar los beneficios compartidos por más de una audiencia.
```sql
```

- 19. Como técnico, quiero encontrar empresas cuyos productos no tengan unidad de medida definida.
```sql
```

- 20. Como gestor de campañas, deseo obtener los clientes con membresía activa y sin productos favoritos.
```sql
```

## 3. Funciones Agregadas

- 1. Obtener el promedio de calificación por producto
```sql
```

- 2. Contar cuántos productos ha calificado cada cliente
```sql
```

- 3. Sumar el total de beneficios asignados por audiencia
```sql
```

- 4. Calcular la media de productos por empresa
```sql
```
- 5. Contar el total de empresas por ciudad
```sql
```

- 6. Calcular el promedio de precios por unidad de medida
```sql
```

- 7. Contar cuántos clientes hay por ciudad
```sql
```

- 8. Calcular planes de membresía por periodo
```sql
```

- 9. Ver el promedio de calificaciones dadas por un cliente a sus favoritos
```sql
```

- 10. Consultar la fecha más reciente en que se calificó un producto
```sql
```

- 11. Obtener la desviación estándar de precios por categoría
```sql
```

- 12. Contar cuántas veces un producto fue favorito
```sql
```

- 13. Calcular el porcentaje de productos evaluados
```sql
```

- 14. Ver el promedio de rating por encuesta
```sql
```

- 15. Calcular el promedio y total de beneficios por plan
```sql
```

- 16. Obtener media y varianza de precios por empresa
```sql
```

- 17. Ver total de productos disponibles en la ciudad del cliente
```sql
```

- 18. Contar productos únicos por tipo de empresa
```sql
```

- 19. Ver total de clientes sin correo electrónico registrado
```sql
```

- 20. Empresa con más productos calificados
```sql
```

## 4. Procedimientos Almacenados

- 1.  Registrar una nueva calificación y actualizar el promedio
```sql
```

- 2. Insertar empresa y asociar productos por defecto
```sql
```

- 3. Añadir producto favorito validando duplicados
```sql
```

- 4. Generar resumen mensual de calificaciones por empresa
```sql
```
- 5. Calcular beneficios activos por membresía
```sql
```

- 6. Eliminar productos huérfanos
```sql
```

- 7. Actualizar precios de productos por categoría
```sql
```

- 8. Validar inconsistencia entre rates y quality_products
```sql
```

- 9. Asignar beneficios a nuevas audiencias
```sql
```

- 10. Activar planes de membresía vencidos con pago confirmado
```sql
```

- 11. Listar productos favoritos del cliente con su calificación
```sql
```

- 12. Registrar encuesta y sus preguntas asociadas
```sql
```

- 13. Eliminar favoritos antiguos sin calificaciones
```sql
```

- 14. Asociar beneficios automáticamente por audiencia
```sql
```

- 15. Historial de cambios de precio
```sql
```

- 16. Registrar encuesta activa automáticamente
```sql
```

- 17. Actualizar unidad de medida de productos sin afectar ventas
```sql
```

- 18. Recalcular promedios de calidad semanalmente
```sql
```

- 19. Validar claves foráneas entre calificaciones y encuestas
```sql
```

- 20. Generar el top 10 de productos más calificados por ciudad
```sql
```

## 5. Triggers

- 1. Actualizar la fecha de modificación de un producto
```sql
```

- 2. Registrar log cuando un cliente califica un producto
```sql
```

- 3. Impedir insertar productos sin unidad de medida
```sql
```

- 4. Validar calificaciones no mayores a 5
```sql
```
- 5. Actualizar estado de membresía cuando vence
```sql
```

- 6. Evitar duplicados de productos por empresa
```sql
```

- 7. Enviar notificación al añadir un favorito
```sql
```

- 8. Insertar fila en quality_products tras calificación
```sql
```

- 9. Eliminar favoritos si se elimina el producto
```sql
```

- 10. Bloquear modificación de audiencias activas
```sql
```

- 11. Recalcular promedio de calidad del producto tras nueva evaluación
```sql
```

- 12. Registrar asignación de nuevo beneficio
```sql
```

- 13. Impedir doble calificación por parte del cliente
```sql
```

- 14. Validar correos duplicados en clientes
```sql
```

- 15. Eliminar detalles de favoritos huérfanos
```sql
```

- 16. Actualizar campo updated_at en companies
```sql
```

- 17. Impedir borrar ciudad si hay empresas activas
```sql
```

- 18. Registrar cambios de estado en encuestas
```sql
```

- 19. Sincronizar rates y quality_products
```sql
```

- 20. Eliminar productos sin relación a empresas
```sql
```

## 6. Events

- 1. Borrar productos sin actividad cada 6 meses
```sql
```

- 2. Recalcular el promedio de calificaciones semanalmente
```sql
```

- 3. Actualizar precios según inflación mensual
```sql
```

- 4. Crear backups lógicos diariamente
```sql
```
- 5. Notificar sobre productos favoritos sin calificar
```sql
```

- 6. Revisar inconsistencias entre empresa y productos
```sql
```

- 7. Archivar membresías vencidas diariamente
```sql
```

- 8. Notificar beneficios nuevos a usuarios semanalmente
```sql
```

- 9. Calcular cantidad de favoritos por cliente mensualmente
```sql
```

- 10. Validar claves foráneas semanalmente
```sql
```

- 11. Eliminar calificaciones inválidas antiguas
```sql
```

- 12. Cambiar estado de encuestas inactivas automáticamente
```sql
```

- 13. Registrar auditorías de forma periódica
```sql
```

- 14. Notificar métricas de calidad a empresas
```sql
```

- 15. Recordar renovación de membresías
```sql
```

- 16. Reordenar estadísticas generales cada semana
```sql
```

- 17. Crear resúmenes temporales de uso por categoría
```sql
```

- 18. Actualizar beneficios caducados
```sql
```

- 19. Alertar productos sin evaluación anual
```sql
```

- 20. Actualizar precios con índice externo
```sql
```

## 7. Historias de Usuario con JOINs

- 1. Ver productos con la empresa que los vende
```sql
```

- 2. Mostrar productos favoritos con su empresa y categoría
```sql
```

- 3. Ver empresas aunque no tengan productos
```sql
```

- 4. Ver productos que fueron calificados (o no)
```sql
```
- 5. Ver productos con promedio de calificación y empresa
```sql
```

- 6. Ver clientes y sus calificaciones (si las tienen)
```sql
```

- 7. Ver favoritos con la última calificación del cliente
```sql
```

- 8. Ver beneficios incluidos en cada plan de membresía
```sql
```

- 9. Ver clientes con membresía activa y sus beneficios
```sql
```

- 10. Ver ciudades con cantidad de empresas
```sql
```

- 11. Ver encuestas con calificaciones
```sql
```

- 12. Ver productos evaluados con datos del cliente
```sql
```

- 13. Ver productos con audiencia de la empresa
```sql
```

- 14. Ver clientes con sus productos favoritos
```sql
```

- 15. Ver planes, periodos, precios y beneficios
```sql
```

- 16. Ver combinaciones empresa-producto-cliente calificados
```sql
```

- 17. Comparar favoritos con productos calificados
```sql
```

- 18. Ver productos ordenados por categoría
```sql
```

- 19. Ver beneficios por audiencia, incluso vacíos
```sql
```

- 20. Ver datos cruzados entre calificaciones, encuestas, productos y clientes
```sql
```

## 8. Historias de Usuario con Funciones Definidas por el Usuario (UDF)

- 1. Como analista, quiero una función que calcule el promedio ponderado de calidad de un producto basado en sus calificaciones y fecha de evaluación.
```sql
```

- 2. Como auditor, deseo una función que determine si un producto ha sido calificado recientemente (últimos 30 días).
```sql
```

- 3. Como desarrollador, quiero una función que reciba un product_id y devuelva el nombre completo de la empresa que lo vende.
```sql
```

- 4. Como operador, deseo una función que, dado un customer_id, me indique si el cliente tiene una membresía activa.
```sql
```
- 5. Como administrador, quiero una función que valide si una ciudad tiene más de X empresas registradas, recibiendo la ciudad y el número como parámetros.
```sql
```

- 6. Como gerente, deseo una función que, dado un rate_id, me devuelva una descripción textual de la calificación (por ejemplo, “Muy bueno”, “Regular”).
```sql
```

- 7. Como técnico, quiero una función que devuelva el estado de un producto en función de su evaluación (ej. “Aceptable”, “Crítico”).
```sql
```

- 8. Como cliente, deseo una función que indique si un producto está entre mis favoritos, recibiendo el product_id y mi customer_id.
```sql
```

- 9. Como gestor de beneficios, quiero una función que determine si un beneficio está asignado a una audiencia específica, retornando verdadero o falso.
```sql
```

- 10. Como auditor, deseo una función que reciba una fecha y determine si se encuentra dentro de un rango de membresía activa.
```sql
```

- 11. Como desarrollador, quiero una función que calcule el porcentaje de calificaciones positivas de un producto respecto al total.
```sql
```

- 12. Como supervisor, deseo una función que calcule la edad de una calificación, en días, desde la fecha actual.
```sql
```

- 13. Como operador, quiero una función que, dado un company_id, devuelva la cantidad de productos únicos asociados a esa empresa.
```sql
```

- 14. Como gerente, deseo una función que retorne el nivel de actividad de un cliente (frecuente, esporádico, inactivo), según su número de calificaciones.
```sql
```

- 15. Como administrador, quiero una función que calcule el precio promedio ponderado de un producto, tomando en cuenta su uso en favoritos.
```sql
```

- 16. Como técnico, deseo una función que me indique si un benefit_id está asignado a más de una audiencia o membresía (valor booleano).
```sql
```

- 17. Como cliente, quiero una función que, dada mi ciudad, retorne un índice de variedad basado en número de empresas y productos.
```sql
```

- 18. Como gestor de calidad, deseo una función que evalúe si un producto debe ser desactivado por tener baja calificación histórica.
```sql
```

- 19. Como desarrollador, quiero una función que calcule el índice de popularidad de un producto (combinando favoritos y ratings).
```sql
```

- 20. Como auditor, deseo una función que genere un código único basado en el nombre del producto y su fecha de creación.
```sql
```