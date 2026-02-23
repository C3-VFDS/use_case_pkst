# Diagramas Arquitectónicos VFDS-PKST
## Vocational Federated Data Space con Sistema de Evaluación Adaptativa

**Proyecto:** PP3 - Red de Centros de Excelencia en IA y Big Data  
**Fecha:** Febrero 2026  
**Versión:** 1.0

---

## 📋 Índice de Diagramas

Este paquete contiene 5 diagramas SVG que documentan la arquitectura completa del sistema:

1. **[Diagrama 0](./GUIA-diagrama-0-caso-uso.md)** - Esquema operativo del caso de uso PKST (EAC) con flujos de datos y actores
2. **[Diagrama 1](#diagrama-1-arquitectura-federada-vfds)** - Vista de Alto Nivel: Arquitectura Federada
3. **[Diagrama 2](#diagrama-2-nodo-fp-individual)** - Componentes Detallados de un Nodo FP
4. **[Diagrama 3](#diagrama-3-nodo-central-coordinador)** - Servicios Habilitadores del Nodo Central
5. **[Diagrama 4](#diagrama-4-flujo-de-datos)** - Flujo de Datos del Caso de Uso PKST

---

## 🏗️ Decisiones Arquitectónicas Clave

### Modelo de Gobernanza
- **Tipo:** Federado con nodo central coordinador
- **Número de nodos:** 1 nodo central + 2 centros FP (MVP)
- **Estándares:** UNE 0087:2025, ETSI NGSI-LD v1.6.1, IDS RAM 4.0, Gaia-X

### Distribución de Servicios
- **Centralizados:** Keycloak (IAM), CKAN (Catálogo), Orion-LD Hub, Observabilidad
- **Distribuidos:** Backend PKST, PostgreSQL local, Conectores IDS/EDC por nodo

### Actores del Dataspace
- **Proveedores de datos:** Plataformas LMS de centros FP, aplicaciones LTI
- **Consumidores:** Sistemas de recomendación, analítica, investigación
- **Operadores:** Gestores del dataspace, autoridad de gobierno
- **Servicios de confianza:** Certificadores, auditores

### Datos Federados vs. Locales
**Datos FEDERADOS (NGSI-LD):**
- ✅ Ontología de skills (vocabulario común)
- ✅ Problemas públicos del banco compartido
- ✅ Métricas agregadas y seudonimizadas
- ✅ Metadatos de módulos FP

**Datos LOCALES (protegidos RGPD):**
- 🔒 Estados individuales de estudiantes
- 🔒 Submissions con datos personales
- 🔒 Evaluaciones detalladas
- 🔒 Rutas de aprendizaje individuales

### Tecnologías Principales
- **Backend:** Python 3.11+, FastAPI 0.110+, NetworkX 3.2+ (o Neo4J), Celery 5.3+
- **FIWARE:** Orion-LD 1.5+, Keycloak 23+, Wilma PEP Proxy, Authzforce PDP
- **Persistencia:** PostgreSQL 16+, MongoDB 6.0+, Redis (cache)
- **Federación:** Eclipse Dataspace Connector (EDC), IDS Protocol
- **LLMs:** API externa (Anthropic Claude / OpenAI GPT-4)
- **Observabilidad:** Prometheus, Grafana, ELK Stack

---

## Diagrama 1: Arquitectura Federada VFDS

### 📊 Archivo
[`diagrama-1-arquitectura-federada-vfds.svg`](./diagrama-1-arquitectura-federada-vfds.svg)

### 🎯 Propósito
Muestra la vista de alto nivel de toda la federación, con el nodo central coordinador y los 2 centros de FP conectados.

### 🔍 Componentes Principales

#### Nodo Central Coordinador
- **Orion-LD Hub:** Context Broker que agrega información de todos los nodos
- **Keycloak IAM:** Gestión centralizada de identidad (OAuth2/OIDC/SAML)
- **CKAN Catálogo:** Descubrimiento de recursos federados (DCAT-AP 2.1)
- **Observabilidad:** Prometheus/Grafana + ELK Stack para monitoreo global

#### Nodo FP 1 - IES Lluis Simarro (Xátiva - Valencia)
- **Capa LMS:** Integración con Moodle, Canvas, LTI/xAPI
- **Backend PKST/EAC:** Motor de evaluación, recomendación y generación
- **PostgreSQL:** Base de datos local con información sensible
- **Conector IDS/EDC:** Para comunicación federada segura
- **Cliente NGSI-LD:** Sincronización de skills y métricas

#### Nodo FP 2 - IES Ribera del Tajo (Talavera de la Reina - Castilla La Mancha)
- Arquitectura equivalente al Nodo FP 1
- Autonomía completa sobre sus datos
- Participa en el catálogo compartido

### 🔗 Flujos de Comunicación
- **IDS Protocol** (líneas naranjas continuas): Conectores IDS/EDC para negociación de contratos
- **NGSI-LD** (líneas verdes discontinuas): Sincronización de entidades federadas

### 💡 Puntos Clave
1. **Soberanía de datos:** Cada centro mantiene control sobre datos sensibles
2. **Interoperabilidad:** Estándares NGSI-LD + IDS garantizan federación
3. **Escalabilidad:** Arquitectura preparada para añadir más nodos FP
4. **Cumplimiento:** Alineado con Gaia-X y normativa europea de datos

---

## Diagrama 2: Nodo FP Individual

### 📊 Archivo
[`diagrama-2-nodo-fp-individual.svg`](./diagrama-2-nodo-fp-individual.svg)

### 🎯 Propósito
Detalla la arquitectura interna de un centro de FP, mostrando todos los componentes del sistema PKST.

### 🔍 Capas Arquitectónicas

#### 1️⃣ Capa de Integración LMS
- **Moodle LMS:** LTI 1.3 Provider, xAPI Endpoint
- **Canvas LMS:** LTI Advantage, Caliper Analytics
- **IoT Agent LTI:** Convierte protocolos LMS a NGSI (componente FIWARE)
- **API Gateway:** FastAPI con rate limiting
- **Otras plataformas:** Blackboard, Sakai (IMS LTI compliant)

**Función:** Captura eventos de aprendizaje desde cualquier LMS compatible con LTI.

#### 2️⃣ Backend PKST - Adaptive Engine
**Componentes principales:**
- **Knowledge Space Builder:** Construye grafo de habilidades con prerrequisitos (NetworkX/Neo4j)
- **Recommendation Engine:** Selecciona "siguiente mejor tarea" usando Outer Fringe (ZDP)
- **Rubric Evaluator:** Evaluación multi-criterio automática + feedback con LLM
- **Problem Generator:** Genera problemas con Claude API/GPT-4, validación y calibración
- **Student Model Manager:** Tracking de estados de conocimiento y skill mastery
- **API REST Layer:** FastAPI + Pydantic con especificación OpenAPI 3.1
- **Background Tasks:** Celery + Redis para procesamiento asíncrono
- **Synthetic Data Gen:** Faker + Monte Carlo para testing

**LLM APIs externo:** Llamadas a Anthropic Claude API / OpenAI GPT-4 (rate limit: 100 req/h)

#### 3️⃣ Capa de Persistencia
- **PostgreSQL 16 (Datos Locales):** students, submissions, knowledge_states (protección RGPD, backup diario)
- **Redis (Cache + Queue):** Session management, Celery broker, rate limiting
- **Neo4j (Opcional):** Grafo de habilidades para consultas complejas
- **MongoDB:** Para Orion-LD Context storage

#### 4️⃣ Capa de Federación e Interoperabilidad
- **Conector IDS/EDC:** Negociación de contratos, políticas ODRL, trazabilidad
- **Cliente NGSI-LD:** Publica VocationalSkill, LearningProblem, AggregatedMetrics
- **Servicios de Autenticación:** OAuth2 Client (Keycloak), token validation, RBAC, SSO

**Conexión con:**
- 🌐 Nodo Central Coordinador (Orion-LD Hub, Keycloak, CKAN, Observability)
- 🔄 Otros Nodos FP (consumo de skills, colaboración, datos agregados)

### 💡 Puntos Clave
1. **Modularidad:** Cada capa tiene responsabilidades claras
2. **Desacoplamiento:** Integración LMS agnóstica mediante estándares
3. **Privacidad:** Datos sensibles nunca salen de PostgreSQL local
4. **Extensibilidad:** Fácil añadir nuevos LMS o componentes PKST

---

## Diagrama 3: Nodo Central Coordinador

### 📊 Archivo
[`diagrama-3-nodo-central-coordinador.svg`](./diagrama-3-nodo-central-coordinador.svg)

### 🎯 Propósito
Muestra todos los servicios habilitadores centralizados que dan soporte a la federación.

### 🔍 Capas de Servicios

#### 1️⃣ Servicios Core FIWARE
- **Orion-LD Context Broker Hub** (v1.5.1, puerto 1026)
  - Agregación de contexto federado
  - Suscripciones entre nodos
  - Queries distribuidas NGSI-LD v1.6.1
  
- **MongoDB Context Store** (v6.0+)
  - Bases: orion, orion-tenants
  - Persistencia de entidades: Skills, Modules, AggregatedMetrics
  
- **Mintaka Temporal API** (opcional)
  - FIWARE Mintaka + TimescaleDB
  - Análisis de evolución temporal de skills
  - Endpoint: /temporal/entities

#### 2️⃣ Identidad y Gestión de Acceso
- **Keycloak Identity Provider** (v23+, puerto 8080)
  - Protocolos: OAuth2, OIDC, SAML 2.0
  - Realms: VFDS-Master, VFDS-FP
  - Roles: Admin, Teacher, Student, Auditor
  
- **Wilma PEP Proxy** (puerto 1027)
  - Policy Enforcement Point
  - Intercepta requests al Context Broker
  - Valida tokens + aplica políticas
  
- **Authzforce PDP** (puerto 8080)
  - Policy Decision Point (XACML 3.0)
  - Políticas ABAC complejas
  - Decisiones: Permit/Deny/NotApplicable

#### 3️⃣ Catálogo y Descubrimiento
- **CKAN Data Catalog** (v2.10+, puerto 5000)
  - Estándar DCAT-AP 2.1
  - Catálogo de: Skills ontology, Problemas públicos, Módulos FP, Rúbricas, Datasets
  
- **PostgreSQL (CKAN DB)** (v16)
  - Base: ckan_default
  - Metadatos + Solr para búsqueda
  
- **Schema Registry**
  - JSON-LD @context
  - Vocabularios: VocationalSkill, LearningModule, LearningProblem, StudentProfile

#### 4️⃣ Observabilidad y Control Operativo
- **Prometheus** (v2.45+, puerto 9090)
  - Scraping de métricas de Orion, Keycloak, nodos FP
  
- **Grafana** (v10.2+, puerto 3001)
  - Dashboards: Estado global VFDS, SLIs/SLOs, Uso de catálogo
  
- **ELK Stack** (v8.11)
  - Elasticsearch + Logstash + Kibana
  - Logging centralizado: Logs Orion-LD, Audit trails, Security events
  
- **Alertmanager** (puerto 9093)
  - Alertas: Nodo FP down, Alta latencia, Violaciones de políticas

#### 5️⃣ Gobernanza y Confianza
- **Servicios de Confianza:** Contratos digitales, Certificación, Resolución de disputas, Compliance RGPD
- **Analítica del Dataspace:** Métricas agregadas, Insights de skills mastery, Reporte de calidad

### 💡 Puntos Clave
1. **Centralización estratégica:** Reduce complejidad operativa en los centros
2. **Escalabilidad horizontal:** Más nodos FP no aumentan carga en servicios centrales
3. **Seguridad multi-capa:** IAM (Keycloak) + PEP (Wilma) + PDP (Authzforce)
4. **Observabilidad completa:** Monitoreo + Logging + Alertas desde el inicio

---

## Diagrama 4: Flujo de Datos - Caso de Uso EAC

### 📊 Archivo
[`diagrama-4-flujo-datos-caso-uso.svg`](./diagrama-4-flujo-datos-caso-uso.svg)

### 🎯 Propósito
Ilustra el ciclo completo desde que un estudiante resuelve un problema hasta que el sistema genera la siguiente recomendación, diferenciando datos locales vs. federados.

### 🔄 Fases del Flujo

#### FASE 1: Estudiante Resuelve Problema
**Actores:**
- 👨‍🎓 **Estudiante:** María García (ID: std_12345, Módulo TBM, FP Comercio)
- **Moodle LMS:** Asigna problema "Diseñar escaparate" (tipo procedural, skills [s1, s3, s5], dificultad media)
- **Submission:** Captura respuesta completa con metadata del proceso

**Datos generados (locales):**
- submission_789: respuesta, timestamps, secuencia de pasos, metadata

#### FASE 2: Evaluación Automática con Rúbricas
**Componentes del Backend PKST:**

1. **Rubric Evaluator**
   - Criterios: Corrección técnica, Seguridad, Eficiencia, Creatividad
   - Score: 7.5/10

2. **LLM Assistant** (Claude API externa)
   - Análisis cualitativo: "El diseño cumple requisitos pero falta contraste. Revisar principio de jerarquía visual"
   - Feedback generado automáticamente

3. **Student State** (actualización local 🔒)
   - knowledge_state_456
   - s1: mastered (0.85)
   - s3: progress (0.65)
   - s5: learning (0.40)
   - Outer fringe actualizado: [s6, s8]
   - **RGPD protected** - No sale del centro

4. **Recommendation Engine**
   - Análisis ZDP: Estado K = {s1, s3}, Fringe = {s6, s8}
   - Recomendación: "Problema P_042: Aplicar normativa de seguridad en visual merchandising"

5. **PostgreSQL**
   - Persistencia local de: submissions, knowledge_states, evaluations, recommendations
   - 🔒 Centro local únicamente

#### FASE 3: Sincronización con Nodo Central (Datos Seudonimizados)

1. **Aggregator Service**
   - Procesa datos locales
   - Elimina PII (RGPD): std_12345 → anonymous_hash_xyz
   - Agrega métricas: skill_mastery_stats
   - ✓ Safe to federate

2. **Conector IDS/EDC**
   - Negociación con políticas ODRL
   - Contrato digital: "Shared skills ontology improvement"
   - Trazabilidad: Transaction ID tx_abc

3. **Entidades NGSI-LD** (publicadas 🌐)
   ```json
   VocationalSkill {
     id: "urn:skill:s3"
     masteryRate: 0.65 ± 0.12
     sampleSize: 47 (anonymized)
   }
   ```

4. **Orion-LD Hub**
   - Recibe actualización desde Nodo FP 1
   - Notifica suscriptores: CKAN, Sistema Analítica, Otros nodos FP

5. **CKAN Catalog**
   - Actualiza dataset: "Skills Mastery TBM Module"
   - Metadatos DCAT-AP
   - Acceso: público

#### FASE 4: Consumo y Mejora Colaborativa

**Consumidores del Dataspace:**

1. **Nodo FP 2 - CIFP Carlos III**
   - Consulta: "¿Qué skills son críticos para TBM?"
   - Recibe stats agregadas
   - Ajusta su currículo ✓

2. **Sistema de Analítica**
   - Dashboard en tiempo real
   - Mapa de calor de skills
   - Identificación de gaps
   - Predicción de abandono
   - → Insights para profesores

3. **Generador de Problemas**
   - Entrena con datos federados
   - Detecta: "Skills con baja mastery requieren más problemas"
   - Genera 10 problemas enfocados en s5 y s8 ✓
   - **Feedback loop:** Mejora continua del banco de problemas

4. **Investigadores / Auditores**
   - Acceso controlado vía Keycloak (rol: researcher)
   - Estudios longitudinales: "Efectividad de PKST en FP 2025-2026"

5. **Audit Trail**
   - Registra: Quién, Qué, Cuándo, Propósito
   - Trazabilidad completa del flujo

### 🔑 Puntos Clave del Flujo

1. ✅ **Privacidad por diseño:** Datos personales nunca abandonan el nodo local
2. ✅ **Seudonimización automática:** Aggregator Service elimina PII antes de federar
3. ✅ **Trazabilidad completa:** Cada transacción registrada en audit trail
4. ✅ **Consentimiento explícito:** Contratos digitales IDS/EDC con propósito declarado
5. ✅ **Mejora colaborativa:** Los datos agregados benefician a toda la red de centros
6. ✅ **Ciclo cerrado:** Recomendación → Evaluación → Feedback → Generación → Recomendación

### 🎨 Leyenda de Flujos
- **Verde continuo:** Datos locales protegidos (RGPD)
- **Azul discontinuo:** Datos federados seudonimizados (NGSI-LD)
- **Negro continuo:** Interacción usuario
- **Naranja discontinuo:** Llamada API externa (LLM)

---

## 📦 Uso de los Diagramas

### Formatos Disponibles
Todos los diagramas están en formato **SVG** (Scalable Vector Graphics), lo que permite:
- ✅ Escalado sin pérdida de calidad
- ✅ Edición con herramientas como Inkscape, Adobe Illustrator, Figma
- ✅ Conversión a PNG/PDF con alta resolución
- ✅ Integración en documentación técnica (Markdown, HTML, LaTeX)

### Visualización
Puedes abrir los archivos SVG directamente en:
- Navegadores web (Chrome, Firefox, Safari, Edge)
- Editores de imágenes vectoriales
- Herramientas de documentación (Confluence, Notion, GitBook)

### Edición
Si necesitas personalizar los diagramas:
1. Abre con **Inkscape** (gratuito): https://inkscape.org/
2. Edita textos, colores, componentes
3. Exporta a SVG optimizado o PNG de alta resolución

### Conversión a Otros Formatos

**Convertir SVG → PNG de alta resolución:**
```bash
# Con ImageMagick (instalar: apt-get install imagemagick)
convert -density 300 diagrama-1-arquitectura-federada-vfds.svg diagrama-1.png

# Con Inkscape (línea de comandos)
inkscape --export-type=png --export-dpi=300 diagrama-1-arquitectura-federada-vfds.svg
```

**Convertir SVG → PDF:**
```bash
# Con Inkscape
inkscape --export-type=pdf diagrama-1-arquitectura-federada-vfds.svg

# Con rsvg-convert
rsvg-convert -f pdf -o diagrama-1.pdf diagrama-1-arquitectura-federada-vfds.svg
```

---

## 🚀 Siguientes Pasos Recomendados

### Validación Técnica
1. **Revisar con equipos técnicos de los centros FP** involucrados
2. **Validar con autoridad de gobierno** del VFDS
3. **Confirmar cumplimiento normativo** (RGPD, Ley de Gobernanza de Datos)

### Refinamiento Arquitectónico
1. **Especificar APIs NGSI-LD** completas (endpoints, payloads, suscripciones)
2. **Definir políticas ODRL** para casos de uso específicos
3. **Diseñar modelo de datos completo** (@context JSON-LD)
4. **Planificar estrategia de despliegue** (Docker Compose → Kubernetes)

### Documentación Complementaria
1. **Diagramas de secuencia UML** para flujos críticos
2. **Diagramas de casos de uso** para diferentes actores
3. **Matriz de trazabilidad** requisitos → componentes
4. **Documento de APIs** (especificación OpenAPI 3.1)

### Prototipado MVP
1. **Despliegue en un solo centro** (Nodo FP + Nodo Central simulado)
2. **Generación de 100 problemas** con LLM
3. **Simulación con 100 estudiantes sintéticos**
4. **Dashboard de visualización** del mapa de conocimiento
5. **Métricas de éxito** (precisión recomendación, engagement, mastery rate)

---

## 📞 Contacto y Soporte

Para preguntas sobre estos diagramas o la arquitectura VFDS-EAC:

**Proyecto:** PP3 - Red de Centros de Excelencia en IA y Big Data  
**Documentación técnica:** [Repositorio GitHub](https://github.com/C3-VFDS/use_case_pkst)  
**Marco teórico:** Ver PDFs adjuntos en el proyecto

---

## 📄 Licencia

Estos diagramas son parte de la documentación técnica del proyecto VFDS-EAC y están sujetos a la licencia del proyecto principal.

---

**Generado:** Febrero 2026  
**Versión de documentación:** 1.0  
**Última actualización:** [Fecha actual]
