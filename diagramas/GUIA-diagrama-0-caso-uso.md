# Diagrama de Caso de Uso VFDS-EAC v2.1

## 🎯 Descripción del Caso de Uso

### Objetivo
Permitir que estudiantes de centros de Formación Profesional resuelvan problemas en sus plataformas LMS locales y que sus evaluaciones automáticas se procesen mediante el sistema PKST del **Backend EAC centralizado**, federando datos anonimizados a través del VFDS para mejorar colaborativamente el sistema educativo.

### Modelo Arquitectónico: Backend EAC Centralizado

**Decisión de diseño:**
- El **Backend EAC** es un **servicio centralizado** operado en el nodo central
- Los centros FP **NO operan su propio Backend EAC localmente**
- Los centros actúan como **consumidores del servicio** publicado en el Marketplace
- El **FIWARE Dataspace Connector** actúa como **API Gateway único** del servicio

**Ventajas de este modelo:**
- ✅ **Mantenimiento centralizado**: Una sola instancia del Backend EAC
- ✅ **Simplificación técnica**: Centros no necesitan infraestructura compleja
- ✅ **Actualizaciones instantáneas**: Un deploy beneficia a todos
- ✅ **Consistencia**: Mismo motor de evaluación para todos
- ✅ **Costes reducidos**: No duplicación de recursos

**Trade-offs aceptados:**
- ⚠️ Dependencia del servicio central (mitigado con SLA 99.5%)
- ⚠️ Menor soberanía computacional (pero datos sensibles siguen locales)
- ⚠️ No es IDS bilateral completo (pero cumple políticas de acceso)

---

## 🏗️ Arquitectura Actualizada por Zonas

### ZONA 1: Actores y Roles en el Dataspace

*[Sin cambios respecto a v2.0]*

#### 👨‍🎓 Estudiante (End User / Learner)
**Color:** Verde  
**Permisos:**
- ✅ Resolver problemas de evaluación automática en su LMS
- ✅ Ver sus propios resultados y feedback personalizado
- ✅ Consultar su estado de conocimiento (knowledge state)
- ❌ NO puede acceder a datos de otros estudiantes

**Credencial Verificable:**
- **Type:** `StudentCredential`
- **Issuer:** Centro FP donde está matriculado
- **Almacenamiento:** Wallet digital del estudiante

---

#### 👨‍🏫 Docente (Teacher / Administrator)
**Color:** Azul  
**Permisos:**
- ✅ Ver resultados agregados de sus grupos/asignaturas
- ✅ Configurar evaluaciones y problemas en el LMS
- ✅ Acceder a dashboards de analítica educativa
- ✅ **Solicitar acceso al servicio Backend EAC central**
- ❌ NO puede acceder a datos individuales sin consentimiento

**Flujo de integración con el servicio:**
1. Docente descubre "Backend EAC" en Marketplace
2. Solicita acceso presentando su `TeacherCredential`
3. Operador aprueba y provisiona **API Key**
4. Docente configura API Key en su LMS
5. LMS puede enviar evaluaciones al servicio central

---

#### 🔧 Operador del Dataspace (Data Space Operator)
**Color:** Naranja  
**Permisos:**
- ✅ Gestionar servicios habilitadores del VFDS
- ✅ **Operar y mantener el Backend EAC centralizado**
- ✅ Aprobar solicitudes de acceso al servicio
- ✅ Auditar transacciones del Dataspace Connector
- ✅ Monitorear métricas de observabilidad

**Responsabilidades adicionales:**
- Mantener operativo el Backend EAC central (SLA 99.5%)
- Gestionar API Keys de los centros consumidores
- Garantizar cumplimiento de políticas de uso
- Resolver incidencias técnicas del servicio

---

#### 🔍 Investigador / Auditor (Auditor / Researcher)
**Color:** Morado  

*[Sin cambios respecto a v2.0]*

---

### ZONA 2: Emisión de Credenciales Verificables (Centro FP)

*[Sin cambios respecto a v2.0]*

Los centros FP siguen actuando como **VC Issuers** para emitir `StudentCredential` y `TeacherCredential`.

---

### ZONA 3: Nodo Central Coordinador

#### 🔐 VC Verifier (Keycloak + VC Plugin)

*[Sin cambios respecto a v2.0]*

---

#### ⭐ Gaia-X Trust Framework

*[Sin cambios respecto a v2.0]*

---

#### 🛒 Marketplace / Federated Catalogue

**Servicio publicado (actualizado):**

##### 📦 Backend EAC v1.2.3 (Servicio Centralizado)
```
Publisher: VFDS Consortium (Nodo Central)
Operator: Equipo central de operaciones
Provider: Nodo Central Coordinador
Category: Learning Analytics
Type: REST API + NGSI-LD
Standards: LTI 1.3 (submission), NGSI-LD v1.6.1, W3C VC
Endpoint: https://eac-central.vfds.org/api/v2
Access Mode: API Key + OAuth2 (for integrations)
SLA: 99.5% uptime, <500ms p95 latency
Capacity: 10,000 evaluations/hour
Pricing: Free (pilot phase), usage-based after pilot
License: Educational use only (CC BY-NC 4.0)
Gaia-X Compliance: ✓ Certified
Self-Description: [Ver JSON-LD]
Documentation: https://eac-central.vfds.org/docs
Sandbox: https://sandbox.eac-central.vfds.org
Support: soporte-eac@vfds.org (L-V 9:00-18:00)
```

**Cambios clave vs. v2.0:**
- ✅ **Publisher**: Nodo Central (no centros individuales)
- ✅ **Endpoint único**: Todos los centros consumen el mismo servicio
- ✅ **Capacidad compartida**: 10k eval/hora para todos los participantes
- ✅ **Pricing**: Modelo de uso compartido

