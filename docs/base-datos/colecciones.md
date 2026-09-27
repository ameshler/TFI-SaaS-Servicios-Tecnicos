# Colecciones - FixTrack

Este documento detalla las colecciones previstas para la base de datos MongoDB de FixTrack, sus campos principales, tipos y obligatoriedad.

---

## `organizations`

Representa cada servicio técnico registrado en la plataforma.

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `_id` | ObjectId | Sí | Identificador de la organización. |
| `name` | String | Sí | Nombre del servicio técnico. |
| `createdAt` | Date | Sí | Fecha de creación. |
| `active` | Boolean | Sí | Indica si la organización se encuentra activa. |

Ejemplo:

```json
{
  "_id": "ObjectId",
  "name": "Servicio Técnico Centro",
  "createdAt": "Date",
  "active": true
}
```

---

## `users`

Representa a los usuarios pertenecientes a una organización y su rol dentro de la plataforma.

Durante el MVP, cada usuario pertenecerá a una única organización.

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `_id` | ObjectId | Sí | Identificador del usuario. |
| `organizationId` | ObjectId | Sí | Organización a la que pertenece. |
| `name` | String | Sí | Nombre del usuario. |
| `email` | String | Sí | Correo utilizado para autenticación. Será único globalmente en el MVP. |
| `passwordHash` | String | Sí | Contraseña almacenada mediante hash. |
| `role` | Enum/String | Sí | `ADMIN`, `RECEPTIONIST` o `TECHNICIAN`. |
| `active` | Boolean | Sí | Estado lógico del usuario. |
| `createdAt` | Date | Sí | Fecha de alta. |

Ejemplo:

```json
{
  "_id": "ObjectId",
  "organizationId": "ObjectId",
  "name": "Juan Pérez",
  "email": "juan@serviciotecnico.com",
  "passwordHash": "...",
  "role": "TECHNICIAN",
  "active": true,
  "createdAt": "Date"
}
```

---

## `clients`

Representa al cliente que entrega uno o más equipos al servicio técnico.

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `_id` | ObjectId | Sí | Identificador del cliente. |
| `organizationId` | ObjectId | Sí | Organización a la que pertenece el cliente. |
| `name` | String | Sí | Nombre o razón social del cliente. |
| `phone` | String | No | Teléfono de contacto. |
| `email` | String | No | Correo electrónico de contacto. |
| `createdAt` | Date | Sí | Fecha de alta. |
| `active` | Boolean | Sí | Estado lógico del cliente. |

Ejemplo:

```json
{
  "_id": "ObjectId",
  "organizationId": "ObjectId",
  "name": "Carlos Gómez",
  "phone": "342-5555555",
  "email": "carlos@email.com",
  "createdAt": "Date",
  "active": true
}
```

---

## `equipment`

Representa un equipo perteneciente a un cliente.

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `_id` | ObjectId | Sí | Identificador del equipo. |
| `organizationId` | ObjectId | Sí | Organización a la que pertenece el equipo. |
| `clientId` | ObjectId | Sí | Cliente propietario del equipo. |
| `type` | String | Sí | Tipo de equipo. |
| `brand` | String | Sí | Marca. |
| `model` | String | Sí | Modelo. |
| `serialNumber` | String | No | Número de serie, cuando exista. |
| `createdAt` | Date | Sí | Fecha de alta. |
| `active` | Boolean | Sí | Estado lógico del equipo. |

Ejemplo:

```json
{
  "_id": "ObjectId",
  "organizationId": "ObjectId",
  "clientId": "ObjectId",
  "type": "Notebook",
  "brand": "Lenovo",
  "model": "ThinkPad E14",
  "serialNumber": "ABC123456",
  "createdAt": "Date",
  "active": true
}
```

---

## `repair_orders`

Constituye la colección central de FixTrack.

Representa una orden de reparación y concentra tanto referencias a otras entidades como información propia del proceso.

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `_id` | ObjectId | Sí | Identificador interno de la orden. |
| `organizationId` | ObjectId | Sí | Organización a la que pertenece la orden. |
| `orderNumber` | String | Sí | Número visible de la orden, único dentro de cada organización. |
| `clientId` | ObjectId | Sí | Cliente asociado. |
| `equipmentId` | ObjectId | Sí | Equipo asociado. |
| `assignedTechnicianId` | ObjectId | No | Técnico responsable actual. |
| `status` | Enum | Sí | Estado operativo actual de la orden. |
| `problemDescription` | String | Sí | Problema informado al ingresar el equipo. |
| `diagnosis` | Object | No | Diagnóstico técnico y observaciones. |
| `quote` | Object | No | Presupuesto y su estado. |
| `statusHistory` | Array | Sí | Historial cronológico de cambios de estado. |
| `delivery` | Object | No | Datos de cierre y entrega. |
| `createdAt` | Date | Sí | Fecha de creación. |
| `updatedAt` | Date | Sí | Última modificación. |

Ejemplo:

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
  "diagnosis": { },
  "quote": { },
  "statusHistory": [ ],
  "delivery": { },
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### Subdocumentos de `repair_orders`

| Subdocumento | Campos principales |
|---|---|
| `diagnosis` | `problemDetected`, `proposedSolution`, `observations`, `technicianId`, `createdAt` |
| `quote` | `description`, `amount`, `status`, `createdAt` |
| `statusHistory` | `status`, `changedBy`, `changedAt`, `observation` |
| `delivery` | `deliveredAt`, `deliveredBy`, `observation` |

---

## `audit_logs`

Componente técnico transversal previsto para registrar acciones relevantes relacionadas con seguridad, cambios de estado y operaciones sensibles.

No constituye un módulo funcional adicional del MVP.

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| `_id` | ObjectId | Sí | Identificador del registro. |
| `organizationId` | ObjectId | Sí | Organización a la que corresponde la acción. |
| `userId` | ObjectId | Sí | Usuario que ejecutó la acción. |
| `action` | String/Enum | Sí | Tipo de acción registrada. |
| `entity` | String/Enum | Sí | Entidad afectada. |
| `entityId` | ObjectId | No | Identificador de la entidad afectada, cuando corresponda. |
| `createdAt` | Date | Sí | Fecha y hora de la acción. |

Ejemplo:

```json
{
  "_id": "ObjectId",
  "organizationId": "ObjectId",
  "userId": "ObjectId",
  "action": "ORDER_STATUS_CHANGED",
  "entity": "REPAIR_ORDER",
  "entityId": "ObjectId",
  "createdAt": "Date"
}
```

---

## Estados técnicos

### `OrderStatus`

```text
RECEIVED
IN_DIAGNOSIS
AWAITING_BUDGET_APPROVAL
IN_REPAIR
READY_FOR_DELIVERY
DELIVERED
REJECTED
```

### `QuoteStatus`

```text
PENDING
APPROVED
REJECTED
```

---

## Roles

```text
ADMIN
RECEPTIONIST
TECHNICIAN
```