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
Eclipse EDC (FIWARE fork)   # CONNECTOR_CENTRAL — API Gateway del Nodo Central
Eclipse EDC (FIWARE fork)   # CONNECTOR_CFP — uno por Centro FP
                             #   (frontera de soberanía del dato)
Keycloak 23+                # Identity & Access Management
                             #   + plugin VC Verifier (OIDC4VC)
Authzforce PDP              # Motor de políticas XACML / ODRL
Mintaka (opcional)          # Temporal queries sobre Orion-LD
```

### 5.3.4 **Frontend (Centro FP):**

```
LMS Moodle 4.x              # Plataforma educativa de los centros
└── Plugin LTI        # Integración LTI 1.3 + REST API

Aplicación LTI (React 18+)  # Embebida en Moodle vía LTI 1.3
│                            # Superficie de presentación del panel competencial
├── D3.js / Cytoscape.js    # Visualización del mapa de nodos competenciales
├── Recharts / Plotly       # Dashboards de progreso y Huella de Talento
├── Aggregator integrado    # Anonimización local en servidor Moodle
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
