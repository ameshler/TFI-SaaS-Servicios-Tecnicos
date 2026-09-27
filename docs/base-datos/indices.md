# Índices preliminares - FixTrack

Los siguientes índices forman parte del diseño preliminar de MongoDB para FixTrack.

Podrán ajustarse durante la implementación según las consultas reales, patrones de acceso y necesidades de rendimiento de la aplicación.

El diseño prioriza especialmente el aislamiento multi-organización y las consultas frecuentes del MVP.

---

## Índices previstos

| Colección | Índice | Restricción | Objetivo |
|---|---|---|---|
| `users` | `{ email: 1 }` | UNIQUE | Garantizar que el email utilizado para autenticación sea único en toda la plataforma. |
| `users` | `{ organizationId: 1, role: 1 }` | — | Listar usuarios de una organización según su rol. |
| `clients` | `{ organizationId: 1, name: 1 }` | — | Buscar clientes dentro de una organización. |
| `equipment` | `{ organizationId: 1, serialNumber: 1 }` | — | Localizar equipos por número de serie dentro de una organización. |
| `repair_orders` | `{ organizationId: 1, orderNumber: 1 }` | UNIQUE | Garantizar que el número visible de una orden sea único dentro de cada organización. |
| `repair_orders` | `{ organizationId: 1, status: 1 }` | — | Listar órdenes de una organización según su estado. |
| `repair_orders` | `{ organizationId: 1, assignedTechnicianId: 1, status: 1 }` | — | Consultar órdenes asignadas a un técnico y filtrarlas por estado. |
| `repair_orders` | `{ organizationId: 1, clientId: 1 }` | — | Consultar el historial de reparaciones correspondiente a un cliente. |
| `repair_orders` | `{ organizationId: 1, equipmentId: 1 }` | — | Consultar el historial de reparaciones correspondiente a un equipo. |
| `repair_orders` | `{ organizationId: 1, createdAt: -1 }` | — | Obtener las órdenes más recientes de una organización. |
| `audit_logs` | `{ organizationId: 1, createdAt: -1 }` | — | Consultar cronológicamente las acciones de auditoría de una organización. |

---

## Email globalmente único

Durante el MVP el email utilizado para autenticación será único en toda la plataforma.

Por este motivo se utilizará:

```text
{ email: 1 } UNIQUE
```

Esto permite resolver el login mediante:

```text
email + contraseña
```

sin requerir que el usuario indique previamente su organización.

---

## Número de orden por organización

`orderNumber` representa un número visible y práctico para identificar una orden de reparación.

Ejemplo:

```text
ORD-000125
```

La unicidad se garantizará dentro de cada organización mediante:

```text
{ organizationId: 1, orderNumber: 1 } UNIQUE
```

Esto permite que organizaciones diferentes puedan utilizar numeraciones equivalentes sin generar conflictos.

Por ejemplo:

```text
Organización A → ORD-000125
Organización B → ORD-000125
```

son valores válidos porque pertenecen a tenants diferentes.

---

## Criterio multi-organización

Las consultas de negocio deberán incorporar `organizationId` cuando corresponda.

Ejemplo conceptual:

```text
findByOrganizationId(organizationId)
```

Para búsquedas individuales se deberá validar también la organización:

```text
findByIdAndOrganizationId(id, organizationId)
```

No deberá confiarse únicamente en:

```text
findById(id)
```

cuando se trate de recursos pertenecientes a una organización.

Del mismo modo, deberá evitarse utilizar:

```text
findAll()
```

en operaciones de negocio que permitan recuperar información perteneciente a múltiples organizaciones sin aplicar el filtro correspondiente.

---

## Consideración sobre los índices

Los índices definidos en esta etapa son **preliminares**.

Durante la implementación podrán:

- mantenerse;
- modificarse;
- eliminarse;
- agregarse nuevos índices;

según las consultas reales de la aplicación y las necesidades detectadas mediante pruebas y uso del sistema.