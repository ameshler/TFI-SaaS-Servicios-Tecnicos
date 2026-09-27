# FixTrack

> Plataforma SaaS multi-organización para la gestión y trazabilidad integral de servicios técnicos.

**Trabajo Final Integrador — Tecnicatura Universitaria en Programación a Distancia — 2026**

## Descripción

**FixTrack** es una plataforma web SaaS orientada a pequeños y medianos servicios técnicos que reciben equipos para diagnóstico, presupuesto, reparación y entrega.

El sistema busca centralizar en una única aplicación la información operativa que habitualmente se encuentra distribuida entre papel, planillas de cálculo, mensajería y registros aislados.

La plataforma será **multi-organización**, permitiendo que distintos servicios técnicos utilicen una misma aplicación e infraestructura mientras mantienen sus datos lógicamente aislados.

## Problemática

En muchos servicios técnicos la información se administra mediante herramientas no integradas, como WhatsApp, anotaciones en papel, planillas de cálculo, archivos locales o sistemas genéricos.

Esto puede provocar:

- información fragmentada de clientes, equipos y reparaciones;
- dificultad para conocer rápidamente el estado de un trabajo;
- pérdida del historial de cambios;
- baja visibilidad sobre trabajos pendientes o técnicos asignados;
- duplicación de información;
- demoras al recuperar el historial de un cliente o equipo.

El problema central es la **ausencia de una fuente única, centralizada y trazable para gestionar el ciclo completo de una reparación**.

## Propuesta de valor

**Unificar en una sola plataforma el ciclo completo de una reparación, desde la recepción del equipo hasta su entrega, con trazabilidad por estado, responsables y organización.**

## Objetivo general

Diseñar, implementar y desplegar un MVP web para la gestión integral de servicios técnicos que centralice clientes, equipos y órdenes de reparación, permita gestionar responsables y estados, y conserve un historial trazable de cada trabajo dentro de un esquema SaaS multi-organización.

## Actores y roles

### Administrador

Responsabilidades principales:

- gestionar usuarios y roles;
- visualizar órdenes;
- controlar la actividad de la organización;
- consultar indicadores operativos.

Rol técnico:

```text
ADMIN
```

### Recepcionista

Responsabilidades principales:

- gestionar clientes;
- registrar equipos;
- crear órdenes de reparación;
- asignar técnicos;
- consultar estados.

Rol técnico:

```text
RECEPTIONIST
```

### Técnico

Responsabilidades principales:

- consultar órdenes asignadas;
- registrar diagnósticos;
- agregar observaciones;
- actualizar avances y estados permitidos.

Rol técnico:

```text
TECHNICIAN
```

### Cliente

El cliente es beneficiario del sistema, pero **no tendrá acceso directo en el MVP inicial**.

## Flujo principal de una reparación

```text
Recepción
   ↓
Diagnóstico
   ↓
Presupuesto
   ↓
Aprobación ─────→ Presupuesto rechazado
   ↓
Reparación
   ↓
Lista para entrega
   ↓
Entrega
```

Para la implementación se utilizarán los siguientes nombres técnicos:

```text
RECEIVED
IN_DIAGNOSIS
AWAITING_BUDGET_APPROVAL
IN_REPAIR
READY_FOR_DELIVERY
DELIVERED
REJECTED
```

El presupuesto tendrá un estado independiente:

```text
PENDING
APPROVED
REJECTED
```

El estado operativo de la orden y el estado del presupuesto serán conceptos diferentes, pero deberán mantener consistencia entre sí.

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

Si el presupuesto es rechazado:

```text
quote.status == REJECTED
```

la orden pasará a:

```text
REJECTED
```

## Alcance del MVP

### Autenticación

- Registro e inicio de sesión.
- Emisión y validación de JWT.
- Protección de endpoints.
- Autenticación stateless.

### Organizaciones

- Creación del servicio técnico.
- Asociación de usuarios a una organización.
- Aislamiento lógico de información entre organizaciones.

### Usuarios y roles

- `ADMIN`
- `RECEPTIONIST`
- `TECHNICIAN`
- Permisos diferenciados según responsabilidad.

### Clientes

- Alta.
- Consulta.
- Edición.
- Gestión de estado lógico.

### Equipos

- Tipo.
- Marca.
- Modelo.
- Número de serie.
- Asociación con cliente.

### Órdenes de reparación

- Creación y consulta.
- Número visible de orden.
- Edición controlada.
- Asignación de técnico.
- Seguimiento del ciclo de reparación.

### Diagnóstico

- Problema detectado.
- Solución propuesta.
- Observaciones técnicas.

### Presupuesto

- Conceptos e importe.
- Estados `PENDING`, `APPROVED` y `REJECTED`.

