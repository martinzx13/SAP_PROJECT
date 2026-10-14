# PLAN MAESTRO POC — SSO IAS + Microsoft Entra ID + S/4HANA Fiori

> **Objetivo**: Poner en producción un POC de SSO funcional, paso a paso, empezando por conseguir los accesos.
> **Estado**: En fase de recopilación de información (Fase 0 en curso)
> **Creado**: 2026-10-14
> **POC Lead**: Juan Martín
> **Líder Solicitante**: Daniel Egas

---

## CÓMO USAR ESTE DOCUMENTO

Cada fase tiene:
1. **Contexto** — por qué existe la fase
2. **Preguntas** — qué necesitas averiguar (responder en `questions.md` o aquí mismo)
3. **Acciones** — qué hacer paso a paso
4. **Entregable** — qué sale de la fase

> **Regla**: No avances de fase hasta responder sus preguntas (o marcarlas `[PENDING]` y acordar con el owner quién resuelve).

---

# FASE 0 — CONSEGUIR ACCESOS (Semana 0, ANTES de todo)

## Contexto
Sin estos accesos no se puede configurar NADA. Es el bloqueante #1. Cada sistema necesita un pedido formal.

## Preguntas de la Fase 0

| # | Pregunta | Respuesta |
|---|----------|-----------|
| 0.1 | ¿Quién es tu **Basis de DEV** (nombre + email)? | `[PENDING]` |
| 0.2 | ¿Quién es tu **IAS Admin** (nombre + email)? | `[PENDING]` |
| 0.3 | ¿Quién es tu **Entra ID Admin** (nombre + email)? | `[PENDING]` |
| 0.4 | ¿Existe un **ticketing system** (ServiceNow, Jira, email) para pedir accesos? Cuál y número de ticket | `[PENDING]` |
| 0.5 | ¿Tienes **VPN / acceso remoto** al entorno DEV? | `[PENDING]` |
| 0.6 | ¿Tienes **SAP GUI instalado** en tu máquina? | `[PENDING]` |
| 0.7 | ¿Tienes **licencia SAP** asignada (usuarios dependen de tipo de licencia)? | `[PENDING]` |

## Acciones de la Fase 0

### Pedido 1 — SAP GUI DEV (a Basis/Security)
> **Texto para enviar**:
> "Solicito usuario SAP en ambiente DEV para POC SSO (IAS + Entra ID + Fiori). Requiero:
> - Cliente: `[PENDING — confirmar con Basis: ¿100?]`
> - Roles/permisos: transacciones `SAML2`, `SU01`, `RZ10`, `PFCG`, `STRUST`, `SICF`, `SM51`, `SCC4`
> - Servicio ICF activo: `/sap/public/bc/sec/saml2`
> - Parámetros de perfil: `login/create_sso2_ticket`, `login/accept_sso2_ticket`, `login/ticket_only_by_https`
> - Motivo: POC de SSO SAML 2.0 con SAP Cloud Identity Services (IAS) y Microsoft Entra ID
> - Aprobador: Daniel Egas"

### Pedido 2 — IAS Administrator (a IAS Admin/Security)
> "Solicito rol **Administrator** en tenant IAS `[PENDING — URL del tenant]` para:
> - Crear Application tipo Trusted Provider (S/4HANA)
> - Configurar Corporate IdP (Microsoft Entra ID)
> - Attribute Transformation (Logon Alias mapping)
> - Routing Rules
> - Certificates y download de Metadata SP
> Motivo: POC SSO — aprobado por Daniel Egas"

### Pedido 3 — Entra ID Application Admin (a Entra ID Admin)
> "Solicito rol **Application Administrator** (o Cloud Application Administrator) en Entra ID tenant `[PENDING — Tenant ID]` para:
> - Crear/configurar Enterprise App (SAML SSO)
> - Mapear Claims (UPN, samAccountName, employeeId, mail, groups)
> - Configurar Conditional Access (MFA)
> - Asignar usuarios de prueba
> - Subir certificado firma IAS, descargar Metadata XML
> Motivo: POC SSO — aprobado por Daniel Egas"

