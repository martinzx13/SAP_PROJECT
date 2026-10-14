# Preguntas de Clarificación — POC SSO IAS + Entra ID + S/4HANA Fiori

> **Instrucciones**: Completa lo que sepas. Marca `[PENDING]` lo que no. Yo uso esto para generar la tabla ejecutiva.
> **Fecha**: 2026-10-14
> **Responsable**: Juan Martín (POC Lead)

---

## SECCIÓN A: Topología de Ambientes

| # | Pregunta | Respuesta |
|---|----------|-----------|
| A1 | ¿Cuántos ambientes entran en la POC? (DEV / QAS / PRD / otros) | |
| A2 | Por ambiente: **SID** del sistema S/4HANA (ej: `D01`, `Q01`, `P01`) | |
| A3 | Por ambiente: **Clientes/Mandantes** incluidos (ej: DEV=100, QAS=200, PRD=300) | |
| A4 | ¿Todos los mandantes por ambiente o solo subset? | |
| A5 | ¿URL base Fiori por ambiente? (ej: `https://fiori-dev.company.com`, `https://fiori-qas.company.com`) | |
| A6 | ¿Tenant IAS único para todos o tenant por ambiente? | |
| A7 | ¿Entra ID tenant único (producción) o hay tenant de prueba? | |

---

## SECCIÓN B: Tus Permisos Reales (CRÍTICO para asignar `Responsable`)

> Marca: **Sí** = tienes acceso y permiso para configurar / **Parcial** = ves pero no guardas / **No** = sin acceso

| Consola / Transacción | ¿Acceso? | Rol/Permiso Específico | Notas / Limitaciones |
|----------------------|----------|------------------------|---------------------|
| **IAS Admin Console** → Applications (Crear/Editar App) | Sí / Parcial / No | | |
| **IAS Admin Console** → Corporate IdP (Configurar Entra ID) | Sí / Parcial / No | | |
| **IAS Admin Console** → Trusted Provider (Configurar S/4HANA) | Sí / Parcial / No | | |
| **IAS Admin Console** → Attribute Transformation / Routing Rules | Sí / Parcial / No | | |
| **IAS Admin Console** → Certificates / Risk-based Auth | Sí / Parcial / No | | |
| **Entra ID Portal** → Enterprise Applications (Crear/Configurar SAML) | Sí / Parcial / No | | |
| **Entra ID Portal** → Claims Mapping (UPN, email, employeeId, groups) | Sí / Parcial / No | | |
| **Entra ID Portal** → Conditional Access (MFA Policies) | Sí / Parcial / No | | |
| **Entra ID Portal** → Users/Groups (Crear usuarios prueba, asignar app) | Sí / Parcial / No | | |
| **Entra ID Portal** → Certificates & Secrets (Subir cert IAS) | Sí / Parcial / No | | |
| **S/4HANA `SAML2`** → Local Provider / Trusted Provider / Certificates | Sí / Parcial / No | | |
| **S/4HANA `SU01`** → Crear usuario, Logon Alias (`USRACLEXT`/`USRACL`) | Sí / Parcial / No | | |
| **S/4HANA `RZ10`** → Perfil params SSO2 (`login/create_sso2_ticket`, etc.) | Sí / Parcial / No | | |
| **S/4HANA `PFCG`** → Roles Fiori para usuarios prueba | Sí / Parcial / No | | |
| **S/4HANA `STRUST`** / `SAML2` → Generar/Importar certificados SAML | Sí / Parcial / No | | |
| **S/4HANA ICF** → Servicio `/sap/public/bc/sec/saml2` (activar/trace) | Sí / Parcial / No | | |

> **Regla**: Si **No** o **Parcial** → `Responsable = IAS Admin` / `Entra ID Admin` / `Basis` / `TBD` en la tabla.
> Si **Sí** → `Responsable = POC Lead`.

---

## SECCIÓN C: Decisiones Técnicas Pendientes