### Estados e historial

- Seguimiento cronológico del ciclo de reparación.
- Registro de cambios relevantes.
- Identificación del usuario responsable de cada cambio.

### Entrega

- Registro de cierre.
- Fecha de entrega.
- Usuario responsable.
- Observaciones.

### Dashboard básico

- Indicadores operativos simples.
- Volumen de órdenes.
- Distribución de órdenes por estado.

## Módulos funcionales

La prioridad se interpreta de la siguiente manera:

- **P0:** módulo crítico para el funcionamiento del flujo principal.
- **P1:** módulo importante dentro del MVP, pero con menor prioridad relativa.

La complejidad es preliminar y podrá ajustarse durante el refinamiento del backlog.

| ID | Módulo | Descripción | Prioridad | Complejidad |
|---|---|---|---|---|
| **MOD-01** | **Autenticación** | Registro e inicio de sesión, generación y validación de JWT y acceso seguro a recursos protegidos. | **P0** | Alta |
| **MOD-02** | **Organizaciones** | Gestión de servicios técnicos y pertenencia de datos y usuarios, garantizando aislamiento lógico entre organizaciones. | **P0** | Alta |
| **MOD-03** | **Usuarios y Roles** | Gestión de usuarios y roles `ADMIN`, `RECEPTIONIST` y `TECHNICIAN`, con permisos según responsabilidades. | **P1** | Alta |
| **MOD-04** | **Clientes** | Registro, consulta y actualización de clientes de cada organización. | **P0** | Media |
| **MOD-05** | **Equipos** | Gestión de dispositivos y asociación con el cliente propietario. | **P0** | Media |
| **MOD-06** | **Órdenes de Reparación** | Gestión del trabajo de reparación vinculando cliente, equipo, técnico responsable y ciclo de vida. | **P0** | Muy alta |
| **MOD-07** | **Diagnóstico** | Registro de falla detectada, solución propuesta y observaciones técnicas. | **P0** | Media |
| **MOD-08** | **Presupuesto** | Gestión de conceptos, importe y decisión del cliente. | **P0** | Media/Alta |
| **MOD-09** | **Estados e Historial** | Control de estados y trazabilidad cronológica de cambios relevantes. | **P0** | Alta |
| **MOD-10** | **Entrega** | Cierre del ciclo de reparación y registro de entrega del equipo. | **P0** | Baja/Media |
| **MOD-11** | **Dashboard** | Indicadores operativos básicos sobre volumen de órdenes y distribución por estado. | **P1** | Media |

La definición completa se encuentra en:

[Documentación de módulos](docs/arquitectura/modulos.md)

## Fuera de alcance del MVP

Quedan fuera de la primera versión:

- pagos online y suscripciones comerciales;
- aplicación móvil nativa;
- integración oficial con WhatsApp, SMS u otros proveedores externos;
- portal de autoservicio para clientes finales;
- facturación electrónica;
- integración con controladores fiscales;
- gestión avanzada de inventario de repuestos y compras;
- reportes analíticos avanzados;
- funcionalidades de inteligencia artificial.

## Arquitectura del proyecto

FixTrack utilizará una arquitectura cliente-servidor.

```text
┌────────────────────────────┐
│   React + TypeScript       │
│         Frontend           │
└─────────────┬──────────────┘
              │
          HTTPS / JSON
              │
              ▼
┌────────────────────────────┐
│        API REST            │
│    Java + Spring Boot      │
│                            │
│ Controller                 │
│     ↓                      │
│ Service                    │
│     ↓                      │
│ Repository                 │
│                            │
│ Spring Security + JWT      │
│ Spring Data MongoDB        │
└─────────────┬──────────────┘
              │
              ▼
┌────────────────────────────┐
│       MongoDB Atlas        │
└────────────────────────────┘
```