### Pedido 4 — Usuarios de prueba (a ambos admins)
| Dónde | Qué pedir | Cantidad |
|-------|-----------|----------|
| **Entra ID** | 3 usuarios prueba (ej: `test.sso1@company.com`, `test.sso2`, `test.sso3`) + asignar a la Enterprise App | 3 |
| **S/4HANA DEV** | 3 usuarios via `SU01` con Logon Alias + roles Fiori (`SAP_FIORI_FOUNDATION` + rol negocio) | 3 |

### Pedido 5 — Información técnica (a Basis DEV)
> "Para la POC necesito confirmar:
> 1. ¿SID del sistema DEV? (ej: `D01`)
> 2. ¿Cliente(s) que entran en la POC? (ej: `100`)
> 3. ¿URL del Fiori Launchpad DEV?
> 4. ¿Existe la transacción `SAML2`? (S/4HANA 2020+)
> 5. ¿Ya existe certificado SAML firmante en `STRUST`?
> 6. ¿El servicio ICF `/sap/public/bc/sec/saml2` está activo?"

## Entregable Fase 0
- [ ] Ticket/email de cada pedido enviado
- [ ] Accesos recibidos y probados (login exitoso en IAS, Entra ID, SAP GUI)
- [ ] Tabla de contactos completada (Sección D de `questions.md`)
- [ ] Preguntas 0.1–0.7 respondidas

---

# FASE 1 — INVENTARIO Y DECISIONES (Día 1-2)

## Contexto
Con los accesos en mano, confirmas la topología real del sistema y tomas las 3 decisiones técnicas bloqueantes que cambian ~10 filas del plan.

## Preguntas de la Fase 1

### Topología (confirmada por Basis)
| # | Pregunta | Respuesta |
|---|----------|-----------|
| 1.1 | **SID** del sistema DEV | `[PENDING — pedir a Basis]` |
| 1.2 | **Cliente(s)** que entran en la POC (ej: `100`) | `[PENDING — pedir a Basis]` |
| 1.3 | **URL Fiori Launchpad** DEV | `[PENDING — pedir a Basis]` |
| 1.4 | ¿Existe transacción **`SAML2`** o se usa `SAML_CONFIG` antigua? | `[PENDING — probar /nSAML2` |
| 1.5 | ¿Ya existe **certificado SAML** en `STRUST`/`SAML2`? | `[PENDING — pedir a Basis]` |
| 1.6 | ¿Servicio **ICF `/sap/public/bc/sec/saml2`** activo? | `[PENDING — pedir a Basis]` |
| 1.7 | ¿Parámetros **SSO2** seteados en perfil? | `[PENDING — pedir a Basis]` |

### Tus Permisos (verificar cuando tengas accesos)
| # | Consola | ¿Puedo crear/guardar? |
|---|---------|----------------------|
| 1.8 | IAS Admin Console (Applications, Corporate IdP, Trusted Provider, Routing, Certificates) | `[PENDING]` |
| 1.9 | Entra ID (Enterprise Apps, SAML, Claims, CA, Usuarios) | `[PENDING]` |
| 1.10 | S/4HANA (`SAML2`, `SU01`, `RZ10`, `PFCG`, `STRUST`) | `[PENDING]` |

### Decisiones Técnicas (definir ANTES de configurar)
| # | Decisión | Opciones | Tu Elección |
|---|----------|----------|-------------|
| 1.11 | **Logon Alias** (qué envía IAS a S/4HANA) | `samAccountName` (`edgar.chacon`) / `userPrincipalName` (`edgar.chacon@corp.com`) / `employeeId` / `mail` | `[PENDING]` |
| 1.12 | **Routing IAS** (quiénes van a Entra ID) | Todos → Entra ID / Por dominio (`@corp.com`) / Por grupo Entra ID / Risk-based | `[PENDING]` |
| 1.13 | **Certificados SAML** | IAS-managed (auto-rotación) / Customer-managed (tú subes PFX) | `[PENDING]` |
| 1.14 | **Single Logout (SLO)** | Sí, propagado a Entra ID / No / Solo local | `[PENDING]` |
| 1.15 | **NameID Format** | `unspecified` / `emailAddress` / `persistent` / `transient` | `[PENDING]` |

