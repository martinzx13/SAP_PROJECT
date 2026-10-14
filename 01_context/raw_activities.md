# Actividades Originales (Input Crudo)

> Fuente: Conversación con Daniel Egas + imagen del cronograma original
> Fecha captura: 2026-10-14
> Estado: **Sin estructurar** — requiere descomposición (ver `plan_paso.md`)

---

## Tabla Original (7 filas)

| Workstream | TimeBox | Nombre de tarea | Descripción de tarea | Status | Responsable | Tiempo Estimado (Horas) | Fecha de inicio | Fecha de fin | Observación |
|------------|---------|-----------------|----------------------|--------|-------------|-------------------------|-----------------|--------------|-------------|
| R&P-ERP | | Configurar Usuario con Alias | | | | 0,1 | | | |
| IAS | | Configuración del SAML2 por ambiente y mandante | Configuración base de la aplicación SAML2 por ambiente y mandante | Pendiente | Equipo Seguridad | 8,33333333 | | | |
| IAS | | Hacer intercambio de metadata entre IAS y SAP 4H | Realizar el intercambio de datos metadata desde IAS hacia SAP 4Hana por mandante y ambiente | Pendiente | Equipo Seguridad | 1 | | | El tiempo está estimado por ambiente y por mandante |
| IAS | | Configuración del intercambio de metadata entre IAS y EntraID | Realizar el intercambio de datos metadata desde IAS hacia EntraID | Pendiente | Equipo Seguridad | 1 | | | |
| IAS | | Configuración de permisos Cloud Identity Services ID | Se deben hacer los ajustes de permisos a nivel de aplicación desde el CIS ID por mandante y ambiente | Pendiente | Equipo Seguridad | 2 | | | El tiempo está estimado por ambiente y por mandante |
| IAS | | Ejecución de pruebas iniciales | Realizar pruebas iniciales de SSO y MFA que será el mismo a usar desde el EntraID | Pendiente | Equipo Seguridad | 3 | | | El tiempo está estimado por ambiente y por mandante |
| IAS | | Replicar el procedimiento para calidad y producción | Replicar las configuraciones realizadas desde desarrollo a los demás ambientes no productivos y productivos | Pendiente | Equipo Seguridad | 3 | | | El tiempo está estimado por ambiente |

---

## Gaps Identificados vs. Requerimiento de Daniel Egas

| Gap | Impacto | Acción Requerida |
|-----|---------|------------------|
| `TimeBox` vacío | No hay sprint/phase boundaries | Definir TimeBoxes en Fase 1 |
| `Responsable` = "Equipo Seguridad" genérico | No distingue POC Lead vs IAS Admin vs Entra ID Admin vs Basis | Mapear permisos reales (Sección B `questions.md`) |
| Unidades mixtas (horas vs "por ambiente") | Inconsistente, no suma | Convertir a **minutos enteros** por tarea atómica |
| Sin dependencias | Orden de ejecución不明 | Mapear DAG: Metadata → Config → Test → Replicate |
| Workstream `R&P-ERP` no alineado | Scope confirmado: IAS, Entra ID, S/4HANA Fiori | Reclasificar a `S4_CONFIG` |
| Faltan workstreams: Entra ID, Testing detallado, Replicación por ambiente | Entregable incompleto | Agregar 7 workstreams estándar |
| `Observación` solo tiene notas de tiempo | Falta: `🔗 Depends on:`, `📖 Doc:`, `✅ Acceptance:`, `📎 Evidence:` | Enriquecer en Fase 2 |
| Sin doc links oficiales | No auditable | Citar SAP Help / Microsoft Learn / SAP Notes por workstream |

---

## Próximo Paso
Completar `questions.md` → Generar tabla ejecutiva (~25-30 filas atómicas) en `02_executive_plan/executive_config_table.md`