**Interacción con Marketplace - Flujo Actualizado:**

```
PASO 1: Descubrimiento
Docente del CIFP Carlos III busca "evaluación automática"
   ↓
CKAN devuelve: Backend EAC v1.2.3 (servicio central)
   ↓
Docente revisa:
   - Endpoint: https://eac-central.vfds.org/api/v2
   - Capacidad: 10k evaluaciones/hora (compartida)
   - Reviews: 4.7/5 estrellas de otros centros

PASO 2: Solicitud de Acceso
Docente solicita acceso al servicio
   ↓
Presenta TeacherCredential (VC)
   ↓
Formulario:
   - Centro: CIFP Carlos III
   - Estudiantes estimados: 50
   - Uso esperado: 200 evaluaciones/semana
   - Acepta términos de uso: [✓]

PASO 3: Aprobación y Provisioning
Operador del Nodo Central valida:
   - Centro es participante activo del VFDS
   - Capacidad disponible en el servicio
   ↓
Sistema genera:
   1. API Key: sk_cifp_carlos_iii_prod_abc123xyz
   2. Política en Authzforce:
      - Allows: POST /api/v2/evaluate
      - Rate limit: 500 req/hora
      - Propósito: evaluación educativa
   3. Contrato de servicio:
      - Vigencia: 2025-2026
      - Prohibiciones: uso comercial, redistribución
      - Retención datos: hasta fin de curso

PASO 4: Integración en LMS del Centro
Docente configura Moodle:
   - URL: https://eac-central.vfds.org/api/v2/evaluate
   - Authentication: Bearer sk_cifp_carlos_iii_prod_abc123xyz
   - LTI Launch URL: https://eac-central.vfds.org/lti/launch
   ↓
Prueba en sandbox: ✓ OK
   ↓
Activa en producción

PASO 5: Uso del Servicio
Estudiantes del CIFP Carlos III resuelven problemas
   ↓
LMS envía evaluaciones al endpoint central
   ↓
FIWARE Dataspace Connector valida API Key + políticas
   ↓
Backend EAC central procesa evaluaciones
   ↓
Retorna resultados al LMS del CIFP Carlos III
   ↓
Datos agregados se publican en Orion-LD Hub
```

---

#### 🔗 FIWARE Dataspace Connector (IDS/EDC) - UBICACIÓN CENTRAL

**🎯 CAMBIO CLAVE:** El Connector está **SOLO en el nodo central**, no en los centros FP.

**Función actualizada:**
- **API Gateway** del servicio Backend EAC centralizado
- **Point of entry** único para todos los centros consumidores
- **Validación de políticas** antes de permitir acceso
- **Auditoría y trazabilidad** de todas las transacciones
- **Rate limiting** por centro consumidor
- **Compliance Gaia-X** del servicio ofrecido

**Tecnología:**
- **Base:** Eclipse Dataspace Connector (EDC)
- **Extensiones:** FIWARE Context Broker integration, Gaia-X compliance, API Gateway capabilities

**Responsabilidades del Connector Central:**

1. **Autenticación de Centros Consumidores**
```
Request entrante
   ↓
Extrae API Key del header:
   Authorization: Bearer sk_cifp_carlos_iii_prod_abc123xyz
   ↓
Valida API Key contra registro:
   - Centro: CIFP Carlos III ✓
   - Estado: Active ✓
   - Expiration: 2026-08-31 ✓
   ↓
Si válido: continúa
Si inválido: 401 Unauthorized
```

2. **Verificación de Políticas (Authzforce PDP)**
```
Request autenticado
   ↓
Extrae contexto:
   - Subject: {center: "cifp_carlos_iii"}
   - Resource: {endpoint: "/api/v2/evaluate"}
   - Action: "POST"
   - Environment: {timestamp, ipAddress}
   ↓
Consulta Authzforce:
   ¿Tiene permiso este centro para este endpoint?
   ↓
PDP evalúa políticas:
   - Centro tiene contrato activo ✓
   - Rate limit no excedido (120/500 req/h) ✓
   - Horario dentro de ventana permitida ✓
   ↓
Decisión: Permit
```

3. **Rate Limiting por Centro**
```
Contador por API Key:
   - CIFP Carlos III: 120 requests en última hora
   - IES Cierva: 340 requests en última hora
   - Límites: 500 req/hora por centro

Si excede límite:
   → 429 Too Many Requests
   → Header: Retry-After: 1800
```

4. **Auditoría y Trazabilidad**
```
Para cada request, registra:
   - Timestamp: 2026-02-20T10:35:42Z
   - Center: cifp_carlos_iii
   - Endpoint: POST /api/v2/evaluate
   - Request ID: req_abc123xyz
   - Response: 200 OK
   - Latency: 142ms
   - Data processed: 1 submission
   - Contract ID: contract_cifp_2025-2026
   ↓
Almacena en audit log inmutable
```

5. **Cumplimiento Gaia-X**
```
Antes de procesar request:
   - Verifica que centro tiene Self-Description válida
   - Comprueba que contrato cumple políticas ODRL
   - Registra transacción en Gaia-X Clearing House (opcional)
   ↓
Si todo OK: procesa
```

**Flujo completo del Connector Central:**

