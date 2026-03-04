### 5.5 Credenciales Verificables y Control de Acceso

| Rol | Tipo VC | Emisor | Wallet | Flujo de acceso |
|---|---|---|---|---|
| Estudiante | `StudentCredential` | VC Issuer · CONNECTOR_CFP | No requerida | Accede vía LMS → APP_LTI directamente. Simplificación deliberada del MVP |
| Docente | `TeacherCredential` | VC Issuer · CONNECTOR_CFP | 💼 Wallet Docente | Presenta VC vía Auth Portal → Verifier de CONNECTOR_CFP · Accede a APP_LTI y panel de clase · Configura SCs |
| Operador | `OperatorCredential` | VC Issuer · CONNECTOR_CENTRAL | 💼 Wallet Operador | Presenta VC vía Auth Portal → Verifier de CONNECTOR_CENTRAL · Gestiona servicio · Supervisa observabilidad |
| Investigador | `ResearcherCredential` | VC Issuer · CONNECTOR_CENTRAL | 💼 Wallet Investigador | Presenta VC vía Auth Portal → Verifier de CONNECTOR_CENTRAL · Acceso de solo lectura a datos agregados NGSI-LD |

Todas las VCs siguen el estándar **W3C Verifiable Credentials** y son compatibles con **eIDAS 2.0**. El flujo de autenticación para Docente, Operador e Investigador sigue el protocolo **OIDC4VP**: la Wallet presenta la VC al **Auth Portal** del conector correspondiente, que orquesta la verificación con el **Verifier**, el cual valida la credencial contra la **Local Trusted Issuers List** local y los servicios globales del dataspace (**Data Space Participants Registry**, **Global Trusted Issuers List** y **Revocation List**). Una vez verificada la identidad, el **PEP (APISIX)** intercepta la request y delega la decisión de autorización en el **PDP (OPA)**, que aplica la política ODRL asociada al producto.

El acceso del **Estudiante** opera en esta primera fase sin Wallet ni VC, autenticándose exclusivamente a través del LMS mediante las credenciales institucionales del centro. Esta simplificación deliberada del MVP se abordará en fases posteriores, cuando se integre la presentación de `StudentCredential` en el flujo LTI.