### Contexto Existente (evita retrabajo)
| # | Pregunta | Respuesta |
|---|----------|-----------|
| 1.16 | ¿Ya existe **Enterprise App** en Entra ID para SAP IAS? | `[PENDING]` |
| 1.17 | ¿El **tenant IAS** ya tiene aplicaciones configuradas (es virgen)? | `[PENDING]` |
| 1.18 | ¿Los **usuarios prueba** ya existen en Entra ID? | `[PENDING]` |
| 1.19 | ¿Los **usuarios prueba** ya existen en S/4HANA? | `[PENDING]` |
| 1.20 | ¿Hay **proxy/firewall/WAF** entre Fiori e IAS/Entra ID? | `[PENDING]` |
| 1.21 | ¿Hay **documentación de intentos previos** de SSO fallidos? | `[PENDING]` |

## Acciones de la Fase 1
1. Ejecutar cada pedido de la Fase 0 (si no se hicieron)
2. Confirmar topología con Basis (1.1–1.7)
3. Verificar tus permisos reales (1.8–1.10)
4. Reunión de decisiones técnicas con Security/Basis (1.11–1.15)
5. Confirmar contexto existente (1.16–1.21)
6. Documentar todo en `01_context/inventory.md`

## Entregable Fase 1
- [ ] Documento `01_context/inventory.md` con topología, permisos, decisiones
- [ ] Decisión Logon Alias tomada y comunicada
- [ ] Decisión Routing tomada
- [ ] Decisiones de certificados y SLO tomadas

---

# FASE 2 — CONFIGURACIÓN ENTRA ID (Día 2-3)

## Contexto
Entra ID es el **Corporate IdP**: autentica al usuario, aplica MFA y envía el assertion a IAS. Se configura PRIMERO porque IAS necesita el Metadata XML de Entra ID.

## Preguntas de la Fase 2
| # | Pregunta | Respuesta |
|---|----------|-----------|
| 2.1 | ¿Tenant ID de Entra ID? | `[PENDING]` |
| 2.2 | ¿Dominio corporativo (`company.com`)? | `[PENDING]` |
| 2.3 | ¿Política MFA existente (CA policy) que aplique o crear nueva? | `[PENDING]` |
| 2.4 | ¿Qué claim usa como identificador principal? (`userPrincipalName` o `samAccountName`) | Depende 1.11 |

## Acciones (Workstream `ENTRA_ID_APP`)
| Paso | Acción | Consola | Owner | Min | Depende de |
|------|--------|---------|-------|-----|------------|
| 2.1 | Crear **Enterprise App** "SAP IAS SSO" (Non-gallery) | Entra ID → Enterprise Apps → New app | Entra ID Admin | 15 | 0.3 |
| 2.2 | Configurar **SAML SSO**: EntityID, ACS URL, Sign-on URL | App → Single sign-on | Entra ID Admin | 20 | 2.1, 1.3 |
| 2.3 | **Mapear Claims**: `userPrincipalName`, `samAccountName`, `employeeId`, `mail`, `groups` | App → Attributes & Claims | Entra ID Admin | 15 | 2.2, 1.11 |
| 2.4 | Subir **certificado firma IAS** | App → SAML Signing Certificate | Entra ID Admin | 10 | 3.6 |
| 2.5 | **Asignar usuarios/grupos** prueba | App → Users and groups | Entra ID Admin | 10 | 0.4 |
| 2.6 | Configurar **Conditional Access MFA** | Conditional Access | Entra ID Admin | 20 | 2.1, 2.3 |
| 2.7 | **Descargar Metadata XML** de Entra ID | App → Download Federation Metadata | Entra ID Admin | 5 | 2.2 |