```
┌─────────────────────────────────────────────────────────────┐
│         Request desde LMS del Centro FP                                │
│  POST https://eac-central.vfds.org/api/v2/evaluate                     │
│  Authorization: Bearer sk_cifp_carlos_iii_prod_abc123xyz               │
│  Content-Type: application/json                                        │
│  Body: { "submission": {...}, "problem_id": "..." }                    │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ↓
┌─────────────────────────────────────────────────────────────┐
│      FIWARE Dataspace Connector (Nodo Central)                         │
│                                                                        │
│  1. Autenticación                                                      │
│     ✓ Valida API Key                                                   │
│     ✓ Identifica centro: CIFP Carlos III                               │
│                                                                        │
│  2. Verificación de Políticas (Authzforce)                             │
│     ✓ Centro tiene contrato activo                                     │
│     ✓ Rate limit OK (120/500 req/h)                                    │
│     ✓ Endpoint permitido                                               │
│                                                                        │
│  3. Rate Limiting                                                      │
│     ✓ Incrementa contador del centro                                   │
│     ✓ Dentro del límite                                                │
│                                                                        │
│  4. Auditoría                                                          │
│     ✓ Registra transacción en log                                      │
│     ✓ Timestamp, center, endpoint, contract                            │
│                                                                        │
│  5. Decisión: PERMIT                                                   │
│     → Request pasa al Backend EAC                                      │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ↓
┌─────────────────────────────────────────────────────────────┐
│           Backend EAC Central (Servicio)                               │
│                                                                        │
│  • Recibe submission para evaluar                                      │
│  • Procesa con sistema PKST                                            │
│  • Genera feedback personalizado                                       │
│  • Actualiza métricas agregadas en Orion-LD                            │
│  • Retorna resultado                                                   │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ↓
┌─────────────────────────────────────────────────────────────┐
│      Response al LMS del Centro FP                                     │
│  200 OK                                                                │
│  {                                                                     │
│    "evaluation_result": {                                              │
│      "score": 7.5,                                                     │
│      "feedback": "...",                                                │
│      "next_recommendation": "prob_042"                                 │
│    }                                                                   │
│  }                                                                     │
└─────────────────────────────────────────────────────────────┘
```

**Ejemplo de política ODRL en el Connector:**
```json
{
  "@context": "http://www.w3.org/ns/odrl/2/",
  "@type": "Agreement",
  "uid": "https://eac-central.vfds.org/contracts/cifp-carlos-iii-2025-2026",
  "profile": "http://vfds.org/odrl:profile:backend-eac",
  "assigner": "https://vfds.org#nodo-central",
  "assignee": "https://cifp-carlos-iii.edu.es#organization",
  "permission": [{
    "target": "https://eac-central.vfds.org/api/v2",
    "action": ["use", "access"],
    "constraint": [
      {
        "leftOperand": "purpose",
        "operator": "eq",
        "rightOperand": "educational_evaluation"
      },
      {
        "leftOperand": "elapsedTime",
        "operator": "lt",
        "rightOperand": "P365D"
      }
    ],
    "duty": [{
      "action": "attribute",
      "target": "https://vfds.org",
      "constraint": [{
        "leftOperand": "payAmount",
        "operator": "eq",
        "rightOperand": "0",
        "unit": "EUR"
      }]
    }]
  }],
  "prohibition": [{
    "target": "https://eac-central.vfds.org/api/v2",
    "action": ["commercialize", "redistribute"]
  }]
}
```

---

#### 📋 Self-Descriptions Registry

*[Actualización menor]*

**Self-Description del Backend EAC Centralizado:**
```json
{
  "@context": {
    "gx": "https://registry.lab.gaia-x.eu/...",
    "vfds": "https://vfds.org/ontology#"
  },
  "@type": "gx:ServiceOffering",
  "gx:providedBy": {
    "@type": "gx:LegalParticipant",
    "gx:legalName": "VFDS Consortium - Nodo Central",
    "gx:legalRegistrationNumber": {
      "gx:taxID": "Q9999999X"
    },
    "gx:headquarterAddress": {
      "gx:countrySubdivisionCode": "ES-MC",
      "gx:locality": "Murcia"
    }
  },
  "gx:name": "Backend EAC v1.2.3",
  "gx:description": "Servicio centralizado de Evaluación Automática Competencial basado en PKST",
  "gx:serviceType": "Centralized Educational Service",
  "gx:endpoint": {
    "@type": "gx:Endpoint",
    "gx:endpointURL": "https://eac-central.vfds.org/api/v2",
    "gx:standardConformity": [
      {"gx:title": "NGSI-LD", "gx:standardReference": "https://www.etsi.org/..."},
      {"gx:title": "W3C VC", "gx:standardReference": "https://www.w3.org/..."}
    ]
  },
  "gx:dataAccountExport": {
    "gx:requestType": "API",
    "gx:accessType": "digital",
    "gx:formatType": "JSON-LD"
  },
  "gx:aggregationOf": [
    {
      "@type": "gx:Resource",
      "gx:name": "Backend EAC Compute Instance",
      "gx:hostedOn": {
        "gx:legalName": "Nodo Central Infrastructure"
      }
    }
  ],
  "vfds:accessControl": {
    "vfds:authenticationType": "API_Key + OAuth2",
    "vfds:authorizationService": "Authzforce PDP",
    "vfds:gatewayService": "FIWARE Dataspace Connector"
  },
  "vfds:serviceLevel": {
    "vfds:availability": "99.5%",
    "vfds:responseTime": "<500ms p95",
    "vfds:capacity": "10000 evaluations/hour",
    "vfds:support": "L-V 9:00-18:00 CET"
  },
  "gx:termsAndConditions": {
    "gx:URL": "https://eac-central.vfds.org/terms",
    "gx:hash": "sha256:abc123..."
  },
  "gx:policy": [
    "https://eac-central.vfds.org/policies/usage-policy.json",
    "https://eac-central.vfds.org/policies/data-retention.json"
  ]
}
```

**Cambios clave:**
- `serviceType`: "Centralized Educational Service"
- `providedBy`: Nodo Central (no centros individuales)
- `accessControl`: Especifica que usa Dataspace Connector como gateway

