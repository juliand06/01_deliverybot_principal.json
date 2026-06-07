# DeliveryBot 🤖☕ 
**Terminal de Pedidos Inteligente y Automatización de Cafetería**

DeliveryBot es una solución de automatización basada en n8n que convierte a Telegram en una terminal de pedidos inteligente para entornos institucionales. Resuelve el problema de las filas largas, la pérdida de órdenes y la falta de comunicación entre la cocina y los usuarios mediante la digitalización del menú y la automatización del ciclo de vida del pedido.

---

## 🚀 Objetivos del Proyecto
- Implementar un sistema de pedidos digitales mediante una interfaz conversacional en Telegram.
- Automatizar el cálculo de totales y la generación de números de orden únicos.
- Gestionar el ciclo de vida del pedido (Recibido, Preparación, En camino, Entregado).
- Centralizar el inventario y menú en Google Sheets.
- Generar reportes de ventas automáticos y optimizar la comunicación cocina-cliente.

---

## 🏗️ Arquitectura del Sistema

El proyecto está dividido en tres módulos principales orquestados por **n8n**:

1. **Interfaz de Usuario (Telegram):** Interfaz conversacional donde el usuario navega por el menú, gestiona su carrito y recibe notificaciones push de estado.
2. **Motor de Procesamiento (n8n):** Flujo modular que integra validación de stock, cálculos aritméticos de precios y un gestor dinámico de estados de preparación.
3. **Modelo de Datos (Google Sheets):** Base de datos relacional y ligera para la persistencia de la información.

### Estructura de la Base de Datos (`DeliveryBot_DB`)

| Hoja | Columnas Clave | Propósito |
| :--- | :--- | :--- |
| **MENÚ** | `id_producto`, `nombre`, `descripción`, `precio`, `categoría`, `stock` | Catálogo de productos disponibles y control de inventario. |
| **PEDIDOS** | `id_pedido`, `id_usuario`, `detalles_pedido`, `total_pago`, `estado`, `fecha`, `hora` | Registro histórico y en tiempo real de las órdenes. |
| **USUARIOS** | `telegram_id`, `nombre_completo`, `departamento/oficina`, `puntos_lealtad` | Gestión de clientes fidelizados. |
| **SESIONES** | `telegram_id`, `pantalla_actual`, `carrito_temporal`, `ultimo_cambio` | Almacenamiento temporal para el flujo conversacional. |

---

## 🧪 Simulación de Pruebas y Casos de Uso (Ejecución del Flujo)

> *Nota Técnica: Para auditar el comportamiento de los nodos en n8n, se detallan las trazas de datos (Inputs/Outputs) generadas durante las pruebas unitarias del flujo.*

### Caso de Prueba 1: Flujo "Realizar Pedido" y Validación de Stock
**Nodo Disparador:** Telegram Trigger (Evento: Callback Query / Botón).
* **Input (Telegram):** El usuario selecciona `1 Café + 1 Empanada`.
* **Procesamiento (n8n - Google Sheets Node):** 
  * Lectura de stock actual (Café: 15, Empanada: 5). Validación exitosa.
  * *Calculador de Precios Node:* (Café $2.50 + Empanada $3.00) = Total $5.50.
* **Output (Telegram Node):** Mensaje enviado: *"Llevas 1 Café + 1 Empanada. Total: $5.50. ¿Confirmar?"*

### Caso de Prueba 2: Confirmación y Persistencia en Base de Datos
**Nodo Disparador:** Telegram Trigger (Evento: Botón "Confirmar").
* **Procesamiento (n8n):**
  * *Code Node (Generador ID):* Crea identificador único `ORD-9843`.
  * *Google Sheets Node (Update):* Descuenta stock en hoja **MENÚ**.
  * *Google Sheets Node (Append):* Inserta nueva fila en hoja **PEDIDOS** con estado `Recibido`.
* **Output (Telegram Node):** Mensaje al usuario: *"¡Pedido ORD-9843 confirmado! Te avisaremos cuando comience a prepararse."*
* **Output Auxiliar:** Notificación enviada al chat de la cocina.

### Caso de Prueba 3: Gestión de Estados y Notificación Push
**Nodo Disparador:** Webhook / Cambio manual de estado en Google Sheets (Detectado por un nodo de Polling).
* **Input:** Administrador cambia celda `estado` de `Recibido` a `En camino` para la orden `ORD-9843`.
* **Procesamiento (n8n):** Identifica el `telegram_id` asociado a la orden.
* **Output (Telegram Node):** Notificación Push: *"🛵 Tu pedido ORD-9843 está en camino. ¡Prepárate para recibirlo!"*

---

## 📈 Resultados Esperados y Métricas

- **Tasa de pérdida de pedidos:** 0% (Persistencia en la nube garantizada).
- **Eficiencia Operativa:** Reducción de tiempos de espera estimada en un 40%.
- **Transparencia:** Notificaciones en tiempo real del ciclo de vida del pedido.
- **Inteligencia de Negocio:** Reporte automatizado diario de métricas (Producto más vendido, ingresos, hora pico).

---

## ⚙️ Configuración y Despliegue

1. Clonar el repositorio: `git clone https://github.com/TuUsuario/Proyecto_DeliveryBot_RicaurteAndrey.git`
2. Crear un Bot en **BotFather** (Telegram) y obtener el Token de la API.
3. Crear una hoja de cálculo en **Google Sheets** con la estructura definida y habilitar la API de Google en Google Cloud Console.
4. Importar el archivo `workflow_deliverybot.json` en tu instancia de **n8n**.
5. Configurar las credenciales (Telegram API y Google OAuth2) dentro de n8n.
6. Activar el flujo (Toggle Active).

---

## 📂 Enlaces del Proyecto

- **Archivo JSON del flujo modular:** [`workflow_deliverybot.json`](./workflow_deliverybot.json) 
- **Base de Datos de Prueba (Google Sheets):** [DeliveryBot_DB - Entorno de Pruebas](https://docs.google.com/spreadsheets/d/1DdTwRhFeE6FHeAGo2uXTW09sXxI6OuQHfoKsm-7reCc/edit?gid=368394602#gid=368394602)

---

**Desarrollador:** Andrey Julián Ricaurte 

#profe pasa y acontese que mi computador no me permite tomar capturas de pantalla a si que las pruebas las tendre por link y eso mucho