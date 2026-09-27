# Modelo documental - FixTrack

## 1. Objetivo

Este documento describe el modelo documental propuesto para la base de datos de **FixTrack**, plataforma SaaS multi-organización para la gestión y trazabilidad integral de servicios técnicos.

La solución utilizará **MongoDB Atlas** como base de datos NoSQL orientada a documentos.

El modelo busca representar la orden de reparación como el agregado principal del proceso y mantener como documentos independientes aquellas entidades que poseen identidad y ciclo de vida propios.

---

## 2. Colecciones principales

El modelo contempla las siguientes colecciones:

- `organizations`
- `users`
- `clients`
- `equipment`
- `repair_orders`
- `audit_logs`

### Responsabilidad de cada colección

| Colección | Responsabilidad |
|---|---|
| `organizations` | Representa cada servicio técnico registrado en la plataforma. |
| `users` | Almacena los usuarios pertenecientes a una organización y sus roles. |
| `clients` | Representa los clientes atendidos por cada servicio técnico. |
| `equipment` | Almacena los equipos asociados a clientes y organizaciones. |
| `repair_orders` | Representa las órdenes de reparación y concentra su ciclo completo de atención. |
| `audit_logs` | Registro técnico previsto para acciones relevantes de trazabilidad y auditoría. |

La colección `audit_logs` constituye un componente técnico transversal y no un módulo funcional adicional del MVP.

---

## 3. Estrategia documental

El modelo combinará **referencias entre documentos independientes** con **subdocumentos embebidos** dentro de las órdenes de reparación.

### 3.1. Documentos referenciados

Las siguientes entidades se mantendrán como documentos independientes:

- `Organization`
- `User`
- `Client`
- `Equipment`

Estas entidades poseen identidad y ciclo de vida propios y pueden ser consultadas o administradas independientemente.

Las relaciones se representarán mediante identificadores `ObjectId`.

### 3.2. Información embebida

Dentro de `repair_orders` se mantendrán como subdocumentos:

- `Diagnosis`
- `Quote`
- `StatusHistory`
- `Delivery`

Estos elementos forman parte directamente del ciclo de vida de una reparación y normalmente serán consultados junto con la orden.

---

## 4. Agregado principal: Repair Order

La colección `repair_orders` constituye el agregado principal del sistema.

Una orden contiene referencias a:

- la organización;
- el cliente;
- el equipo;
- el técnico responsable.

Además, contiene información propia del proceso de reparación:

- número visible de orden;
- problema informado;
- diagnóstico;
- presupuesto;
- estado operativo actual;
- historial cronológico de estados;
- datos de entrega;
- fecha de creación;
- fecha de última modificación.

Ejemplo conceptual:

```json
{
  "_id": "ObjectId",
  "organizationId": "ObjectId",
  "orderNumber": "ORD-000125",
  "clientId": "ObjectId",
  "equipmentId": "ObjectId",
  "assignedTechnicianId": "ObjectId",
  "status": "IN_DIAGNOSIS",
  "problemDescription": "El equipo no enciende",
  "diagnosis": {
    "problemDetected": "Falla en fuente de alimentación",
    "proposedSolution": "Reemplazo de componente",
    "observations": "Se detectaron daños en el circuito",
    "technicianId": "ObjectId",
    "createdAt": "Date"
  },
  "quote": {
    "description": "Reparación de fuente",
    "amount": 85000,
    "status": "PENDING",
    "createdAt": "Date"
  },
  "statusHistory": [
    {
      "status": "RECEIVED",
      "changedBy": "ObjectId",
      "changedAt": "Date",
      "observation": "Equipo recibido"
    },
    {
      "status": "IN_DIAGNOSIS",
      "changedBy": "ObjectId",
      "changedAt": "Date",
      "observation": "Equipo derivado al técnico"
    }
  ],
  "delivery": {
    "deliveredAt": null,
    "deliveredBy": null,
    "observation": null
  },
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

---

## 5. Número de orden

Cada orden tendrá un campo:

```text
orderNumber
```

Este valor será visible para los usuarios del sistema y permitirá identificar una reparación mediante un número práctico para las operaciones del servicio técnico.

Ejemplo:

```text
ORD-000125
```

El número deberá ser **único dentro de cada organización**, no necesariamente en toda la plataforma.

Esto se garantizará mediante un índice compuesto:

```text
{ organizationId: 1, orderNumber: 1 } UNIQUE
```

---

## 6. Aislamiento multi-organización

FixTrack utilizará `organizationId` como regla transversal de aislamiento lógico.

Todo documento perteneciente a una organización deberá incluir:

```text
organizationId
```

La colección `organizations` constituye la raíz de cada organización y no requiere un `organizationId` sobre sí misma.

La organización válida no será obtenida de un valor enviado libremente por el frontend.

El backend determinará la organización mediante el usuario autenticado con **Spring Security + JWT**.

Las consultas deberán restringirse a la organización correspondiente.

Ejemplo conceptual:

```text
findByIdAndOrganizationId(id, organizationId)
```

La capa de servicio deberá verificar además que las entidades relacionadas con una orden pertenezcan a la misma organización.

Por ejemplo:

```text
Order.organizationId
Client.organizationId
Equipment.organizationId
Technician.organizationId
```

deberán representar la misma organización.

---

## 7. Relaciones conceptuales

Aunque MongoDB no utiliza claves foráneas como una base de datos relacional, los documentos se vincularán mediante identificadores.

Las relaciones conceptuales principales son:

```text
Organization 1 ─────── N Users
Organization 1 ─────── N Clients
Organization 1 ─────── N Equipment
Organization 1 ─────── N RepairOrders
Organization 1 ─────── N AuditLogs