> **Nota 2.4**: El certificado de IAS se descarga en Fase 3 (paso 3.6). Si IAS no existe aún, se sube después.
> **Dependencia real**: 2.1 → 2.2 → 2.3 → 2.7 primero; 2.4, 2.5, 2.6 pueden ir en paralelo.

## Entregable Fase 2
- [ ] Enterprise App creada y configurada SAML
- [ ] Claims mapeados
- [ ] Metadata XML descargada (guardar en `01_context/entra_metadata.xml`)
- [ ] Usuarios prueba asignados
- [ ] MFA activo via Conditional Access

---

# FASE 3 — CONFIGURACIÓN IAS (Día 3-4)

## Contexto
IAS es el **broker**: recibe la request de Fiori (SP-initiated), redirige a Entra ID, recibe el assertion, lo transforma (Logon Alias) y lo envía a S/4HANA. **Nodo central de toda la arquitectura.**

## Preguntas de la Fase 3
| # | Pregunta | Respuesta |
|---|----------|-----------|
| 3.1 | ¿URL del tenant IAS? | `[PENDING]` |
| 3.2 | ¿EntityID acordado para la App? (convención corporativa?) | `[PENDING — ver 1.13]` |
| 3.3 | ¿ACS URL exacta de S/4HANA? (`https://<host>:<port>/sap/public/bc/sec/saml2/acs`) | Depende 1.3 |
| 3.4 | ¿Qué atributo exacto se envía como NameID? | Depende 1.11 |

## Acciones (Workstream `IAS_APPLICATION` + `IAS_ENTRA_METADATA`)
| Paso | Acción | Consola | Owner | Min | Depende de |
|------|--------|---------|-------|-----|------------|
| 3.1 | Crear **Application** tipo Trusted Provider (S/4HANA) | IAS → Applications → Add | IAS Admin / POC Lead | 20 | 1.11, 1.12 |
| 3.2 | Configurar **SAML 2.0**: EntityID, ACS URL, NameID Format | App → SAML 2.0 | IAS Admin / POC Lead | 20 | 3.1, 1.3, 3.3 |
| 3.3 | **Attribute Transformation**: Logon Alias mapping (ej: `samAccountName` → `NameID`) | App → Attribute Transformation | IAS Admin / POC Lead | 15 | 3.2, 1.11 |
| 3.4 | **Routing Rule**: Default → Entra ID | App → Routing Rules | IAS Admin / POC Lead | 10 | 3.1, 1.12 |
| 3.5 | Configurar **Corporate IdP** = Entra ID (subir metadata 2.7) | IAS → Corporate Identity Providers | IAS Admin | 15 | 2.7 |
| 3.6 | Verificar **certificados firma IAS** | IAS → Certificate Management | IAS Admin | 10 | 3.1 |
| 3.7 | **Descargar Metadata SP de IAS** | App → SAML 2.0 → Download | IAS Admin / POC Lead | 5 | 3.2 |

## Entregable Fase 3
- [ ] App IAS Trusted Provider creada
- [ ] SAML 2.0 configurado (EntityID, ACS, NameID)
- [ ] Attribute Transformation con Logon Alias definido
- [ ] Routing → Entra ID
- [ ] Corporate IdP (Entra ID) activo
- [ ] Metadata SP IAS descargada (guardar en `01_context/ias_sp_metadata.xml`)

---

# FASE 4 — CONFIGURACIÓN S/4HANA (Día 4-5)

## Contexto
S/4HANA es el **consumidor final**: recibe el assertion de IAS, valida la firma, busca al usuario por Logon Alias y crea la sesión Fiori. Requiere permisos de **Basis**.

