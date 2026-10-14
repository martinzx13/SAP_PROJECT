# Plan Paso a Paso: Crear Tabla Ejecutiva de Configuración POC SSO

> **Objetivo**: Entregar a Daniel Egas una tabla ejecutiva con columnas exactas:
> `Workstream | TimeBox | Nombre de tarea | Descripción | Status | Responsable | Tiempo Estimado (min) | Fecha inicio | Fecha fin | Observación`
>
> Cada fila = **una acción concreta de configuración** (clic/guardar en una consola).

---

## 📋 FASE 0: Recopilar Contexto Faltante (¡PRIMER PASO OBLIGATORIO!)

**No avances a Fase 1 sin completar `questions.md`**.

### Checklist de Entrada
- [ ] Sección A: Topología de Ambientes (DEV/QAS/PRD, SIDs, clientes)
- [ ] Sección B: Tus Permisos Reales (consola por consola)
- [ ] Sección C: Decisiones Técnicas (Logon Alias, Routing, Certs, MFA)
- [ ] Sección D: Contactos Técnicos (nombres, emails, confirmados)
- [ ] Sección E: Fechas y Restricciones
- [ ] Preguntas 6–15: Contexto técnico adicional

> **Truco**: Marca `[PENDING]` lo que no sepas. Yo genero la tabla con placeholders; luego refinamos con owners.

---

## 📋 FASE 1: Descomponer en Tareas Atómicas (El "Método")

### Regla de Oro
> **Una fila = un clic/guardar en UNA consola por UN responsable**

### Método de Descomposición (Aplicar a cada actividad macro)

| Pregunta Guía | Acción |
|---------------|--------|
| ¿En qué consola se hace? | IAS Admin / Entra ID Portal / S/4HANA (`SAML2`, `SU01`, `RZ10`, `PFCG`, `STRUST`) |
| ¿Qué clics exactos? | "Crear Application → Tipo Trusted Provider → Guardar" |
| ¿Qué parámetros se setean? | EntityID, ACS URL, NameID Format, Attribute Mapping, Cert thumbprint |
| ¿Quién tiene permiso para ese clic? | `POC Lead` / `IAS Admin` / `Entra ID Admin` / `Basis` / `TBD` |
| ¿Qué debe existir ANTES? | Dependency: metadata XML, certificado, usuario de prueba, EntityID acordado |
| ¿Cómo se valida que quedó bien? | SAML trace, test login, log entry, screenshot |

### Mapeo de tus 7 Actividades Macro → Workstreams Atómicos

| Tu Actividad Macro | Workstream(s) Destino | Ejemplo de Filas Atómicas |
|-------------------|----------------------|---------------------------|
| Configuración SAML2 por ambiente/mandante | `IAS_APPLICATION` | Crear App Trusted Provider, Config SAML2, Subir cert S/4, Attribute Transform, Routing Rule |
| Intercambio metadata IAS ↔ S/4HANA | `IAS_S4_METADATA` | Export IAS SP meta, Import en `SAML2` Local Provider, Export S/4 meta, Import en IAS Trusted Provider, Certificados |
| Intercambio metadata IAS ↔ Entra ID | `IAS_ENTRA_METADATA` + `ENTRA_ID_APP` | Descargar Entra ID meta → Subir a IAS Corporate IdP, Subir IAS SP meta a Entra ID, Mapear claims (UPN, email, employeeId), Asignar usuarios |
| Configuración permisos CIS ID | `IAS_APPLICATION` | App Roles en IAS, Asignar grupos Entra ID → roles IAS, Conditional Access / Risk-based |
| Usuario con Alias | `S4_CONFIG` | Crear usuario prueba `SU01`, Set Logon Alias `USRACLEXT`, Verificar en `SAML2` trace |
| Pruebas SSO + MFA | `TESTING` | SP-init Fiori URL → MFA → Sesión, IdP-init MyApps, SLO propagación, Errores (cert expiry, clock skew, attr mismatch) |
| Replicar a QAS/PRD | `REPLICATION` | Export config IAS, Repetir metadata por ambiente, Ajustar EntityID/ACS URLs, Repetir usuarios, Suite pruebas por ambiente |

### Workstreams Estándar (Orden de Dependencias)