---

#### ⚖️ Policy Engine (Authzforce PDP)

*[Sin cambios conceptuales, actualización de ejemplo]*

**Política actualizada para acceso al servicio central:**
```xml
<Policy PolicyId="backend-eac-central-access" RuleCombiningAlgId="first-applicable">
  <Description>Política de acceso al Backend EAC centralizado</Description>
  
  <Target>
    <AnyOf>
      <AllOf>
        <Match MatchId="string-equal">
          <AttributeValue>Backend EAC Central v1.2.3</AttributeValue>
          <AttributeDesignator AttributeId="service-id" Category="resource"/>
        </Match>
      </AllOf>
    </AnyOf>
  </Target>
  
  <Rule RuleId="center-with-valid-contract" Effect="Permit">
    <Condition>
      <Apply FunctionId="and">
        <!-- Centro tiene contrato activo -->
        <Apply FunctionId="string-equal">
          <AttributeDesignator AttributeId="contract-status" Category="subject"/>
          <AttributeValue>active</AttributeValue>
        </Apply>
        <!-- Rate limit no excedido -->
        <Apply FunctionId="integer-less-than">
          <AttributeDesignator AttributeId="request-count-last-hour" Category="subject"/>
          <AttributeDesignator AttributeId="rate-limit" Category="subject"/>
        </Apply>
        <!-- Propósito es evaluación educativa -->
        <Apply FunctionId="string-equal">
          <AttributeDesignator AttributeId="purpose" Category="action"/>
          <AttributeValue>educational_evaluation</AttributeValue>
        </Apply>
      </Apply>
    </Condition>
  </Rule>
  
  <Rule RuleId="deny-by-default" Effect="Deny"/>
</Policy>
```

---

#### 🌐 Orion-LD Hub (Context Broker)

*[Sin cambios respecto a v2.0]*

---

#### 📊 Observabilidad

**Métricas adicionales para servicio centralizado:**

**Por servicio Backend EAC:**
- Total evaluations processed: 47,340 (last 24h)
- Success rate: 99.2%
- Average latency: 142ms (p50), 287ms (p95), 512ms (p99)
- Error rate: 0.8% (mostly 4xx client errors)

**Por centro consumidor:**
| Centro | Requests/h | Success % | Avg Latency | Rate Limit Used |
|--------|-----------|-----------|-------------|-----------------|
| IES Cierva | 340 | 99.5% | 138ms | 68% (340/500) |
| CIFP Carlos III | 120 | 98.9% | 145ms | 24% (120/500) |
| IES María Guerrero | 85 | 99.8% | 132ms | 17% (85/500) |

**Alertas específicas del modelo centralizado:**
- **Crítica:** Backend EAC down → **Impacta a TODOS los centros** → Prioridad máxima
- **Warning:** Latencia > 500ms → Posible saturación del servicio
- **Warning:** Centro específico excede 80% de su rate limit → Revisar uso
- **Info:** Nuevo centro registrado → Notificar a equipo de soporte

---

### ZONA 4: Nodo del Centro FP (Cliente del Servicio)

**🎯 CAMBIO FUNDAMENTAL:** El centro FP ya **NO tiene Backend EAC local** ni **IDS/EDC Connector local**.

#### 📚 LMS Moodle + Plugin EAC

**Función:** Interfaz donde los estudiantes resuelven problemas. El LMS actúa como **cliente del servicio Backend EAC central**.

**Integración actualizada:**

**Opción A: Integración LTI 1.3 (Recomendada)**
```
Estudiante accede a actividad EAC en Moodle
   ↓
Moodle genera LTI Launch Request
   ↓
Envía a: https://eac-central.vfds.org/lti/launch
   ↓
Backend EAC central valida firma LTI
   ↓
Renderiza interfaz de problema en iframe
   ↓
Estudiante resuelve problema
   ↓
Backend EAC procesa y retorna grade via LTI AGS
   ↓
Moodle actualiza calificación en libro de notas
```

**Opción B: Integración REST API directa**
```
Estudiante envía respuesta en Moodle
   ↓
Plugin Moodle captura submission
   ↓
Moodle hace POST:
   URL: https://eac-central.vfds.org/api/v2/evaluate
   Headers:
     Authorization: Bearer sk_cifp_carlos_iii_prod_abc123xyz
     Content-Type: application/json
   Body:
     {
       "student_id_hash": "anon_xyz123",
       "problem_id": "prob_tbm_042",
       "submission": { ... },
       "context": {
         "course_id": "curso_tbm_2025",
         "institution": "cifp_carlos_iii"
       }
     }
   ↓
Request pasa por FIWARE Dataspace Connector (validación)
   ↓
Backend EAC central procesa evaluación
   ↓
Retorna resultado:
     {
       "evaluation_result": {
         "score": 7.5,
         "feedback": "...",
         "skill_assessment": { "s1": 0.85, "s3": 0.65 },
         "next_recommendation": "prob_043"
       }
     }
   ↓
Moodle muestra resultado al estudiante
```

**Configuración en Moodle (ejemplo):**
```php
// Archivo: config.php del plugin EAC
define('EAC_API_URL', 'https://eac-central.vfds.org/api/v2/evaluate');
define('EAC_API_KEY', 'sk_cifp_carlos_iii_prod_abc123xyz');
define('EAC_TIMEOUT', 30); // segundos
define('EAC_RETRY_ATTEMPTS', 3);
define('EAC_INSTITUTION_ID', 'cifp_carlos_iii');
```

