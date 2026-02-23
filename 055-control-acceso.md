### 5.5 Credenciales Verificables y Control de Acceso

| Rol | Tipo VC | Emisor | Acceso concedido |
|---|---|---|---|
| Estudiante | `StudentCredential` | Centro FP | Resolver SCs · Ver panel competencial y resultados propios |
| Docente | `TeacherCredential` | Centro FP | Panel de clase · Solicitar API Key · Configurar SCs |
| Operador | `OperatorCredential` | Autoridad VFDS | Gestionar servicio · Supervisar observabilidad vía CONNECTOR_CENTRAL |
| Investigador | `ResearcherCredential` | Inst. acreditada | Datos agregados NGSI-LD vía CONNECTOR_CENTRAL (solo lectura) |

Todas las VCs siguen el estándar **W3C Verifiable Credentials**, son compatibles con **eIDAS 2.0** y se almacenan en la wallet digital del usuario. El flujo de autenticación cotidiano del LMS y APP_LTI opera en primera fase sin pasar por el VCVERIFIER central, siendo esta una simplificación deliberada del MVP que se abordará en fases posteriores.
