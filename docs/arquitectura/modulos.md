# Módulos funcionales - FixTrack

Los módulos funcionales definidos para el MVP de FixTrack son los siguientes.

La prioridad se interpreta de la siguiente manera:

- **P0:** módulo crítico para el funcionamiento del flujo principal.
- **P1:** módulo importante dentro del MVP, pero con menor prioridad relativa.

La complejidad es preliminar y podrá ajustarse durante el refinamiento del backlog.

| ID | Módulo | Descripción | Prioridad | Complejidad |
|---|---|---|---|---|
| MOD-01 | Autenticación | Registro e inicio de sesión, generación y validación de JWT y acceso seguro a recursos protegidos. | P0 | Alta |
| MOD-02 | Organizaciones | Gestión de servicios técnicos y pertenencia de datos y usuarios, garantizando aislamiento lógico entre organizaciones. | P0 | Alta |
| MOD-03 | Usuarios y Roles | Gestión de usuarios y roles ADMIN, RECEPTIONIST y TECHNICIAN, con permisos según responsabilidades. | P1 | Alta |
| MOD-04 | Clientes | Registro, consulta y actualización de clientes de cada organización. | P0 | Media |
| MOD-05 | Equipos | Gestión de dispositivos y asociación con el cliente propietario. | P0 | Media |
| MOD-06 | Órdenes de Reparación | Gestión del trabajo de reparación vinculando cliente, equipo, técnico responsable y ciclo de vida. | P0 | Muy alta |
| MOD-07 | Diagnóstico | Registro de falla detectada, solución propuesta y observaciones técnicas. | P0 | Media |
| MOD-08 | Presupuesto | Gestión de conceptos, importe y decisión del cliente. | P0 | Media/Alta |
| MOD-09 | Estados e Historial | Control de estados y trazabilidad cronológica de cambios relevantes. | P0 | Alta |
| MOD-10 | Entrega | Cierre del ciclo de reparación y registro de entrega del equipo. | P0 | Baja/Media |
| MOD-11 | Dashboard | Indicadores operativos básicos sobre volumen de órdenes y distribución por estado. | P1 | Media |