**Gestión de errores en el LMS:**
```
Si Backend EAC retorna error:
   - 401 Unauthorized → API Key inválida → Contactar operador
   - 429 Too Many Requests → Rate limit excedido → Reintentar en 30 min
   - 503 Service Unavailable → Servicio temporalmente no disponible → Cola local
   - 422 Unprocessable Entity → Datos malformados → Validar submission

Si timeout (>30s):
   - Guardar submission en cola local
   - Mostrar al estudiante: "Evaluación en proceso, recibirás resultado pronto"
   - Reintentar en background cada 5 minutos
```

---

#### 🔒 Aggregator / Anonymizer (Local)

**Función:** Preparar datos antes de enviarlos al servicio central, eliminando PII.

**⚠️ Importante:** Aunque el Backend EAC es centralizado, la **anonimización se hace en el centro** antes de enviar datos.

**Flujo:**
```
Estudiante envía submission con datos personales
   ↓
Plugin Moodle captura submission
   ↓
Aggregator local (integrado en plugin):
   1. Elimina nombre, email, ID real del estudiante
   2. Genera hash irreversible: std_12345 → anon_abc123xyz
   3. Remueve metadata sensible (IP address, ubicación exacta)
   4. Generaliza edad (22 años → rango 21-25)
   ↓
Submission anonimizada lista para enviar
   ↓
POST a Backend EAC central con datos limpios
```

**Ejemplo de transformación:**
```javascript
// Antes de anonimizar (local, nunca sale del centro)
{
  "student_id": "std_12345",
  "student_name": "María García López",
  "student_email": "maria.garcia@cifp-carlos-iii.edu.es",
  "submission": { ... },
  "timestamp": "2026-02-20T10:35:42Z",
  "ip_address": "192.168.1.50"
}

// Después de anonimizar (enviado al servicio central)
{
  "student_id_hash": "anon_sha256_abc123xyz",
  "submission": { ... },
  "timestamp": "2026-02-20T10:35:42Z",
  "context": {
    "institution": "cifp_carlos_iii",
    "age_range": "21-25",
    "program": "fp_comercio"
  }
}
```

**Ventaja:** El Backend EAC central **nunca ve datos personales**, cumplimiento RGPD por diseño.

---

#### 💾 Base de Datos Local (PostgreSQL)

**Función:** Almacenar datos sensibles que **NO se envían al servicio central**.

**Tablas principales:**
```sql
-- Datos de estudiantes (PII protegido, nunca sale del centro)
CREATE TABLE students (
  student_id UUID PRIMARY KEY,
  full_name VARCHAR(255) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  enrollment_date DATE NOT NULL,
  program VARCHAR(100),
  age INTEGER,
  consent_evaluation BOOLEAN DEFAULT TRUE,
  consent_research BOOLEAN DEFAULT FALSE
);

-- Submissions originales (con PII, solo local)
CREATE TABLE submissions_raw (
  submission_id UUID PRIMARY KEY,
  student_id UUID REFERENCES students(student_id),
  problem_id VARCHAR(100),
  submission_data JSONB,
  submitted_at TIMESTAMP DEFAULT NOW(),
  ip_address INET
);

-- Resultados recibidos del servicio central (sin PII)
CREATE TABLE evaluation_results (
  result_id UUID PRIMARY KEY,
  submission_id UUID REFERENCES submissions_raw(submission_id),
  student_id_hash VARCHAR(255), -- El hash que usó el servicio central
  score DECIMAL(4,2),
  feedback TEXT,
  skill_assessment JSONB,
  evaluated_at TIMESTAMP,
  service_request_id VARCHAR(255) -- Para trazabilidad
);

-- Cola de reintentos (en caso de fallo temporal)
CREATE TABLE submission_queue (
  queue_id UUID PRIMARY KEY,
  submission_id UUID REFERENCES submissions_raw(submission_id),
  retry_count INTEGER DEFAULT 0,
  last_attempt TIMESTAMP,
  next_retry TIMESTAMP,
  error_message TEXT,
  status VARCHAR(50) -- 'pending', 'processing', 'failed', 'completed'
);
```

**Datos que PERMANECEN local (nunca al servicio central):**
- ✅ Nombres completos de estudiantes
- ✅ Emails personales
- ✅ IDs reales de estudiantes
- ✅ Direcciones IP
- ✅ Ubicación geográfica precisa
- ✅ Datos sensibles según RGPD

**Datos que SÍ se envían (anonimizados):**
- ✅ Hash irreversible del student_id
- ✅ Submission content (respuesta al problema)
- ✅ Context agregado (institución, rango edad, programa)
- ✅ Timestamp (sin timezone que revele ubicación)

---

#### 🖥️ Arquitectura Completa del Nodo Centro FP