El backend seguirá conceptualmente una arquitectura por capas:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
MongoDB Atlas
```

Spring Security y JWT actuarán como componentes transversales para autenticación y autorización.

## Stack tecnológico

| Capa | Tecnología |
|---|---|
| Frontend | React + TypeScript |
| Backend | Java + Spring Boot |
| Persistencia | Spring Data MongoDB |
| Base de datos | MongoDB Atlas |
| Seguridad | Spring Security + JWT |
| Frontend cloud | Vercel |
| Backend cloud | Render |
| Control de versiones | GitHub |

## ¿Por qué MongoDB?

El dominio posee relaciones claras y también podría implementarse mediante una base de datos relacional.

MongoDB fue seleccionado porque la **orden de reparación funciona como agregado principal del sistema** y concentra información estrechamente relacionada con su ciclo de vida:

- diagnóstico;
- presupuesto;
- historial de estados;
- observaciones;
- información de entrega.

Estos elementos podrán almacenarse como subdocumentos dentro de `repair_orders`.

Las entidades que poseen identidad y ciclo de vida propios se mantendrán como documentos independientes:

```text
organizations
users
clients
equipment
```

El diseño combina, por lo tanto, documentos referenciados con información embebida.

## Colecciones principales

```text
organizations
users
clients
equipment
repair_orders
audit_logs
```

Responsabilidades generales:

| Colección | Responsabilidad |
|---|---|
| `organizations` | Servicios técnicos registrados en la plataforma. |
| `users` | Usuarios de cada organización y sus roles. |
| `clients` | Clientes atendidos por cada servicio técnico. |
| `equipment` | Equipos asociados a clientes y organizaciones. |
| `repair_orders` | Órdenes de reparación y ciclo completo de atención. |
| `audit_logs` | Registro técnico previsto para acciones relevantes de trazabilidad y auditoría. |

`audit_logs` será un componente técnico transversal y **no constituye un módulo funcional adicional del MVP**.

## Agregado RepairOrder

`repair_orders` constituye la colección central del sistema.

Conceptualmente:

```text
RepairOrder
│
├── organizationId
├── orderNumber
├── clientId
├── equipmentId
├── assignedTechnicianId
├── status
├── problemDescription
│
├── Diagnosis
├── Quote
├── StatusHistory[]
└── Delivery
```

Los siguientes elementos se almacenarán embebidos dentro de la orden:

```text
Diagnosis
Quote
StatusHistory
Delivery
```

Mientras que:

```text
Organization
User
Client
Equipment
```

se mantendrán como documentos independientes referenciados mediante identificadores.

## Número de orden

Cada orden tendrá un identificador interno de MongoDB y además un número visible para las operaciones del servicio técnico.

Ejemplo:

```text
ORD-000125
```

El campo:

```text
orderNumber
```

será único dentro de cada organización.

Índice previsto:

```text
{ organizationId: 1, orderNumber: 1 } UNIQUE
```

Dos organizaciones diferentes podrán utilizar la misma numeración sin generar conflictos.

## Aislamiento multi-organización

El aislamiento lógico constituye un requisito transversal de FixTrack.

Todo documento perteneciente a un servicio técnico deberá incluir:

```text
organizationId
```

La colección `organizations` constituye la raíz de cada tenant y no necesita incluir este campo sobre sí misma.

La organización válida **no será obtenida de un valor enviado libremente por el frontend**.

El backend determinará la organización mediante el usuario autenticado utilizando:

```text
Spring Security + JWT
```

Las consultas deberán restringirse a la organización correspondiente.

Ejemplo conceptual:

```java
findByIdAndOrganizationId(id, organizationId);
```

En lugar de depender únicamente de:

```java
findById(id);
```

Además:

```text
Cliente
Equipo
Técnico
Orden
```

asociados entre sí deberán pertenecer a la misma organización.

## Autenticación

Durante el MVP:

- cada usuario pertenecerá a una única organización;
- el email utilizado para autenticación será único globalmente;
- el inicio de sesión podrá realizarse mediante email y contraseña.

Ejemplo:

```text
email + contraseña
```

De esta manera, el usuario no deberá indicar manualmente a qué organización pertenece durante el login.

## Índices preliminares

Los principales índices previstos son:

```text
users
{ email: 1 } UNIQUE

users
{ organizationId: 1, role: 1 }

clients
{ organizationId: 1, name: 1 }

equipment
{ organizationId: 1, serialNumber: 1 }

repair_orders
{ organizationId: 1, orderNumber: 1 } UNIQUE

repair_orders
{ organizationId: 1, status: 1 }

repair_orders
{ organizationId: 1, assignedTechnicianId: 1, status: 1 }

repair_orders
{ organizationId: 1, clientId: 1 }

repair_orders
{ organizationId: 1, equipmentId: 1 }

repair_orders
{ organizationId: 1, createdAt: -1 }