## Preguntas de la Fase 4
| # | Pregunta | Respuesta |
|---|----------|-----------|
| 4.1 | ¿Basis asignado a la POC (nombre, email)? | `[PENDING]` |
| 4.2 | ¿Usuarios de prueba ya existen en S/4HANA? | `[PENDING]` |
| 4.3 | ¿Roles Fiori asignados (`SAP_FIORI_FOUNDATION` + negocio)? | `[PENDING]` |
| 4.4 | ¿Certificado firma IAS importado en `STRUST`? | Depende 3.6 |
| 4.5 | ¿`RZ10` parámetros SSO2 confirmados? | Depende 1.7 |

## Acciones (Workstream `S4_CONFIG` + `IAS_S4_METADATA`)
| Paso | Acción | Transacción | Owner | Min | Depende de |
|------|--------|-------------|-------|-----|------------|
| 4.1 | **Crear Local Provider** (importar Metadata SP de IAS) | `SAML2` → Local Provider | Basis | 20 | 3.7 |
| 4.2 | **Crear Trusted Provider** (importar Metadata de IAS Corporate IdP) | `SAML2` → Trusted Provider | Basis | 20 | 3.5 |
| 4.3 | **Importar cert firma IAS** | `STRUST` / `SAML2` → Certificate Maintenance | Basis | 15 | 3.6 |
| 4.4 | **Verificar/Activar ICF** `/sap/public/bc/sec/saml2` | `SICF` | Basis | 10 | 1.6 |
| 4.5 | **Confirmar parámetros SSO2** en perfil | `RZ10` | Basis | 10 | 1.7 |
| 4.6 | **Crear usuarios prueba** + Logon Alias (`USRACLEXT`) | `SU01` | Basis / POC Lead | 15/usuario | 1.11, 1.19 |
| 4.7 | **Asignar roles Fiori** a usuarios prueba | `PFCG` | Basis / POC Lead | 10/usuario | 4.6, 4.3 |

## Entregable Fase 4
- [ ] `SAML2` Local Provider + Trusted Provider configurados
- [ ] Certificado IAS importado
- [ ] ICF y parámetros SSO2 verificados
- [ ] 3 usuarios prueba con Logon Alias correcto
- [ ] Roles Fiori asignados

---

# FASE 5 — PRUEBAS (Día 5-6)

## Contexto
Sin pruebas documentadas no hay POC cerrada. Cada caso genera **evidencia** (screenshot, SAML trace, log).

## Preguntas de la Fase 5
| # | Pregunta | Respuesta |
|---|----------|-----------|
| 5.1 | ¿Navegadores a probar? (Chrome, Edge, Safari, Firefox) | `[PENDING]` |
| 5.2 | ¿Dispositivos? (Windows domain-joined, Mac, iPhone, Android) | `[PENDING]` |
| 5.3 | ¿Se prueba SLO? (depende 1.14) | Depende 1.14 |
| 5.4 | ¿Zona horaria del servidor para detectar clock skew? | `[PENDING]` |

## Casos de Prueba (Workstream `TESTING`)
| Caso | Qué probar | Cómo | Evidencia esperada |
|------|-----------|------|--------------------|
| TC-01 | **SP-initiated SSO** | Abrir URL Fiori → redirige a Entra ID → MFA → sesión Fiori | Screenshot + SAML Trace |
| TC-02 | **IdP-initiated** | MyApps Entra ID → click App → Fiori | Screenshot |
| TC-03 | **MFA challenge** | Usuario sin MFA → prompt registro | Screenshot |
| TC-04 | **Single Logout** | Logout Fiori → ¿cierra sesión Entra ID? (solo si SLO=Si) | Screenshot + logs |
| TC-05 | **Error: certificado expirado** | Simular cambio de fecha | Log de error |
| TC-06 | **Error: Logon Alias incorrecto** | Cambiar alias en `SU01` → reintento | SAML Trace (attribute mismatch) |
| TC-07 | **Error: clock skew** | Diferencia hora servidor vs IAS | Log |
| TC-08 | **Usuario no asignado** en Entra ID | Login con usuario sin asignación | Mensaje de error IAS |
| TC-09 | **Usuario sin Logon Alias** en S/4HANA | Login con alias vacío | Log SAML2 trace |
| TC-10 | **Acceso a varias apps Fiori** con misma sesión | Navegar entre apps | Screenshot |