```
┌────────────────────────────────────────────────────────────┐
│          NODO CENTRO FP (Cliente Ligero)                              │
│                                                                       │
│  ┌──────────────────────────────────────────────┐             │
│  │         LMS Moodle + Plugin EAC                       │            │
│  │  • Interfaz estudiante                                │            │
│  │  • Captura submissions                                │            │
│  │  • Muestra resultados                                 │            │
│  └───────────────────┬──────────────────────────┘             │
│                          │                                            │
│                          ↓                                             │
│  ┌──────────────────────────────────────────────┐             │
│  │      Aggregator/Anonymizer (integrado)                │            │
│  │  • Elimina PII                                        │            │
│  │  • Genera hashes                                      │            │
│  │  • Prepara payload limpio                             │            │
│  └───────────────────┬──────────────────────────┘             │
│                          │                                            │
│                          ↓                                             │
│  ┌──────────────────────────────────────────────┐             │
│  │      PostgreSQL (Base de Datos Local)                 │            │
│  │  • students (PII protegido)                           │            │
│  │  • submissions_raw (con PII)                          │            │
│  │  • evaluation_results (sin PII)                       │            │
│  │  • submission_queue (reintentos)                      │            │
│  └──────────────────────────────────────────────┘             │
│                                                                       │
│                      ↓ HTTP POST (TLS 1.3)                            │
│            Authorization: Bearer API_KEY                              │
│            Body: Submission anonimizada                               │
└────────────────────────┬───────────────────────────────────┘
                              │
                              │ Internet
                              ↓
┌────────────────────────────────────────────────────────────┐
│                  NODO CENTRAL COORDINADOR                             │
│                                                                       │
│  ┌──────────────────────────────────────────────┐             │
│  │   FIWARE Dataspace Connector (API Gateway)            │            │
│  │  • Valida API Key                                     │            │
│  │  • Verifica políticas (Authzforce)                    │            │
│  │  • Rate limiting por centro                           │            │
│  │  • Auditoría completa                                 │            │
│  └───────────────────┬──────────────────────────┘             │
│                          ↓                                             │
│  ┌──────────────────────────────────────────────┐             │
│  │     Backend EAC Central (Servicio PKST)               │            │
│  │  • Rubric Evaluator                                   │            │
│  │  • Recommendation Engine                              │            │
│  │  • Problem Generator (LLM)                            │            │
│  │  • Student Model Manager                              │            │
│  └───────────────────┬──────────────────────────┘             │
│                          │                                            │
│                          ↓                                             │
│  ┌──────────────────────────────────────────────┐             │
│  │      Orion-LD Hub (Context Broker)                    │            │
│  │  • Agrega skills mastery de todos                     │            │
│  │  • Publica entidades NGSI-LD                          │            │
│  │  • Notifica a suscriptores                            │            │
│  └──────────────────────────────────────────────┘             │
│                                                                       │
└────────────────────────────────────────────────────────────┘
```

---

## 🔄 Flujos de Interacción Actualizados

### Flujo 1: Estudiante Resuelve Problema (Simplificado)

```
1. Estudiante presenta StudentCredential al sistema
   [ZONA 1 → ZONA 3: VC Verifier]
   
2. Sistema valida VC y genera sesión autenticada
   [ZONA 3: VC Verifier → JWT Token]
   
3. Estudiante accede a LMS Moodle de su centro
   [ZONA 1 → ZONA 4: LMS]
   
4. Selecciona actividad EAC en su curso
   [ZONA 4: LMS - muestra interfaz local o iframe del servicio central]
   
5. Estudiante resuelve y envía solución
   [ZONA 4: LMS captura submission]
   
6. Plugin Moodle anonimiza datos (Aggregator local)
   [ZONA 4: Aggregator elimina PII]
   [Etiqueta: "Anonimización local (RGPD)"]
   
7. LMS envía POST al servicio central
   [ZONA 4 → ZONA 3: HTTP POST con API Key]
   [Etiqueta: "POST con submission anonimizada"]
   
8. FIWARE Dataspace Connector valida request
   [ZONA 3: Connector → Authzforce → Backend EAC]
   [Etiqueta: "Validación API Key + políticas"]
   
9. Backend EAC central procesa evaluación
   [ZONA 3: Backend EAC → Rubric Evaluator + Recommendation Engine]
   
10. Backend EAC actualiza Orion-LD con métricas agregadas
    [ZONA 3: Backend EAC → Orion-LD Hub]
    [Etiqueta: "Skills federados (agregados)"]
    
11. Backend EAC retorna resultado al LMS del centro
    [ZONA 3 → ZONA 4: HTTP 200 + resultado JSON]
    [Etiqueta: "Resultado + feedback personalizado"]
    
12. LMS muestra resultado al estudiante
    [ZONA 4: LMS → Estudiante]
    
13. LMS almacena resultado en BD local (vinculado a student_id real)
    [ZONA 4: LMS → PostgreSQL local]
    [Datos: vincula resultado con PII local para uso del docente]
```

**Datos en tránsito:**
- ➡️ **Al servicio central:** Submission anonimizada (sin PII)
- ⬅️ **Desde servicio central:** Resultado de evaluación (sin PII)
- 🔒 **Solo local:** PII del estudiante, vinculación resultado-estudiante

---

### Flujo 2: Federación de Datos Agregados (Simplificado)

```
Ya NO hay flujo separado de "federación de datos agregados" desde el centro.

En su lugar:
   
Backend EAC Central procesa evaluación
   ↓
Actualiza directamente métricas en Orion-LD Hub
   (todo ocurre dentro del ZONA 3 - Nodo Central)
   ↓
Otras entidades (investigadores, otros centros) consultan Orion-LD
   [ZONA 3: Orion-LD → Consumidores]
```

**Ventaja:** Simplificación radical del flujo de federación.

---

### Flujo 3: Docente Descubre y Consume Servicio (Actualizado)

```
1. Docente presenta TeacherCredential
   [ZONA 1 → ZONA 3: VC Verifier]
   
2. Busca "Backend EAC" en Marketplace
   [ZONA 1 → ZONA 3: Marketplace]
   [Etiqueta: "Busca servicio EAC centralizado"]
   
3. Marketplace muestra servicio con metadata Gaia-X
   [ZONA 3: Marketplace consulta Self-Descriptions]
   [Detalle: "Backend EAC v1.2.3 - Servicio Central"]
   
4. Docente revisa:
   - Endpoint: https://eac-central.vfds.org/api/v2
   - SLA: 99.5% uptime
   - Capacidad: 10k eval/hora (compartida)
   - Pricing: Free (pilot phase)
   - Reviews: 4.7/5 estrellas
   
5. Docente solicita acceso
   [ZONA 1 → ZONA 3: Marketplace]
   [Etiqueta: "Solicita acceso con TeacherCredential"]
   
6. Sistema verifica con Policy Engine
   [ZONA 3: Marketplace → Authzforce PDP]
   
7. Operador aprueba (manual o automático)
   [ZONA 3: Operador Dataspace]
   
8. Sistema provisiona:
   - API Key: sk_cifp_carlos_iii_prod_abc123xyz
   - Política en Authzforce (rate limit 500 req/h)
   - Contrato ODRL (vigencia 2025-2026)
   [ZONA 3: Sistema central]
   
9. Docente recibe API Key por email
   [ZONA 3 → ZONA 1]
   
10. Docente configura API Key en Moodle
    [ZONA 1 → ZONA 4: Configuración LMS]
    
11. Estudiantes del centro pueden usar el servicio
    [ZONA 1 → ZONA 4 → ZONA 3: Flujo normal de evaluación]
```