```
1. ENTRA_ID_APP        → Enterprise App, Claims, CA MFA, Usuarios prueba
2. IAS_ENTRA_METADATA  → Metadata bidireccional IAS↔Entra ID
3. IAS_APPLICATION     → App Trusted Provider, SAML2, Attr Transform, Routing
4. IAS_S4_METADATA     → Metadata bidireccional IAS↔S/4HANA
5. S4_CONFIG           → SAML2 Local/Trusted Provider, RZ10, SU01 usuarios
6. TESTING             → Casos prueba SSO, MFA, SLO, Errores
7. REPLICATION         → Replicar DEV→QAS→PRD por mandante
```

---

## 📋 FASE 2: Construir la Tabla Ejecutiva

### Plantilla de Fila (Copiar-Pegar por cada tarea)

```markdown
| WORKSTREAM | TimeBox | Nombre de tarea | Descripción | Status | Responsable | Tiempo (min) | Fecha inicio | Fecha fin | Observación |
|------------|---------|-----------------|-------------|--------|-------------|--------------|--------------|-----------|-------------|
| IAS_ENTRA_METADATA | Sprint 1 | Subir metadata Entra ID a IAS Corporate IdP | En IAS Admin Console > Applications > Corporate IdP: upload metadata XML descargado de Entra ID Enterprise App. Validar EntityID match, endpoints SSO/Logout, cert thumbprint. | Not Started | IAS Admin | 30 | [PENDING] | [PENDING] | 🔗 Depends on: ENTRA_ID_APP#3 (metadata XML listo en Entra ID)\n📖 Doc: https://help.sap.com/docs/identity-authentication/identity-authentication/configure-corporate-identity-providers\n✅ Acceptance: IAS muestra "Corporate IdP configured" + EntityID coincide\n📎 Evidence: Screenshot IAS Corporate IdP config |
```

### Validaciones Obligatorias por Fila

- [ ] **Nombre de tarea** = Verbo imperativo + objeto concreto (`Subir`, `Crear`, `Configurar`, `Asignar`, `Probar`)
- [ ] **Descripción** = Qué + Dónde + Parámetros clave (copiable a runbook)
- [ ] **Responsable** ∈ {`POC Lead`, `IAS Admin`, `Entra ID Admin`, `Basis`, `TBD`}
- [ ] **Tiempo (min)** = Entero (ej: `45`) o `[PENDING]`
- [ ] **Fechas** = `YYYY-MM-DD` o `[PENDING]`
- [ ] **Observación** tiene los 4 bloques: `🔗 Depends on:`, `📖 Doc:`, `✅ Acceptance:`, `📎 Evidence:`

---

## 📋 FASE 3: Citar Documentación Oficial (Por Workstream)