| # | Decisión | Opciones | Tu Decisión / `[PENDING]` |
|---|----------|----------|---------------------------|
| C1 | **Logon Alias** (valor que IAS envía a S/4HANA para mapear usuario) | `samAccountName` (`edgar.chacon`) / `userPrincipalName` (`edgar.chacon@corp.com`) / `employeeId` / `mail` / Otro | |
| C2 | **Routing Rule IAS** (qué usuarios van a Entra ID vs IAS local) | Todos → Entra ID / Por dominio (`@corp.com` → Entra ID) / Por grupo Entra ID / Risk-based / Hybrid | |
| C3 | **Certificados SAML** | IAS-managed (auto-rotación 90d) / Customer-managed (tú subes PFX, tú rotas) | |
| C4 | **Protocolo** | SAML 2.0 (estándar Fiori on-prem) / OIDC (poco común) | SAML 2.0 |
| C5 | **Iniciación SSO** | SP-initiated (usuario abre Fiori URL) / IdP-initiated (MyApps/Entra ID) | SP-initiated |
| C6 | **MFA** | Entra ID Conditional Access (política corp) / IAS Risk-based / Ambos en cascada | Entra ID CA |
| C7 | **Single Logout (SLO)** | Requerido (propagar logout a Entra ID) / No requerido / Solo local IAS | |
| C8 | **NameID Format** SAML | `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` / `emailAddress` / `persistent` / `transient` | |
| C9 | **Attribute Transformation IAS** | Regla simple (passthrough) / Regla compleja (regex, concatenación, lookup) | |
| C10 | **EntityID Naming Convention** | ¿Hay estándar corporativo? (ej: `urn:sap:ias:<tenant>:<app>`) | |

---

## SECCIÓN D: Contactos Técnicos (Nombres Reales)

| Rol | Nombre | Email / Teams | Teléfono | Confirmado? | Notas |
|-----|--------|---------------|----------|-------------|-------|
| **IAS Admin** | | | | Sí / No | |
| **Entra ID Admin** | | | | Sí / No | |
| **Basis S/4HANA (DEV)** | | | | Sí / No | |
| **Basis S/4HANA (QAS)** | | | | Sí / No | |
| **Basis S/4HANA (PRD)** | | | | Sí / No | |
| **Seguridad / Compliance** | | | | Sí / No | Certificados, políticas |
| **Red / Firewall / Proxy** | | | | Sí / No | URLs, puertos, certificados |
| **Líder Solicitante** | Daniel Egas | | | Sí | Validación final |

---

## SECCIÓN E: Fechas y Restricciones

| Ítem | Valor / `[PENDING]` |
|------|---------------------|
| Fecha objetivo **inicio** configuración DEV | |
| Fecha objetivo **fin** POC DEV (entrega a Daniel) | |
| Ventanas de cambio permitidas (horarios/días) | |
| ¿Freeze periods? (ej: fin de mes, cerrados contables) | |
| ¿Lead time para aprobaciones de cambio (Basis/Seguridad)? | |
| ¿Fecha límite duro por compliance/auditoría? | |

---

## PREGUNTAS TÉCNICAS ADICIONALES (6–15)

| # | Pregunta | Respuesta / `[PENDING]` | Impacto en Tabla |
|---|----------|------------------------|------------------|
| 6 | ¿Ya existe **Enterprise App en Entra ID** para esta POC o hay que crearlo desde cero? | | Si existe → quitar filas "crear app", solo "configurar claims/usuarios" |
| 7 | ¿El **tenant IAS ya tiene aplicaciones** configuradas o es virgen? | | Afecta conflictos EntityID, certs existentes, naming |
| 8 | ¿Los **usuarios de prueba ya existen en Entra ID**? (cloud-only / hybrid sync / guest) | | Si no → filas "crear usuarios prueba en Entra ID + asignar app" |
| 9 | ¿S/4HANA usa **`SAML2` t-code (nuevo, recomendado)** o **SAML clásico (antiguo, `SAML_CONFIG`)**? | | Cambia pasos Basis completamente |
| 10 | ¿Hay **proxy / firewall / WAF / Load Balancer** entre Fiori e IAS / Entra ID? | | Afecta URLs ACS/Logout, certificados (SAN), timeouts, headers |
| 11 | ¿Qué **navegadores/dispositivos** deben probarse? (Win domain-joined, Mac, Mobile, Safari, Chrome, Edge) | | Afecta casos prueba MFA (Windows Hello, Authenticator, SMS, FIDO2) |
| 12 | ¿Se requiere **Single Logout (SLO)** propagado a Entra ID? | | Si sí → filas extra config SLO en IAS, Entra ID, S/4HANA |
| 13 | ¿Hay **política de naming** para EntityID, Application Name, Certificados en IAS? | | Evita retrabajo; documentar en `Observación` |
| 14 | ¿El **certificado de firma SAML de S/4HANA** ya existe (`STRUST`/`SAML2`) o hay que generarlo? | | Si no → filas Basis: generar cert, exportar, importar en IAS |
| 15 | ¿Daniel quiere **una tabla consolidada** (columna `Ambiente/Mandante`) o **tablas separadas por ambiente**? | | Cambia estructura final de entregable |
| 16 | ¿Los **usuarios de prueba en S/4HANA** ya existen o hay que crearlos en cada mandante? | | Si no → filas `SU01` por mandante/ambiente |
| 17 | ¿Hay **roles Fiori específicos** que deben tener los usuarios de prueba? (ej: `SAP_FIORI_FOUNDATION`, roles negocio) | | Si sí → filas `PFCG` por ambiente |
| 18 | ¿Se usa **SAP Cloud Connector** o conexión directa on-prem? | | Afecta URLs ACS, certificados, network |
| 19 | ¿Qué **idioma/región** para pruebas? (afecta formato fecha, número en Fiori) | | Casos de prueba localizados |
| 20 | ¿Hay **documentación previa** de intentos SSO fallidos? (logs, errores, lessons learned) | | Evita repetir errores; incluir en `Observación` |