---

### Flujo 4: Publicación del Servicio en Marketplace (Actualizado)

```
Ya NO es "cada centro publica su Backend EAC".

En su lugar:

1. Nodo Central decide publicar el servicio Backend EAC
   [ZONA 3: Operador Dataspace]
   
2. Genera Self-Description Gaia-X del servicio
   [ZONA 3: proceso interno del nodo central]
   
3. Envía Self-Description a Gaia-X Compliance Service
   [ZONA 3: interno → Gaia-X Trust Framework]
   [Etiqueta: "Valida compliance del servicio"]
   
4. Compliance Service valida y emite certificado ⭐
   [ZONA 3: Gaia-X Trust Framework]
   
5. Self-Description registrada
   [ZONA 3: Self-Descriptions Registry]
   
6. Servicio publicado en Marketplace
   [ZONA 3: Marketplace/CKAN]
   [Visible para todos los docentes del VFDS]
   
7. Centros FP pueden descubrirlo y solicitarlo
   [ZONA 1 → ZONA 3: flujo de descubrimiento]
```

---

## 🌟 Componentes Gaia-X - Resumen Actualizado

### Dónde se usa Gaia-X (sin cambios conceptuales):

1. **⭐ Trust Framework** - Valida identidad de participantes y compliance
2. **⭐ Federated Catalogue** - Marketplace donde se publica el Backend EAC
3. **⭐ Self-Descriptions** - Metadata del servicio centralizado
4. **⭐ IDS/EDC Connector** - Gateway del servicio con compliance Gaia-X

**Nota:** Aunque el Connector está solo en el nodo central, sigue cumpliendo estándares Gaia-X.

---

## 📊 Comparación Arquitectónica

| Aspecto | v2.0 (Distribuido) | v2.1 (Centralizado - Actual) |
|---------|-------------------|------------------------------|
| **Backend EAC** | Uno por centro FP | Uno centralizado en nodo central |
| **IDS/EDC Connector** | Uno por centro + uno central | Solo en nodo central |
| **Mantenimiento** | Cada centro actualiza su instancia | Un único deploy para todos |
| **Escalabilidad** | Horizontal (más centros = más instancias) | Vertical (escalar servicio central) |
| **Consistencia** | Posibles diferencias entre versiones | Misma versión para todos |
| **Complejidad técnica** | Alta (cada centro opera infraestructura) | Baja (centros son clientes simples) |
| **Soberanía computacional** | Alta (cada centro procesa localmente) | Baja (procesamiento centralizado) |
| **Soberanía de datos** | Alta (datos nunca salen del centro) | Alta (PII permanece local, solo anonimizados al central) |
| **Coste operativo** | Alto (multiplicado por N centros) | Bajo (un solo servicio compartido) |
| **Dependencia** | Baja (cada centro independiente) | Alta (dependen del servicio central) |
| **SLA crítico** | No (fallo afecta solo un centro) | Sí (fallo afecta a todos) |
| **Negociación IDS** | Bilateral completa (P2P) | Unilateral (cliente-servidor) |
| **Cumplimiento Gaia-X** | ✓ Completo | ✓ Adaptado (gateway pattern) |

---

## ✅ Ventajas del Modelo Centralizado (v2.1)

### 1. Simplicidad Operativa
- ✅ Centros pequeños sin capacidad técnica pueden participar fácilmente
- ✅ No requieren infraestructura compleja (solo LMS + plugin)
- ✅ Onboarding rápido (configurar API Key en <1 hora)

### 2. Consistencia del Servicio
- ✅ Todos los estudiantes evaluados con el mismo motor PKST
- ✅ Misma versión, mismo algoritmo, mismos criterios
- ✅ Comparabilidad directa de métricas entre centros

### 3. Eficiencia de Recursos
- ✅ Una sola infraestructura compartida (economía de escala)
- ✅ Optimización centralizada (caching, GPU sharing para LLMs)
- ✅ Mantenimiento de un solo equipo técnico

### 4. Actualizaciones Rápidas
- ✅ Nuevas features disponibles inmediatamente para todos
- ✅ Corrección de bugs con impacto inmediato
- ✅ No necesidad de coordinar actualizaciones multi-centro

### 5. Observabilidad Completa
- ✅ Métricas centralizadas de uso y rendimiento
- ✅ Detección temprana de anomalías
- ✅ Optimización basada en datos globales

---

## ⚠️ Desafíos y Mitigaciones

### Desafío 1: Dependencia del Servicio Central
**Riesgo:** Si el Backend EAC central cae, todos los centros se ven afectados.

**Mitigación:**
- ✅ SLA 99.5% (3.6h downtime/mes máximo)
- ✅ Infraestructura redundante (multiple availability zones)
- ✅ Monitoreo 24/7 con alertas automáticas
- ✅ Plan de contingencia: LMS guarda submissions en cola local y reintenta
- ✅ Modo degradado: evaluación básica en LMS sin PKST avanzado