audit_logs
{ organizationId: 1, createdAt: -1 }
```

Estos índices son preliminares y podrán ajustarse según las consultas reales y necesidades detectadas durante la implementación.

## Estrategia de pruebas

### Backend

Herramientas previstas:

- JUnit 5;
- Mockito;
- Spring Boot Test;
- MockMvc.

Casos prioritarios:

- reglas del ciclo de vida de las órdenes;
- presupuestos;
- transiciones de estado;
- validaciones;
- filtros por `organizationId`;
- endpoints protegidos;
- respuestas `401` y `403`;
- permisos por rol;
- intentos de acceso cruzado entre organizaciones.

### Frontend

Herramientas previstas:

- Vitest;
- React Testing Library.

Se priorizarán formularios y renderizado de estados o permisos relevantes.

## Documentación técnica

La documentación de análisis y diseño se encuentra organizada dentro del directorio `docs/`.

### Arquitectura

- [Módulos funcionales del MVP](docs/arquitectura/modulos.md)

### Base de datos

- [Modelo documental](docs/base-datos/modelo-documental.md)
- [Colecciones y campos](docs/base-datos/colecciones.md)
- [Índices preliminares](docs/base-datos/indices.md)
- [Ejemplos de documentos](docs/base-datos/ejemplos.json)

### Entregas académicas

- [Primera entrega](docs/entregas/01-primera-entrega/TFI_Primera_Entrega.pdf)
- [Segunda entrega](docs/entregas/02-segunda-entrega/TFI_Segunda_Entrega_FixTrack.pdf)

## Estructura actual del repositorio

```text
TFI-SaaS-Servicios-Tecnicos/
│
├── frontend/
│
├── backend/
│
├── docs/
│   │
│   ├── arquitectura/
│   │   └── modulos.md
│   │
│   ├── base-datos/
│   │   ├── modelo-documental.md
│   │   ├── colecciones.md
│   │   ├── indices.md
│   │   └── ejemplos.json
│   │
│   └── entregas/
│       │
│       ├── 01-primera-entrega/
│       │   └── TFI_Primera_Entrega.pdf
│       │
│       └── 02-segunda-entrega/
│           └── TFI_Segunda_Entrega_FixTrack.pdf
│
├── README.md
└── .gitignore
```

## Organización prevista para la implementación

El frontend se organizará progresivamente en módulos.

```text
frontend/
└── src/
    └── modules/
        ├── auth/
        ├── organizations/
        ├── users/
        ├── clients/
        ├── equipment/
        ├── repair-orders/
        │   ├── diagnosis/
        │   ├── quotes/
        │   ├── status-history/
        │   └── delivery/
        └── dashboard/
```

El backend utilizará una organización modular con arquitectura por capas.

```text
backend/
└── src/
    └── main/
        └── java/
            └── .../
                ├── auth/
                ├── organization/
                ├── user/
                ├── client/
                ├── equipment/
                ├── repairorder/
                └── dashboard/
```

El agregado principal podrá organizarse inicialmente de la siguiente manera:

```text
repairorder/
├── controller/
│   └── RepairOrderController.java
├── service/
│   └── RepairOrderService.java
├── repository/
│   └── RepairOrderRepository.java
├── model/
│   ├── RepairOrder.java
│   ├── Diagnosis.java
│   ├── Quote.java
│   ├── StatusHistory.java
│   └── Delivery.java
├── dto/
│   ├── CreateRepairOrderRequest.java
│   ├── UpdateRepairOrderRequest.java
│   └── RepairOrderResponse.java
└── mapper/
    └── RepairOrderMapper.java
```

Esta estructura representa una propuesta inicial y podrá refinarse durante la implementación.

---

## Instalación y ejecución

El proyecto se encuentra actualmente en etapa de análisis, diseño e inicio de implementación.

Las instrucciones definitivas de ejecución se incorporarán cuando los primeros módulos funcionales estén disponibles.

Se documentarán:

- requisitos previos;
- instalación de dependencias;
- ejecución del frontend;
- ejecución del backend;
- configuración de MongoDB Atlas;
- variables de entorno;
- configuración de seguridad;
- ejecución de pruebas;
- proceso de build.

No se incorporarán comandos ficticios mientras la configuración ejecutable definitiva no se encuentre disponible.

## Variables de entorno

El repositorio no almacenará:

- contraseñas;
- secretos JWT;
- cadenas de conexión reales;
- credenciales de MongoDB Atlas;
- otras credenciales sensibles.

Estos valores serán administrados mediante variables de entorno.

Cuando corresponda se incorporarán archivos de ejemplo sin información sensible.

## Estado actual del proyecto

**Etapa actual: Segunda Entrega — análisis y diseño.**

En esta etapa se definieron:

- arquitectura preliminar;
- stack tecnológico;
- modelo documental;
- colecciones principales;
- documentos embebidos y referenciados;
- aislamiento multi-organización;
- estados del ciclo de reparación;
- estado del presupuesto;
- índices preliminares;
- validaciones;
- módulos funcionales del MVP;
- organización inicial del repositorio.

La siguiente etapa corresponde al desarrollo incremental de los módulos definidos.

## Integrantes

- **Matías Ariel Deluca**
- **Gastón Armando Giorgio**
- **Andrés Emanuel Meshler**

## Tutora

**María Candela Grosso**

## Carrera

**Tecnicatura Universitaria en Programación a Distancia**

**Trabajo Final Integrador — 2026**