---

## PLANTILLA DE RESPUESTA RÁPIDA (Copiar-Pegar)

```markdown
## RESPUESTAS - Juan Martín - 2026-10-14

### A. Ambientes
A1: DEV, QAS, PRD
A2: DEV=D01, QAS=Q01, PRD=P01
A3: DEV=100, QAS=200, PRD=300
A4: Solo mandante 100 en DEV, 200 en QAS, 300 en PRD
A5: https://fiori-dev.company.com / https://fiori-qas.company.com / https://fiori-prd.company.com
A6: Tenant IAS único: `miempresa.accounts.ondemand.com`
A7: Entra ID tenant producción: `miempresa.onmicrosoft.com`

### B. Permisos (ejemplo)
IAS Admin Console - Applications: Sí - IAS Administrator
Entra ID - Enterprise Apps: Parcial - Application Admin (no Global Admin)
S/4HANA SAML2: No - Requiere Basis
...

### C. Decisiones Técnicas
C1: samAccountName (edgar.chacon)
C2: Todos → Entra ID
C3: IAS-managed
C7: Sí, SLO requerido
...

### D. Contactos
IAS Admin: María González / maria.gonzalez@company.com / Confirmado: Sí
Entra ID Admin: Carlos Ruiz / carlos.ruiz@company.com / Confirmado: No (pendiente)
Basis DEV: Pedro López / pedro.lopez@company.com / Confirmado: Sí
...

### E. Fechas
Inicio DEV: 2026-10-21
Fin DEV: 2026-11-04
Ventanas: Martes/Jueves 14:00-18:00
Freeze: 25-30 cada mes
...

### Preguntas 6-20
6: No existe, crear desde cero
7: Virgen, primera app
8: Usuarios prueba no existen, crear 3 por ambiente
9: SAML2 t-code (S/4HANA 2022+)
10: Sí, proxy corporativo - URLs públicas distintas a internas
11: Chrome/Edge en Windows domain-joined + Safari en iPhone
12: Sí, SLO completo
13: Convención: urn:sap:ias:miempresa:<app>-<env>
14: Certificado no existe, generar en STRUST
15: Tabla consolidada con columna Ambiente/Mandante
16: No existen, crear en SU01
17: SAP_FIORI_FOUNDATION + Z_ROLE_POC_FIORI
18: Cloud Conector activo
19: ES/ES (español España)
20: Intento previo falló por cert mismatch (resuelto regenerando)
```

---

## CHECKLIST DE COMPLETITUD

Antes de pasar a generación de tabla:

- [ ] **Sección A**: Todos los ambientes, SIDs, clientes, URLs definidos
- [ ] **Sección B**: Tu acceso real verificado consola por consola
- [ ] **Sección C**: Decisiones C1–C10 resueltas o `[PENDING]` consciente
- [ ] **Sección D**: Al menos 1 contacto confirmado por rol crítico (IAS, Entra ID, Basis)
- [ ] **Sección E**: Fechas objetivo y ventanas de cambio
- [ ] **Preguntas 6–20**: Respondidas según conocimiento actual

> **Nota**: Está bien tener `[PENDING]`. La tabla se genera con placeholders y se refina en validación con owners.