### Desafío 2: Cuello de Botella de Rendimiento
**Riesgo:** 10,000 eval/hora puede no ser suficiente en picos de uso.

**Mitigación:**
- ✅ Escalado horizontal automático del Backend EAC (Kubernetes)
- ✅ Rate limiting por centro (500 req/h) para evitar monopolización
- ✅ Caché de problemas frecuentes
- ✅ Procesamiento asíncrono para evaluaciones complejas

### Desafío 3: Privacidad de Datos
**Riesgo:** Centralizar procesamiento podría exponer datos sensibles.

**Mitigación:**
- ✅ Anonimización obligatoria en origen (antes de enviar)
- ✅ Backend EAC central nunca ve PII
- ✅ Auditorías regulares de compliance RGPD
- ✅ Datos agregados publicados con k-anonymity ≥ 5

### Desafío 4: Vendor Lock-in
**Riesgo:** Centros dependen del proveedor del servicio central.

**Mitigación:**
- ✅ API abierta y documentada (OpenAPI 3.1)
- ✅ Estándares abiertos (NGSI-LD, W3C VC, IDS)
- ✅ Código del Backend EAC en GitHub (licencia open source futura)
- ✅ Datos exportables en cualquier momento (DCAT-AP compliance)
- ✅ Posibilidad de federación futura con otros servicios EAC

---

## 🚀 Plan de Implementación Actualizado

### Fase 1: MVP - Servicio Central Básico (2 meses)

**Nodo Central:**
1. Desplegar Backend EAC v1.0 (funcionalidad core PKST)
2. Configurar FIWARE Dataspace Connector como API Gateway
3. Integrar con Keycloak (VC Verifier)
4. Configurar Authzforce con políticas básicas
5. Publicar servicio en Marketplace con Self-Description

**Centro Piloto 1 (IES Cierva):**
1. Configurar plugin Moodle con API Key
2. Implementar Aggregator local (anonimización)
3. Probar flujo completo con 10 estudiantes sintéticos
4. Validar cumplimiento RGPD

**Métricas de éxito:**
- ✅ 100 evaluaciones procesadas sin errores
- ✅ Latencia p95 < 500ms
- ✅ Sin fugas de PII detectadas

---

### Fase 2: Escalado a 3 Centros (2 meses)

**Nodo Central:**
1. Optimizar rendimiento (caching, async processing)
2. Implementar observabilidad completa (Prometheus/Grafana)
3. Configurar rate limiting por centro
4. Añadir generación de problemas con LLM

**Centros Adicionales:**
1. CIFP Carlos III se integra
2. IES María Guerrero se integra
3. Cada centro: 50 estudiantes reales

**Métricas de éxito:**
- ✅ 1,000 evaluaciones/día sin degradación
- ✅ Success rate > 99%
- ✅ 3 centros operando simultáneamente

---

### Fase 3: Producción Completa (3 meses)

1. Añadir más centros (hasta 10)
2. Implementar sistema de soporte (ticketing)
3. Documentación completa para nuevos centros
4. Capacitación de docentes
5. Plan de escalado para 100k evaluaciones/día

**Métricas de éxito:**
- ✅ 10 centros activos
- ✅ 500+ estudiantes/día usando el servicio
- ✅ SLA 99.5% cumplido

---

## 📞 Recursos y Referencias

### Estándares Implementados
*[Sin cambios respecto a v2.0]*

### Implementaciones Open Source
*[Sin cambios respecto a v2.0]*

### Documentación Específica del Modelo Centralizado
- **API Gateway Pattern:** https://microservices.io/patterns/apigateway.html
- **Multi-tenancy Best Practices:** https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/
- **Centralized vs Distributed Services:** https://martinfowler.com/articles/microservices.html

---

## 📝 Apéndice: Preguntas Frecuentes (FAQ)

### ¿Por qué el Backend EAC es centralizado y no distribuido?
**Respuesta:** Después de analizar las capacidades técnicas de los centros FP participantes, se decidió que un modelo centralizado reduce barreras de entrada y costes operativos. Los centros más pequeños pueden participar sin necesidad de infraestructura compleja.

### ¿Los datos de estudiantes salen del centro?
**Respuesta:** **Solo datos anonimizados**. El PII (nombre, email, ID real) **NUNCA** sale del centro. La anonimización se hace localmente antes de enviar cualquier dato al servicio central.

### ¿Qué pasa si el servicio central falla?
**Respuesta:** El LMS del centro guarda las submissions en una cola local y reintenta automáticamente. En el peor caso, el docente puede evaluar manualmente hasta que el servicio se recupere. El SLA es 99.5% (máximo 3.6h downtime/mes).

### ¿Cómo se asegura que un centro no monopolice el servicio?
**Respuesta:** Rate limiting por centro (500 requests/hora por defecto). Si un centro necesita más capacidad, puede solicitarlo al operador.

### ¿Puedo operar mi propio Backend EAC en vez de usar el centralizado?
**Respuesta:** En el futuro (v3.0), se planea permitir nodos híbridos donde centros con capacidad técnica puedan operar su propia instancia y federarla con el servicio central.

### ¿El modelo centralizado cumple Gaia-X?
**Respuesta:** Sí. Aunque no es IDS bilateral completo (peer-to-peer), sigue cumpliendo Gaia-X mediante el uso del Dataspace Connector como API Gateway, Self-Descriptions del servicio, y políticas ODRL aplicadas.

---

**Versión:** 2.1  
**Fecha:** Febrero 2026  
**Cambio principal:** FIWARE Dataspace Connector ubicado únicamente en el Nodo Central  
**Autores:** Equipo VFDS-EAC  
**Licencia:** Documentación bajo CC BY 4.0