## Entregable Fase 5
- [ ] Matriz de pruebas: 10/10 pasaron (o defectos documentados)
- [ ] Screenshots en `04_evidence/screenshots/`
- [ ] SAML traces en `04_evidence/saml_traces/`
- [ ] Resultados en `04_evidence/test_results.md`

---

# FASE 6 — REPLICACIÓN (Semana 2-3)

## Contexto
El POC se replica de DEV a QAS/PRD. Cada ambiente tiene **distintos SID, cliente, URLs y certificados** — no se copia y pega, se adapta.

## Preguntas de la Fase 6
| # | Pregunta | Respuesta |
|---|----------|-----------|
| 6.1 | ¿QAS entra en la POC? (SID, cliente, URL) | `[PENDING]` |
| 6.2 | ¿PRD entra en la POC? (SID, cliente, URL) | `[PENDING]` |
| 6.3 | ¿Se requiere Change Request formal para PRD? | `[PENDING]` |
| 6.4 | ¿Ventana de cambio para PRD (fecha/hora)? | `[PENDING]` |
| 6.5 | ¿Se necesitan usuarios prueba en QAS/PRD o solo DEV? | `[PENDING]` |

## Acciones (Workstream `REPLICATION`)
| Paso | Acción | Owner | Por ambiente |
|------|--------|-------|-------------|
| 6.1 | Repetir Fase 2 (Entra ID) — misma App, ajustar ACS URL | Entra ID Admin | 1 vez (Entra ID no cambia por ambiente) |
| 6.2 | Repetir Fase 3 (IAS) — copiar config, ajustar EntityID/ACS por ambiente | IAS Admin | 1 por ambiente |
| 6.3 | Repetir Fase 4 (S/4HANA) — distinto SID, cliente, certificados | Basis | 1 por ambiente |
| 6.4 | Ejecutar Fase 5 (pruebas) en cada ambiente | POC Lead | 1 por ambiente |
| 6.5 | Documentar diferencias por ambiente | POC Lead | 1 por ambiente |

## Entregable Fase 6
- [ ] QAS configurado + probado
- [ ] PRD configurado + probado (si aplica)
- [ ] Diferencias por ambiente documentadas

---

# FASE 7 — ENTREGA A DANIEL EGAS

## Entregables Finales
| Entregable | Formato | Ubicación |
|------------|---------|-----------|
| Tabla Ejecutiva de Configuración | Markdown / Excel | `02_executive_plan/executive_config_table.md` |
| Runbooks por Workstream | Markdown | `03_workstream_docs/` |
| Evidencias de Pruebas | Screenshots, Traces, Logs | `04_evidence/` |
| Criterios de Aceptación cumplidos | Checklist | `05_handoff/` |
| Lessons Learned | Markdown | `05_handoff/lessons_learned.md` |

## Criterios de Aceptación del POC
- [ ] Login SSO con MFA exitoso en Fiori
- [ ] Correlación correcta Logon Alias → usuario SAP
- [ ] SLO funciona (si aplica)
- [ ] Errores manejados (cert, alias, clock skew)
- [ ] Evidencia documentada de cada prueba
- [ ] Daniel Egas aprueba

---

# RESUMEN: PREGUNTAS PENDIENTES (Todas las Fases)