| Workstream | SAP Help Portal | Microsoft Learn | SAP Notes / Learning |
|------------|-----------------|-----------------|---------------------|
| `ENTRA_ID_APP` | — | [Tutorial SAP IAS + Entra ID](https://learn.microsoft.com/en-us/entra/identity/saas-apps/sap-cloud-identity-services-tutorial) | — |
| `IAS_ENTRA_METADATA` | [Corporate IdP](https://help.sap.com/docs/identity-authentication/identity-authentication/configure-corporate-identity-providers) | [SAML SSO Config](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-saml-single-sign-on) | SAP Note 2937897 |
| `IAS_APPLICATION` | [Create Application](https://help.sap.com/docs/identity-authentication/identity-authentication/create-applications) \| [Attribute Transformation](https://help.sap.com/docs/identity-authentication/identity-authentication/attribute-transformation) \| [Routing Rules](https://help.sap.com/docs/identity-authentication/identity-authentication/routing-rules) | — | SAP Learning: "Configuring Applications in IAS" |
| `IAS_S4_METADATA` | [Trusted Provider](https://help.sap.com/docs/identity-authentication/identity-authentication/configure-trusted-providers) | — | SAP Note 2716552, 2480207 |
| `S4_CONFIG` | [S/4HANA SAML2](https://help.sap.com/docs/sap-s4hana/sap-s4hana/saml2-configuration) | — | `SAML2` t-code, `RZ10` params (`login/create_sso2_ticket`, `login/accept_sso2_ticket`, `login/ticket_only_by_https`) |
| `TESTING` | [Test SSO](https://help.sap.com/docs/identity-authentication/identity-authentication/test-single-sign-on) | [Test SSO Entra ID](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/test-single-sign-on) | — |

---

## 📋 FASE 4: Validación Final (Checklist Pre-Entrega)

Antes de enviar a Daniel Egas:

- [ ] **Todas las filas son atómicas** (no "configurar", "verificar", "validar", "revisar")
- [ ] **Responsable** usa solo los 5 valores permitidos — nada de "Equipo Seguridad"
- [ ] **Tiempo en minutos** enteros, `[PENDING]` donde falte info
- [ ] **Fechas** `YYYY-MM-DD` o `[PENDING]`
- [ ] **Observación** completa: `🔗 Depends on:`, `📖 Doc:`, `✅ Acceptance:`, `📎 Evidence:`
- [ ] **Dependencias respetadas**: Metadata → Config → Test → Replicación
- [ ] **Workstreams cubiertos**: 7 workstreams estándar
- [ ] **Out-of-scope excluido**: BTP, IAM/IAG/IPS, auto-provisioning, roles auto
- [ ] **Tabla en markdown** copiable a Excel/Teams/Word sin perder formato

---

## 📋 FASE 5: Entrega y Seguimiento

### Entregables
1. **`02_executive_plan/executive_config_table.md`** → Tabla final para Daniel
2. **`03_workstream_docs/`** → Detalle por workstream (runbooks para ejecutores)
3. **`04_evidence/`** → Evidencias durante ejecución (screenshots, traces, logs)

### Cadencia de Actualización
| Evento | Acción |
|--------|--------|
| Inicio tarea | `Status: In Progress`, `Fecha inicio: hoy` |
| Fin tarea | `Status: Done`, `Fecha fin: hoy`, `📎 Evidence: link/ruta` |
| Bloqueo | `Status: Blocked`, `Observación: 🔗 Blocked by: ...` |
| Cambio owner | Actualizar `Responsable`, notificar al nuevo owner |
| Nueva info (fechas, ambientes) | Reemplazar `[PENDING]` → valor real |

### Comunicación con Daniel Egas
- **Formato**: Tabla markdown (este repo) + export Excel si pide
- **Frecuencia**: Semanal o por hito (fin DEV, fin QAS, fin PRD)
- **Métricas**: % filas `Done` por workstream, # `Blocked`, días vs plan

---

## 🎓 Tips para Principiantes (Mentor Notes)

> **"No intentes adivinar — pregunta al owner"**
> - Si no sabes quién configura `SAML2` en S/4HANA → `Responsable: TBD` + `🔗 Depends on: Confirmar Basis owner`
> - Mejor `[PENDING]` honesto que fecha inventada que rompe confianza

> **"Documenta mientras configuras, no al final"**
> - Cada `Done` debe tener `📎 Evidence: screenshot/trace/log` en la fila
> - Ahorra horas de "¿qué hiciste?" después

> **"Una tabla viva > un PDF perfecto"**
> - Daniel verá avances reales en `Status` y `Fecha fin`
> - Los owners verán sus tareas (`Responsable = ellos`) y dependencias

> **"SAML 2.0 es determinista — si falla, es config, no magia"**
> - Trace SAML (`SAML2` → Trace) te dice EXACTAMENTE qué atributo llega
> - Compara: Entra ID claim → IAS transformation → Assertion → Logon Alias esperado

---

## 🗓️ Cronograma Sugerido Para Ti

| Día | Actividad | Salida |
|-----|-----------|--------|
| **Día 1 (Hoy)** | Completar `questions.md` (todo lo que sepas) | Contexto completo |
| **Día 2** | Validar permisos/contactos con owners | Owners confirmados |
| **Día 3** | Yo genero tabla borrador (~25-30 filas) | `executive_config_table.md` v0.1 |
| **Día 4** | Revisas con owners, ajustas tiempos/owners | v0.2 validado |
| **Día 5** | Entrega final a Daniel Egas | **v1.0 Aprobada** |
| **Semana 2** | Ejecución DEV (siguiendo tabla) | Evidencias en `04_evidence/` |
| **Semana 3-4** | Replicación QAS → PRD | Tablas por ambiente |

---

## 🚀 Próxima Acción Inmediata

> **Abre `questions.md` y completa lo que sepas ahora.**
>
> Lo que no sepas → `[PENDING]`.
>
> Yo uso tus respuestas para generar la tabla completa en Fase 2.