# ProyectoMySQL-Fabio
## 📦 Plataforma de Comercialización Digital Multinivel

## 📘 Descripción General del Proyecto

Este proyecto consiste en el diseño e implementación de una base de datos relacional en **MySQL** para respaldar el funcionamiento de una **plataforma digital de comercialización de productos y servicios** ofrecidos por empresas registradas. El sistema abarca la gestión integral de:

- Clientes
- Empresas
- Productos
- Evaluaciones
- Membresías
- Beneficios
- Ubicación geográfica

El modelo es **escalable**, **modular** y se basa en un esquema **normalizado**.

---

## 🧩 Justificación Técnica

La solución responde a la creciente necesidad de plataformas B2C y B2B con:

- Personalización de servicios
- Evaluación de calidad
- Segmentación de usuarios
- Fidelización mediante beneficios

El diseño sigue las **buenas prácticas** de modelado de datos, con énfasis en la **seguridad**, **integridad referencial** y posibilidades de **expansión futura**.

---

## 🎯 Objetivos del Sistema de Base de Datos

Desarrollar una base de datos que permita gestionar eficientemente:

- Información de clientes y empresas
- Catálogo de productos y servicios
- Georreferenciación de usuarios
- Preferencias, favoritos y segmentación
- Evaluaciones y encuestas de calidad
- Membresías y beneficios
- Métricas por audiencia y producto

---

## 🧱 Modelo de Datos y Estructura Relacional

### 🌍 Estructura Geográfica

- `countries`: Países
- `stateregions`: Departamentos o estados
- `citiesormunicipalities`: Ciudades o municipios

### 🏢 Gestión de Entidades

- `companies`: Empresas (ubicación, tipo, categoría, audiencia)
- `customers`: Clientes (datos personales, ubicación, historial)

### 🛒 Catálogo de Productos

- `products`: Descripción, precio, categoría, imagen
- `companyproducts`: Asociación empresa-producto, precios personalizados

### ⭐ Evaluaciones y Métricas

- `polls`: Encuestas configurables
- `rates`: Valoraciones de productos por clientes
- `quality_products`: Métricas de calidad avanzada

### ❤️ Personalización del Usuario

- `favorites` y `details_favorites`: Listas de productos favoritos
- `audiences`: Segmentación de usuarios

### 💳 Membresías y Beneficios

- `memberships`: Tipos de planes
- `membershipperiods`: Vigencia y costo
- `benefits`: Privilegios por membresía
- `audiencebenefits` y `membershipbenefits`: Asociación beneficios-usuario

---

## 🧮 Normalización e Integridad

La base de datos está normalizada hasta la **Tercera Forma Normal (3FN)**:

- Eliminación de redundancia
- Integridad semántica
- Eficiencia en consultas y actualizaciones

Todas las relaciones incluyen **claves foráneas (FOREIGN KEY)** con **InnoDB**, asegurando integridad referencial.

---

## ⚙️ Tecnologías y Herramientas

- **SGBD:** MySQL 8.x
- **Motor:** InnoDB
- **Interfaces sugeridas:** MySQL Workbench o DBeaver
- **Lenguaje:** SQL (estándar + extensiones MySQL)

---

## 🚀 Escalabilidad y Seguridad

El modelo soporta crecimiento horizontal mediante:

- Nuevas categorías, empresas, productos, zonas
- Nuevos planes de membresía

La seguridad está orientada a **roles** (a implementar desde la aplicación) y el esquema incluye:

- Claves únicas
- Restricciones de nulidad
- Longitudes controladas

---

## ✅ Conclusión

Este sistema relacional proporciona una **base sólida y moderna** para aplicaciones de comercialización digital. Es consistente, eficiente y extensible, ideal para conectar con APIs, dashboards o módulos analíticos. Está preparado para evolucionar hacia **arquitecturas distribuidas o basadas en microservicios**.

---

© Proyecto Académico - Base de Datos MySQL  
