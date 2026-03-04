### 5.3 Stack Tecnológico

#### 5.3.1 **Backend EAC (Nodo Central):**

```
Python 3.11+
├── FastAPI 0.110+          # API REST
├── NetworkX 3.2+           # Grafos de conocimiento (Grafo de Precedencia)
├── Pydantic 2.5+           # Validación de datos
├── SQLAlchemy 2.0+         # ORM para PostgreSQL
├── Anthropic SDK / OpenAI  # Generación de SCs con LLMs
├── Celery 5.3+             # Tareas asíncronas
└── Pytest 8.0+             # Testing
```

#### 5.3.2 **Base de Datos:**

```
PostgreSQL 16+  (Nodo Central)   # Datos estructurados del servicio
├── Tablas: scs, knowledge_states, submissions_anon, evaluations
│           habilitacion, skill_mastery
└── Extensión: pg_vector (para embeddings futuros)

PostgreSQL 16+  (cada Centro FP) # Datos sensibles locales (PII)
├── Tablas: students, submissions_raw, evaluation_results, submission_queue
└── NUNCA sincronizado con el servicio central

Neo4j 5.x (opcional)             # Grafo de Precedencia (SCs y prereqs)
└── Queries complejas de inferencia (Outer Fringe, Politopía)
```

#### 5.3.3 **FIWARE / Dataspace:**

```
Orion-LD 1.5+               # Context Broker (NGSI-LD v1.6.1)
                             #   Entidades: VocationalSkill,
                             #   LearningProblem, SkillMasteryAggregate
Mintaka (opcional)          # Temporal queries sobre Orion-LD

## FIWARE Dataspace Connector — desplegado en Nodo Central y en cada Centro FP

Eclipse EDC (FIWARE fork)   # Base del conector (CONNECTOR_CENTRAL y CONNECTOR_CFP)

  ## Authentication Service (por conector)
  Keycloak 23+              # VC Issuer — emite VCs firmadas (Ed25519)
                             #   CONNECTOR_CENTRAL: OperatorCredential, ResearcherCredential
                             #   CONNECTOR_CFP:     StudentCredential, TeacherCredential
  Verifier                  # Verificación de VCs presentadas (OIDC4VP / DIDComm)
  Auth Portal               # Orquesta flujo OIDC4VP entre cliente y Verifier
  Credentials Config Service# Define qué tipos de VC acepta cada recurso
  Local Trusted Issuers List# Registro local de emisores de confianza del conector
                             #   El Marketplace escribe en él al aprovisionar acceso

  ## Policy Management / Authorization Service (por conector)
  APISIX                    # API Gateway — PEP (Policy Enforcement Point)
  Open Policy Agent         # PDP (Policy Decision Point)
                             #   Evalúa políticas ODRL en tiempo de ejecución
  PRP / PAP                 # Policy Repository / Policy Administration Point
                             #   El Marketplace escribe la política ODRL al registrar producto
  PIP                       # Policy Information Point
                             #   Proporciona contexto al PDP para evaluar políticas

## Servicios Globales del Dataspace (Nodo Central)

  Data Space Participants Registry  # Registro de organizaciones participantes
                                    #   Consultado por el Verifier de cada conector
  Global Trusted Issuers List       # Catálogo global de emisores VC de confianza
  Revocation List                   # Credenciales revocadas — consultada en cada verificación
```

---

### 5.3.4 **Frontend (Centro FP):**

```
LMS Moodle 4.x              # Plataforma educativa de los centros
├── Plugin LTI              # Integración LTI 1.3 + REST API
├── Aggregator              # Seudonimización local en servidor Moodle
└── Cola de reintentos      # SQLite / BD Moodle para submissions pendientes

Aplicación LTI (React 18+)  # Embebida en Moodle vía LTI 1.3
│                            # Superficie de presentación del panel competencial
├── D3.js / Cytoscape.js    # Visualización del mapa de nodos competenciales
├── Recharts / Plotly       # Dashboards de progreso y Huella de Talento
├── Aggregator integrado    # Seudonimización local en servidor Moodle
└── Cola de reintentos      # SQLite / BD Moodle para submissions pendientes

```

##### **DevOps:**

```
Docker + Docker Compose     # Containerización
Kubernetes (producción)     # Escalado horizontal del Backend EAC
GitHub Actions              # CI/CD
Nginx                       # Reverse proxy
Prometheus + Grafana        # Observabilidad y alertas (SLA 99.5%)
```
