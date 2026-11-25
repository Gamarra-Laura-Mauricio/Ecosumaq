# 🍽️ EcoSumaq V2 — Marketplace Gastronómico

**EcoSumaq V2** es una plataforma web tipo *marketplace* gastronómico diseñada para conectar a compradores con proveedores locales de productos alimenticios (tanto platos preparados como insumos de cocina) a través de un catálogo digital centralizado.

> **Contexto Académico:** Proyecto Integrador – Unidad III (*Desarrollo e integración de plataformas mediante APIs REST*)  
> **Asignatura:** Plataformas para el desarrollo de aplicaciones  
> **Ubicación:** Cusco – Perú | 2026

---

## 📋 Tabla de Contenidos
1. [Descripción del Problema y Solución](#-descripción-del-problema-y-solución)
2. [Arquitectura y Stack Tecnológico](#-arquitectura-y-stack-tecnológico)
3. [Roles y Permisos](#-roles-y-permisos)
4. [Módulos Principales y Reglas de Negocio](#-módulos-principales-y-reglas-de-negocio)
5. [Modelo de Datos](#-modelo-de-datos)
6. [Instalación y Configuración](#-instalación-y-configuración)
7. [Equipo de Desarrollo](#-equipo-de-desarrollo)

---

## 🎯 Descripción del Problema y Solución

Los pequeños negocios y proveedores gastronómicos suelen difundir su oferta mediante publicaciones dispersas o contacto directo, lo que dificulta a los clientes comparar opciones, verificar stock y organizar sus compras. 

**EcoSumaq V2** resuelve esto centralizando la publicación de productos, la gestión de carritos de compra, la generación de pedidos y el seguimiento de estados en tiempo real mediante una arquitectura cliente-servidor desacoplada.

---

## 🛠️ Arquitectura y Stack Tecnológico

El sistema sigue una arquitectura desacoplada dividida en **Frontend**, **API REST** y **Capa de Persistencia**:

| Capa / Componente | Tecnología | Responsabilidad |
| :--- | :--- | :--- |
| **Frontend** | React + Vite | Interfaz de usuario por roles, formularios, navegación y consumo HTTP. |
| **Backend** | Node.js + Express | API REST, rutas, controladores, servicios y lógica de negocio. |
| **Base de Datos** | MySQL | Persistencia relacional de la información. |
| **ORM** | Prisma | Modelado de datos, consultas tipadas y migraciones. |
| **Validación** | Zod | Validación estricta de esquemas, parámetros y entradas. |
| **Seguridad** | JWT + bcryptjs | Autenticación basada en tokens, cifrado de contraseñas y control de acceso. |
| **Documentación** | Swagger / OpenAPI | Especificación interactiva de endpoints y respuestas. |
| **Pruebas** | Postman, Jest y Supertest | Pruebas manuales y automatizadas de integración. |

---

## 👥 Roles y Permisos

El control de acceso se valida tanto en el frontend como en cada endpoint protegido del backend mediante JWT:

* **🛒 COMPRADOR:**
  * Explora el catálogo, busca por nombre/descripción y filtra por categoría o disponibilidad.
  * Gestiona su carrito de compras y genera pedidos.
  * Consulta su historial de pedidos, estados y califica a los proveedores tras completar una compra.
* **🏪 PROVEEDOR:**
  * Registra y administra el perfil comercial de su negocio.
  * Publica, edita y desactiva sus propios productos (precios, fotografías, stock y disponibilidad).
  * Recibe pedidos, los acepta o rechaza y actualiza su estado de entrega.
* **🛡️ ADMIN:**
  * Supervisa la plataforma de forma integral.
  * Verifica cuentas de proveedores y gestiona usuarios y categorías.
  * Audita pedidos, registros de pago y comisiones generadas.

---

## ⚙️ Módulos Principales y Reglas de Negocio

### Módulos Funcionales
1. **Usuarios y Autenticación:** Registro, login, gestión de perfiles y protección de rutas por rol.
2. **Proveedores y Catálogo:** Perfiles comerciales, categorías, gestión de productos e imágenes.
3. **Carrito y Pedidos:** Carrito persistente, generación de órdenes, detalle histórico y flujo de estados.
4. **Pagos y Comisiones:** Registro de pagos y cálculo automático de comisión de plataforma.
5. **Administración:** Verificación de negocios y monitoreo general.

### Reglas de Negocio Clave
* **Un proveedor por pedido:** Para garantizar una logística viable en la v2, todos los productos incluidos en un mismo pedido deben pertenecer a un único proveedor.
* **Integridad de precios (Snapshot):** El detalle del pedido guarda el nombre y precio unitario vigentes al momento de la compra para evitar alteraciones históricas.
* **Control de stock:** No se permite agregar productos sin disponibilidad ni generar pedidos si el stock es insuficiente.
* **Comisión de plataforma:** Se calcula una única vez por pedido elegible con una tasa inicial del **5 %** (ej. para un pedido de `S/ 80.00`, la comisión es `S/ 4.00`).
* **Trazabilidad:** Los pedidos pagados no se eliminan físicamente de la base de datos; las cancelaciones siguen un flujo de estados controlado.
* **Calificaciones únicas:** Solo se puede emitir una calificación por cada pedido válido completado.

---

## 🗄️ Modelo de Datos

La base de datos relacional en MySQL (gestionada con Prisma) contempla las siguientes entidades principales:

* `usuarios`: Credenciales, roles (`COMPRADOR`, `PROVEEDOR`, `ADMIN`) y estado de cuenta.
* `proveedores`: Información comercial vinculada a un usuario y estado de verificación.
* `categorias`: Clasificación de productos alimenticios e insumos.
* `productos` e `imagenes_productos`: Catálogo, precios, stock, disponibilidad y URLs multimedia.
* `carritos` y `detalle_carrito`: Ítems seleccionados temporalmente por el comprador.
* `pedidos` y `detalle_pedido`: Órdenes confirmadas, modalidad de entrega y snapshot de productos.
* `pagos` y `comisiones`: Registro financiero de transacciones y cálculo del 5 % de plataforma.
* `calificaciones`: Puntuación y reseñas de compradores hacia proveedores.

---

## 🚀 Instalación y Configuración

*(Guía base para levantar el entorno de desarrollo local)*

### Prerrequisitos
* [Node.js](https://nodejs.org/) (v18 o superior)
* [MySQL](https://www.mysql.com/) (v8.0 o superior)
* Git

### 1. Clonar el repositorio
```bash
git clone https://github.com/tu-usuario/ecosumaq.git
cd ecosumaq-v2
```

### 2. Configurar el Backend
```bash
cd backend
npm install
```
Crea un archivo `.env` en la raíz del backend basado en `.env.example`:
```env
PORT=3000
DATABASE_URL="mysql://usuario:password@localhost:3306/ecosumaq_db"
JWT_SECRET="tu_clave_secreta_super_segura"
```
Ejecuta las migraciones de Prisma y levanta el servidor:
```bash
npx prisma migrate dev
npm run dev
```
> La documentación de la API estará disponible en `http://localhost:3000/api-docs` (Swagger).

### 3. Configurar el Frontend
```bash
cd ../frontend
npm install
npm run dev
```

---

## 👨‍💻 Equipo de Desarrollo

* **Integrante 1:** [Chevarria Revollar Jose Andrei] — *Backend y API REST*
* **Integrante 2:** [Gamarra Laura Edgar Mauricio] — *Base de Datos y Prisma ORM*
* **Integrante 3:** [Racuaz Palma Balery Dayanara] — *Frontend React y UI/UX*
* **Integrante 4:** [Yabar Nieto Edgar Mauricio] — *Frontend React y UI/UX*
* **Docente:** [Ing. Mora Jimenez Ramiro]

---
*Desarrollado como parte del Proyecto Integrador – Unidad III (2026).*