Client       1 ─────── N Equipment
Client       1 ─────── N RepairOrders

Equipment    1 ─────── N RepairOrders

User (Technician) 1 ── N RepairOrders
```

La integridad de estas referencias será validada por la capa de servicio del backend.

---

## 8. Roles

Los roles técnicos previstos para el MVP son:

```text
ADMIN
RECEPTIONIST
TECHNICIAN
```

Cada usuario pertenecerá a una única organización durante el MVP.

Los roles determinarán las operaciones permitidas, pero no reemplazarán el aislamiento por `organizationId`.

---

## 9. Autenticación

Para simplificar el inicio de sesión del MVP, el email utilizado por los usuarios será **único globalmente** en la plataforma.

El login podrá realizarse mediante:

```text
email + contraseña
```

sin solicitar un identificador adicional de organización.

Cada usuario continuará asociado internamente a una única `organizationId`.

---

## 10. Estados de una orden

Para la implementación se utilizarán nombres técnicos de estado en inglés.

La interfaz de usuario podrá mostrar sus equivalentes en español.

### OrderStatus

```text
RECEIVED
IN_DIAGNOSIS
AWAITING_BUDGET_APPROVAL
IN_REPAIR
READY_FOR_DELIVERY
DELIVERED
REJECTED
```

### Significado

| Estado | Descripción |
|---|---|
| `RECEIVED` | Registro inicial del equipo y creación de la orden. |
| `IN_DIAGNOSIS` | Análisis técnico y registro del problema detectado. |
| `AWAITING_BUDGET_APPROVAL` | Presupuesto cargado y pendiente de decisión del cliente. |
| `IN_REPAIR` | Presupuesto aprobado y trabajo técnico en ejecución. |
| `READY_FOR_DELIVERY` | Reparación finalizada y equipo disponible para retiro. |
| `DELIVERED` | Cierre de la orden y registro de entrega. |
| `REJECTED` | El presupuesto fue rechazado y la orden no continúa hacia reparación. |

---

## 11. Estado del presupuesto

El estado del presupuesto se gestionará independientemente del estado operativo de la orden.

### QuoteStatus

```text
PENDING
APPROVED
REJECTED
```

El flujo previsto es:

```text
RECEIVED
    ↓
IN_DIAGNOSIS
    ↓
AWAITING_BUDGET_APPROVAL
    ↓
IN_REPAIR
    ↓
READY_FOR_DELIVERY
    ↓
DELIVERED
```

Con una salida alternativa:

```text
AWAITING_BUDGET_APPROVAL
    ↓
REJECTED
```

---

## 12. Reglas de consistencia

El estado de la orden y el estado del presupuesto son conceptos diferentes, pero deberán mantener consistencia.

Una orden solamente podrá pasar de:

```text
AWAITING_BUDGET_APPROVAL
```

a:

```text
IN_REPAIR
```

cuando:

```text
quote.status == APPROVED
```

Si:

```text
quote.status == REJECTED
```

la orden deberá pasar a:

```text
REJECTED
```

No deberán permitirse combinaciones contradictorias entre `OrderStatus` y `QuoteStatus`.

---

## 13. Trazabilidad

Los cambios relevantes del ciclo de vida de una reparación se registrarán dentro de:

```text
statusHistory
```

Cada entrada podrá incluir:

- estado;
- usuario responsable del cambio;
- fecha y hora;
- observación cuando corresponda.

Los cambios de técnico responsable también deberán registrarse cuando sean relevantes para la trazabilidad.

La colección `audit_logs` se utilizará como soporte técnico transversal para registrar acciones sensibles o relevantes relacionadas con seguridad y auditoría.

---

## 14. Reglas generales de integridad

El diseño establece las siguientes reglas preliminares:

- `organizationId` se utilizará para aislar los datos entre organizaciones.
- El email de los usuarios será único globalmente en el MVP.
- Cada usuario pertenecerá a una única organización.
- `orderNumber` será único dentro de cada organización.
- Cliente, equipo y técnico asociados a una orden deberán pertenecer a la misma organización.
- Las transiciones de estado deberán respetar el ciclo definido.
- `OrderStatus` y `QuoteStatus` deberán mantener coherencia.
- El monto de un presupuesto deberá ser mayor o igual a cero.
- La integridad de las referencias será validada por la capa de servicio.
- Las operaciones deberán verificar autenticación y autorización antes de acceder a datos.