| Fase | # | Pregunta | Estado |
|------|---|----------|--------|
| 0 | 0.1 | ¿Quién es tu Basis de DEV? | `[PENDING]` |
| 0 | 0.2 | ¿Quién es tu IAS Admin? | `[PENDING]` |
| 0 | 0.3 | ¿Quién es tu Entra ID Admin? | `[PENDING]` |
| 0 | 0.4 | ¿Qué sistema de tickets usas? | `[PENDING]` |
| 0 | 0.5 | ¿Tienes VPN? | `[PENDING]` |
| 0 | 0.6 | ¿Tienes SAP GUI instalado? | `[PENDING]` |
| 0 | 0.7 | ¿Tienes licencia SAP asignada? | `[PENDING]` |
| 1 | 1.1 | SID del sistema DEV | `[PENDING]` |
| 1 | 1.2 | Cliente(s) en la POC | `[PENDING]` |
| 1 | 1.3 | URL Fiori Launchpad DEV | `[PENDING]` |
| 1 | 1.4 | ¿Existe `SAML2` o `SAML_CONFIG`? | `[PENDING]` |
| 1 | 1.5 | ¿Certificado SAML ya existe? | `[PENDING]` |
| 1 | 1.6 | ¿ICF activo? | `[PENDING]` |
| 1 | 1.7 | ¿Parámetros SSO2 seteados? | `[PENDING]` |
| 1 | 1.8 | Permiso IAS Console | `[PENDING]` |
| 1 | 1.9 | Permiso Entra ID | `[PENDING]` |
| 1 | 1.10 | Permiso S/4HANA | `[PENDING]` |
| 1 | 1.11 | **Decisión**: Logon Alias | `[PENDING]` |
| 1 | 1.12 | **Decisión**: Routing | `[PENDING]` |
| 1 | 1.13 | **Decisión**: Certificados | `[PENDING]` |
| 1 | 1.14 | **Decisión**: SLO | `[PENDING]` |
| 1 | 1.15 | **Decisión**: NameID Format | `[PENDING]` |
| 1 | 1.16 | ¿Enterprise App ya existe? | `[PENDING]` |
| 1 | 1.17 | ¿Tenant IAS virgen? | `[PENDING]` |
| 1 | 1.18 | ¿Usuarios prueba en Entra ID? | `[PENDING]` |
| 1 | 1.19 | ¿Usuarios prueba en S/4HANA? | `[PENDING]` |
| 1 | 1.20 | ¿Proxy/firewall/WAF? | `[PENDING]` |
| 1 | 1.21 | ¿Intentos previos de SSO? | `[PENDING]` |
| 2 | 2.1 | Tenant ID Entra ID | `[PENDING]` |
| 2 | 2.2 | Dominio corporativo | `[PENDING]` |
| 2 | 2.3 | Política MFA existente? | `[PENDING]` |
| 3 | 3.1 | URL tenant IAS | `[PENDING]` |
| 3 | 3.2 | EntityID acordado | `[PENDING]` |
| 4 | 4.1 | Basis asignado | `[PENDING]` |
| 4 | 4.2 | Usuarios prueba S/4HANA existen? | `[PENDING]` |
| 4 | 4.3 | Roles Fiori asignados? | `[PENDING]` |
| 5 | 5.1 | Navegadores a probar | `[PENDING]` |
| 5 | 5.2 | Dispositivos a probar | `[PENDING]` |
| 6 | 6.1 | QAS entra en POC? | `[PENDING]` |
| 6 | 6.2 | PRD entra en POC? | `[PENDING]` |
| 6 | 6.3 | Change Request para PRD? | `[PENDING]` |
| 6 | 6.4 | Ventana cambio PRD? | `[PENDING]` |
| 6 | 6.5 | Usuarios prueba en QAS/PRD? | `[PENDING]` |

---

# ORDEN RECOMENDADO DE RESPUESTA (Para ti ahora)

```
PASO 1 → Fase 0: Contactos + pedidos de acceso (0.1–0.7)
PASO 2 → Fase 1: Topología con Basis (1.1–1.7)
PASO 3 → Fase 1: Decisiones técnicas (1.11–1.15)
PASO 4 → Fase 1: Contexto existente (1.16–1.21)
PASO 5 → El resto se resuelve WHILE YOU CONFIGURE (Fases 2–6)
```

> **Con Paso 1 + 2 + 3 respondidos (~15 preguntas)**, puedo generar la tabla ejecutiva completa con los `[PENDING]` restantes como placeholders.