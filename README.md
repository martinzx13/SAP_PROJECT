# POC SSO: SAP S/4HANA Fiori + IAS + Microsoft Entra ID

## Descripción
Prueba de Concepto (POC) para Single Sign-On (SSO) en SAP S/4HANA Fiori utilizando:
- **SAP Cloud Identity Services - Identity Authentication (IAS)** como broker de identidad
- **Microsoft Entra ID** (antes Azure AD) como Corporate IdP con MFA corporativo
- **SAP S/4HANA** (on-premise) consumiendo assertion SAML 2.0 vía Logon Alias

## Arquitectura Objetivo
```
Usuario → Fiori Launchpad (S/4HANA) → IAS (SP-initiated) 
    → Entra ID (IdP + MFA) → IAS (ACS) → S/4HANA (Logon Alias) → Sesión Fiori
```

## Alcance (In-Scope)
- ✅ SP-initiated SAML 2.0 flow
- ✅ Federación IAS ↔ Entra ID (Corporate IdP)
- ✅ Federación IAS ↔ S/4HANA (Trusted Provider)
- ✅ MFA via Entra ID Conditional Access
- ✅ Correlación usuario via Logon Alias (`USRACLEXT`/`USRACL`)
- ✅ Pruebas SSO, MFA, SLO, casos de error
- ✅ Replicación DEV → QAS → PRD

## Fuera de Alcance (Out-of-Scope)
- ❌ SAP BTP / Cloud Foundry / Kyma
- ❌ IAM / IAG / IPS (Identity Provisioning/Governance)
- ❌ Aprovisionamiento automático de usuarios (SCIM/JIT)
- ❌ Asignación automática de roles (PFCG)
- ❌ OIDC (solo SAML 2.0 para Fiori on-prem)

## Estructura del Repositorio
```
POC_IAS_EntraID_SSO/
├── README.md                 # Este archivo
├── plan_paso.md             # Plan paso a paso (guía metodológica)
├── questions.md             # Preguntas de clarificación (responder ANTES de ejecutar)
├── 01_context/              # Contexto crudo (inputs originales)
│   ├── raw_activities.md    # Actividades originales sin estructurar
│   └── meeting_notes.md     # Notas de reuniones con Daniel Egas
├── 02_executive_plan/       # Entregable final para liderazgo
│   └── executive_config_table.md  # Tabla ejecutiva (columns spec)
├── 03_workstream_docs/      # Documentación por workstream
│   ├── ENTRA_ID_APP.md
│   ├── IAS_ENTRA_METADATA.md
│   ├── IAS_APPLICATION.md
│   ├── IAS_S4_METADATA.md
│   ├── S4_CONFIG.md
│   ├── TESTING.md
│   └── REPLICATION.md
├── 04_evidence/             # Evidencias de ejecución
│   ├── screenshots/
│   ├── saml_traces/
│   └── test_results/
└── 05_handoff/              # Para siguiente fase / rollout
    └── lessons_learned.md
```

## Roles y Responsables
| Rol | Responsabilidad | Contacto |
|-----|-----------------|----------|
| **POC Lead** | Coordinar, ejecutar tareas con permiso, documentar, reportar | Juan Martín |
| **IAS Admin** | Tenant IAS: apps, Corporate IdP, Trusted Provider, Routing, Certs | `[PENDING]` |
| **Entra ID Admin** | Enterprise App, SAML/OIDC, Claims, CA MFA, Usuarios/Grupos | `[PENDING]` |
| **Basis S/4HANA** | `SAML2`, `RZ10`, `SU01`, `PFCG`, `STRUST`, ICF services | `[PENDING]` |
| **Seguridad/Compliance** | Validar certificados, políticas, auditoría | `[PENDING]` |

## Estado Actual
- [x] Contexto y arquitectura confirmados
- [x] Plan metodológico creado (`plan_paso.md`)
- [x] Preguntas de clarificación listadas (`questions.md`)
- [ ] **PENDIENTE**: Respuestas a preguntas de clarificación
- [ ] **PENDIENTE**: Tabla ejecutiva desglosada (entregable a Daniel Egas)

## Próximos Pasos Inmediatos
1. **Completar `questions.md`** con respuestas reales (Secciones A–E + 6–15)
2. **Validar con owners** (IAS Admin, Entra ID Admin, Basis) permisos y contactos
3. **Generar tabla ejecutiva** (~25-30 filas atómicas) en `02_executive_plan/`
4. **Revisar con Daniel Egas** antes de ejecución

## Documentación de Referencia
| Fuente | Enlaces Clave |
|--------|---------------|
| **SAP Help Portal - IAS** | [Corporate IdP](https://help.sap.com/docs/identity-authentication/identity-authentication/configure-corporate-identity-providers) \| [Trusted Provider](https://help.sap.com/docs/identity-authentication/identity-authentication/configure-trusted-providers) \| [Applications](https://help.sap.com/docs/identity-authentication/identity-authentication/create-applications) |
| **Microsoft Learn** | [Tutorial SAP IAS + Entra ID](https://learn.microsoft.com/en-us/entra/identity/saas-apps/sap-cloud-identity-services-tutorial) \| [SAML SSO Config](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-saml-single-sign-on) |
| **S/4HANA SAML2** | [SAML2 Configuration](https://help.sap.com/docs/sap-s4hana/sap-s4hana/saml2-configuration) |
| **SAP Notes** | 2937897, 2716552, 2480207 |

## Convenciones
- **Fechas**: `YYYY-MM-DD` o `[PENDING]`
- **Tiempos**: Minutos enteros (ej: `45`, `120`) o `[PENDING]`
- **Responsable**: Solo `POC Lead` \| `IAS Admin` \| `Entra ID Admin` \| `Basis` \| `TBD`
- **Status**: Solo `Not Started` \| `In Progress` \| `Done` \| `Blocked`
- **Observación**: Debe incluir `🔗 Depends on:`, `📖 Doc:`, `✅ Acceptance:`, `📎 Evidence:`

## Contacto
**POC Lead**: Juan Martín  
**Líder Solicitante**: Daniel Egas  
**Fecha Inicio Planificación**: 2026-10-14