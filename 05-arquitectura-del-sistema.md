## 5. Arquitectura del Sistema

### 5.1 Vista de Alto Nivel

```
┌─────────────────────────────────────────────────────────────────┐
│                    VOCATIONAL FEDERATE DATASPACE                │
│                     (FIWARE Orion-LD + Keycloak)                │
└───────────────────────────┬─────────────────────────────────────┘
                            │ NGSI-LD API
                            ↓
┌─────────────────────────────────────────────────────────────────┐
│                      EAC ADAPTIVE ENGINE                        │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌────────────────┐  │
│  │ Knowledge Space │  │ Problem Generator│  │ Recommendation │  │
│  │   Builder       │  │   (LLM-based)    │  │    Engine      │  │
│  └─────────────────┘  └─────────────────┘  └────────────────┘  │
│                                                                  │
│  ┌─────────────────┐  ┌─────────────────┐  ┌────────────────┐  │
│  │ Rubric Evaluator│  │ Synthetic Data   │  │  Skill Graph   │  │
│  │   (Auto-score)  │  │   Generator      │  │   Manager      │  │
│  └─────────────────┘  └─────────────────┘  └────────────────┘  │
└───────────────────────────┬─────────────────────────────────────┘
                            │ REST API
                            ↓
┌─────────────────────────────────────────────────────────────────┐
│                   FRONTEND DASHBOARD                             │
├─────────────────────────────────────────────────────────────────┤
│  Student View          │  Teacher View         │  Admin View    │
│  - Knowledge Map       │  - Class Progress     │  - Analytics   │
│  - Next Problem        │  - Problem Bank       │  - Data Export │
│  - Progress Tracker    │  - Rubric Editor      │  - Integrations│
└─────────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────────┐
│              EXTERNAL PROBLEM PLATFORMS (via API)                │
│  - LMS (Moodle, Canvas)  - Coding Platforms  - Simulators      │
└─────────────────────────────────────────────────────────────────┘
```

### 5.2 Componentes Principales

#### **5.2.1 Knowledge Space Builder**

**Responsabilidad:** Construir y mantener el grafo de habilidades y el espacio de conocimiento.

**Funcionalidades:**
- Parsear estructura curricular (Ciclos → Módulos → RA → CE)
- Construir DAG de habilidades desde CE
- Calcular propiedades del grafo (componentes, ciclos, topological sort)
- Detectar inconsistencias (ciclos, habilidades huérfanas)

**Tecnologías:**
- **NetworkX** (Python): Manipulación de grafos
- **Neo4j** (opcional): Persistencia y queries complejas

#### **5.2.2 Problem Generator (LLM-based)**

**Responsabilidad:** Generar automáticamente problemas desde plantillas y habilidades requeridas.

**Funcionalidades:**
- Generar enunciados de problemas con Claude/GPT-4
- Crear rúbricas de evaluación multi-criterio
- Validar coherencia problema-habilidades
- Versionar problemas (dificultad, variantes)

**Prompt Engineering:** Ver sección 7

#### **5.2.3 Recommendation Engine**

**Responsabilidad:** Seleccionar el próximo problema óptimo para cada estudiante.

**Algoritmo básico (MVP):**
```python
def recommend_next_problem(student_knowledge_state):
    outer_fringe = calculate_outer_fringe(student_knowledge_state)
    
    # Filtrar problemas que cubran habilidades de la franja
    candidate_problems = filter_problems_by_skills(all_problems, outer_fringe)
    
    # Heurística de selección
    if student_performance_last_3 > 0.85:  # Resuelve rápido y bien
        # Problema más complejo de la misma área
        return select_max_difficulty(candidate_problems)
    
    elif student_performance_last_3 < 0.50:  # Tiene dificultades
        # Problema más simple que refuerce prerrequisitos débiles
        weak_skills = identify_weak_prerequisites(student_knowledge_state)
        return select_problem_reinforcing(weak_skills)
    
    else:  # Rendimiento medio
        # Problema aleatorio de la franja exterior
        return random.choice(candidate_problems)
```

#### **5.2.4 Rubric Evaluator**

**Responsabilidad:** Evaluar automáticamente las respuestas de estudiantes mediante rúbricas.

**Funcionalidades:**
- Recibir respuesta del estudiante (texto, imagen, código, JSON estructurado)
- Aplicar rúbrica multi-criterio
- Asignar puntuación por habilidad
- Actualizar estado de conocimiento del estudiante

**Tipos de evaluación:**
- **Automática completa:** Problemas con respuesta estructurada (JSON, código)
- **Asistida por LLM:** Evaluación de respuestas abiertas (ensayos, diseños)
- **Manual:** Profesor revisa, sistema aprende

### 5.3 Stack Tecnológico

#### **Backend:**
```
Python 3.11+
├── FastAPI 0.110+          # API REST
├── NetworkX 3.2+           # Grafos de conocimiento
├── Pydantic 2.5+           # Validación de datos
├── SQLAlchemy 2.0+         # ORM para PostgreSQL
├── Anthropic SDK / OpenAI  # Generación con LLMs
├── Celery 5.3+             # Tareas asíncronas
└── Pytest 8.0+             # Testing
```

#### **Base de datos:**
```
PostgreSQL 16+              # Datos estructurados
├── Tablas: students, problems, knowledge_states, submissions
└── Extensión: pg_vector (para embeddings futuros)

Neo4j 5.x (opcional)        # Grafo de habilidades
└── Queries complejas de inferencia
```

#### **FIWARE:**
```
Orion-LD 1.5+               # Context Broker (NGSI-LD)
Keycloak 23+                # Identity & Access Management
Mintaka (opcional)          # Temporal queries
```

#### **Frontend:**
```
React 18+ / Next.js 14
├── D3.js / Cytoscape.js    # Visualización de grafos
├── Recharts / Plotly       # Dashboards
├── TailwindCSS             # Styling
└── React Query             # State management
```

#### **DevOps:**
```
Docker + Docker Compose     # Containerización
GitHub Actions              # CI/CD
Nginx                       # Reverse proxy
```
