# Caso de Uso: Sistema de Evaluación y Recomendación Adaptativa basado en PKST para el Vocational Federate Dataspace

## Documento Técnico de Especificación
**Versión:** 1.0  
**Fecha:** Febrero 2026  
**Proyecto:** PP3 - Red de Centros de Excelencia en IA y Big Data  
**Módulo Piloto:** Técnicas Básicas de Merchandising

---

## Índice

1. [Resumen Ejecutivo](#1-resumen-ejecutivo)
2. [Contexto y Justificación](#2-contexto-y-justificación)
3. [Marco Teórico: PKST Simplificado](#3-marco-teórico-pkst-simplificado)
4. [Análisis del Módulo Piloto](#4-análisis-del-módulo-piloto)
5. [Arquitectura del Sistema](#5-arquitectura-del-sistema)
6. [Modelado del Espacio de Conocimiento](#6-modelado-del-espacio-de-conocimiento)
7. [Generación Automática de Problemas con LLMs](#7-generación-automática-de-problemas-con-llms)
8. [Sistema de Evaluación mediante Rúbricas](#8-sistema-de-evaluación-mediante-rúbricas)
9. [Motor de Recomendación Adaptativa](#9-motor-de-recomendación-adaptativa)
10. [Especificación de APIs](#10-especificación-de-apis)
11. [Integración con FIWARE](#11-integración-con-fiware)
12. [Generación de Datos Sintéticos](#12-generación-de-datos-sintéticos)
13. [Plan de Implementación MVP](#13-plan-de-implementación-mvp)
14. [Métricas de Éxito y Evaluación](#14-métricas-de-éxito-y-evaluación)
15. [Roadmap Futuro](#15-roadmap-futuro)
16. [Referencias y Bibliografía](#16-referencias-y-bibliografía)

---

## 1. Resumen Ejecutivo

### 1.1 Visión General

El presente documento especifica un **sistema de evaluación y recomendación adaptativa** basado en la Teoría de Espacios de Conocimiento Procedimental (PKST) para el Vocational Federate Dataspace. El sistema revoluciona la evaluación en Formación Profesional al:

- **Mapear competencias procedimentales** en un espacio de conocimiento formal
- **Evaluar procesos, no solo resultados** mediante rúbricas automatizadas
- **Recomendar problemas personalizados** según la zona de desarrollo próximo del estudiante
- **Federar conocimiento** entre centros de FP mediante estándares NGSI-LD

### 1.2 Diferenciación e Innovación

**Valor único frente a otros dataspaces educativos:**

| Aspecto | DS4Skills / Prometheus-X / Merlot | **Nuestro Sistema (PKST-FP)** |
|---------|-----------------------------------|-------------------------------|
| Foco | Competencias declarativas (skills inventory) | **Competencias procedimentales (cómo se resuelven problemas)** |
| Evaluación | Resultado binario (pass/fail) | **Secuencia de pasos + rúbricas multi-criterio** |
| Recomendación | Basada en matching genérico | **Basada en franja de aprendizaje (outer fringe) con fundamento matemático** |
| Dominio | Genérico educación superior | **Específico Formación Profesional española** |
| Generación de contenido | Manual | **Automática con LLMs validados** |

### 1.3 Objetivos del MVP (Marzo 2026)

1. ✅ Implementar PKST Capa 1 (determinista) para 1 módulo completo
2. ✅ Generar automáticamente 100+ problemas con Claude/GPT-4
3. ✅ Demostrar recomendación adaptativa con 100 estudiantes sintéticos
4. ✅ Integrar con FIWARE Orion-LD y Keycloak
5. ✅ Proporcionar dashboard visual de mapa de conocimiento

---

## 2. Contexto y Justificación

### 2.1 Problemática Actual en FP

La evaluación tradicional en Formación Profesional presenta limitaciones críticas:

**Problema 1: Evaluación sumativa descontextualizada**
- Se evalúa el resultado final (examen tipo test, proyecto entregado)
- No se captura el proceso de resolución
- Un error intermedio invalida todo el trabajo, sin diagnóstico preciso

**Problema 2: Falta de personalización**
- Todos los estudiantes siguen la misma secuencia curricular
- No se adapta a ritmos de aprendizaje individuales
- Difícil identificar lagunas específicas de conocimiento

**Problema 3: Desconexión teoría-práctica**
- Los Criterios de Evaluación son abstractos
- No hay trazabilidad entre CE → Habilidades concretas → Problemas

**Problema 4: Imposibilidad de compartir aprendizajes entre centros**
- Cada centro tiene su propio banco de problemas
- No existe una estructura formal para intercambiar recursos educativos

### 2.2 Solución Propuesta: PKST + Dataspace Federado

Nuestro sistema aborda estos problemas mediante:

1. **Modelado formal del conocimiento procedimental**
   - Mapeo explícito: CE → Habilidades atómicas → Problemas
   - Grafo de prerrequisitos que garantiza coherencia pedagógica

2. **Evaluación mediante rúbricas multi-criterio**
   - No solo "correcto/incorrecto", sino qué pasos específicos dominó el estudiante
   - Captura parcial de la traza procedimental

3. **Recomendación adaptativa fundamentada**
   - Algoritmo basado en la franja exterior (outer fringe) de PKST
   - Garantías matemáticas de progreso óptimo

4. **Federación de conocimiento**
   - Cada centro aporta su espacio de conocimiento al dataspace
   - Estándares NGSI-LD + ESCO permiten interoperabilidad

### 2.3 Por Qué "Técnicas Básicas de Merchandising"

La elección de este módulo como piloto es estratégica:

✅ **Representativo de FP:** Combina conocimientos teóricos (psicología del consumidor) con habilidades prácticas (diseño de escaparates, gestión de lineal)

✅ **Evaluable procedimentalmente:** Las tareas tienen secuencias claras (análisis → diseño → implementación → evaluación)

✅ **Visualmente demostrable:** Los resultados (escaparates, layouts) son visualizables en dashboards

✅ **Escalable a otras familias:** La metodología aplica igualmente a Informática, Administración, etc.

✅ **Datos sintéticos realistas:** Es fácil generar escenarios ficticios de merchandising

---

## 3. Marco Teórico: PKST Simplificado

### 3.1 Fundamentos de PKST (Capa 1)

La Teoría de Espacios de Conocimiento Procedimental modela el conocimiento como una **estructura combinatoria** donde:

**Definición 1: Habilidad Atómica**
> Una habilidad atómica `H` es la unidad mínima de competencia procedimental evaluable de forma independiente.

**Ejemplo en Merchandising:**
- `H1`: Identificar el público objetivo de un producto
- `H2`: Analizar la circulación de clientes en un establecimiento
- `H3`: Aplicar la regla del triángulo de oro en layout
- `H4`: Seleccionar paleta cromática según psicología del color

**Definición 2: Estado de Conocimiento**
> Un estado de conocimiento `K` es un subconjunto de habilidades que un estudiante ha dominado: `K ⊆ H_total`

**Ejemplo:**
```
K_estudiante = {H1, H2, H5, H8}  # Domina 4 de 15 habilidades totales
```

**Definición 3: Espacio de Conocimiento**
> El espacio de conocimiento `𝒦` es el conjunto de todos los estados de conocimiento válidos, construido respetando prerrequisitos.

**Axioma de Clausura bajo Unión:**
> Si `K₁, K₂ ∈ 𝒦`, entonces `K₁ ∪ K₂ ∈ 𝒦`

Esto garantiza que no existen restricciones artificiales al aprendizaje.

### 3.2 Grafo de Prerrequisitos

El espacio de conocimiento se deriva de un **grafo dirigido acíclico (DAG)** donde:
- **Nodos:** Habilidades atómicas
- **Aristas:** Relaciones de prerrequisito (`H_i → H_j` significa "H_i es prerrequisito de H_j")

**Ejemplo visual:**

```
         H1 (Identificar público)
           ↓
         H2 (Analizar circulación)
           ↓
    ┌──────┴──────┐
    ↓             ↓
   H3 (Layout)   H4 (Psicología color)
    ↓             ↓
    └──────┬──────┘
           ↓
         H5 (Diseño escaparate completo)
```

**Regla de inferencia:**
> Si un estudiante domina `H5`, necesariamente domina `{H1, H2}` y al menos uno de `{H3, H4}`

### 3.3 Franja Exterior (Outer Fringe)

**Definición:**
> La franja exterior de un estado `K` es el conjunto de habilidades que el estudiante está **listo para aprender** porque domina todos sus prerrequisitos:

```
OuterFringe(K) = {H ∉ K | ∀H_prereq → H, H_prereq ∈ K}
```

**Ejemplo:**
```
K_actual = {H1, H2, H3}
OuterFringe(K_actual) = {H5}  # Puede aprender H5 porque tiene H1, H2, H3
```

**Implicación pedagógica:**
> El sistema solo recomienda problemas que involucren habilidades de la franja exterior, evitando frustración (reto imposible) o aburrimiento (reto trivial).

### 3.4 Simplificaciones para MVP (Capa 1)

Para el prototipo de marzo, **NO implementamos:**
- ❌ Modelos probabilísticos (BLIM, MSPM)
- ❌ Captura completa de trazas en tiempo real
- ❌ Inferencia bayesiana de estados latentes

**SÍ implementamos:**
- ✅ Grafo de prerrequisitos determinista
- ✅ Cálculo exacto de franja exterior
- ✅ Evaluación binaria por habilidad (dominada/no dominada) mediante rúbricas
- ✅ Recomendación basada en heurística simple sobre la franja

---

## 4. Análisis del Módulo Piloto

### 4.1 Ficha Técnica del Módulo

**Módulo:** Técnicas Básicas de Merchandising  
**Código:** (Código oficial del MEFP según Real Decreto)  
**Familia Profesional:** Comercio y Marketing  
**Ciclo Formativo:** Técnico en Actividades Comerciales (Grado Medio)  
**Duración:** 100 horas (aproximadamente)  
**Curso:** 1º

### 4.2 Estructura Curricular

#### **Resultados de Aprendizaje (RA)**

**RA1:** Caracteriza la implantación de productos y servicios analizando el espacio comercial y las técnicas de animación del punto de venta.

**RA2:** Organiza la implantación de productos en la superficie de venta definiendo y aplicando criterios comerciales.

**RA3:** Realiza el montaje de escaparates y otros expositores comerciales aplicando criterios de composición y los elementos de animación del punto de venta.

**RA4:** Dispone elementos gráficos de información, señalización y publicidad en el punto de venta, aplicando técnicas de diseño y colocación.

**RA5:** Valora la implantación de productos y los elementos de animación en el punto de venta, analizando la normativa aplicable y el impacto sobre el cliente.

### 4.3 Muestra de Criterios de Evaluación

Dado que el módulo tiene aproximadamente 60 CE, mostramos una muestra representativa:

#### **Criterios de Evaluación del RA1:**

**CE 1.1:** Se han identificado las características físicas y psicológicas del público objetivo.

**CE 1.2:** Se ha analizado la circulación de clientes en el establecimiento comercial.

**CE 1.3:** Se han identificado las zonas y puntos de venta fríos y calientes.

**CE 1.4:** Se han aplicado técnicas de análisis del comportamiento del cliente en el punto de venta.

**CE 1.5:** Se han reconocido las normativas de seguridad y accesibilidad aplicables al punto de venta.

#### **Criterios de Evaluación del RA3:**

**CE 3.1:** Se ha elaborado un proyecto de escaparate según el tipo de producto y establecimiento.

**CE 3.2:** Se han aplicado los principios de composición en el diseño del escaparate.

**CE 3.3:** Se ha seleccionado la paleta cromática coherente con la identidad de marca.

**CE 3.4:** Se han dispuesto los elementos del escaparate aplicando la regla del equilibrio visual.

**CE 3.5:** Se ha evaluado el impacto del escaparate mediante técnicas de observación.

### 4.4 Mapeo CE → Habilidades Atómicas

#### **Proceso de Derivación:**

1. **Análisis semántico del CE:** Identificar verbos de acción y objetos
2. **Descomposición en habilidades atómicas:** Cada verbo + contexto = 1 habilidad
3. **Identificación de prerrequisitos:** Establecer dependencias lógicas
4. **Validación con expertos:** Profesores del módulo validan el grafo

#### **Ejemplo de Mapeo:**

**CE 3.1:** "Se ha elaborado un proyecto de escaparate según el tipo de producto y establecimiento"

**Habilidades derivadas:**
- `H3.1.1`: Clasificar productos según categoría (alimentación, moda, tecnología...)
- `H3.1.2`: Identificar el tipo de establecimiento (flagship, outlet, pop-up...)
- `H3.1.3`: Analizar el público objetivo del establecimiento
- `H3.1.4`: Definir objetivos del escaparate (informativo, persuasivo, recordatorio)
- `H3.1.5`: Esbozar el layout básico del escaparate (boceto)

**Prerrequisitos:**
```
H3.1.1 → H3.1.4  # Necesito clasificar producto antes de definir objetivos
H3.1.2 → H3.1.4  # Necesito conocer tipo de establecimiento
H3.1.3 → H3.1.4  # Necesito conocer público objetivo
{H3.1.1, H3.1.2, H3.1.3, H3.1.4} → H3.1.5  # Todas las anteriores antes del boceto
```

### 4.5 Estimación del Grafo Completo

**Estadísticas del MVP:**
- **Resultados de Aprendizaje:** 5
- **Criterios de Evaluación:** ~60
- **Habilidades Atómicas estimadas:** 60 CE × 5 habilidades/CE = **~300 habilidades**
- **Aristas del grafo (prerrequisitos):** ~450-600 (ratio típico 1.5-2 aristas/nodo)

**Complejidad computacional:**
- **Cálculo de franja exterior:** O(|V| + |E|) = O(300 + 500) ≈ **800 operaciones** (trivial)
- **Espacio de conocimiento completo:** 2^300 estados (imposible enumerar, pero no necesario)

---

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
│                      PKST ADAPTIVE ENGINE                        │
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

---

## 6. Modelado del Espacio de Conocimiento

### 6.1 Estructura de Datos

#### **6.1.1 Entidad: Skill (Habilidad)**

```python
from pydantic import BaseModel, Field
from typing import List, Optional
from enum import Enum

class SkillLevel(str, Enum):
    BASIC = "basic"
    INTERMEDIATE = "intermediate"
    ADVANCED = "advanced"

class Skill(BaseModel):
    id: str = Field(..., description="Identificador único (e.g., H3.1.1)")
    name: str = Field(..., description="Nombre descriptivo")
    description: str = Field(..., description="Descripción detallada")
    
    # Vinculación curricular
    criterion_id: str = Field(..., description="CE del que deriva")
    learning_outcome_id: str = Field(..., description="RA al que pertenece")
    module_id: str = Field(..., description="Módulo formativo")
    
    # Prerrequisitos
    prerequisites: List[str] = Field(default=[], description="IDs de habilidades prereq")
    
    # Metadatos
    level: SkillLevel = Field(default=SkillLevel.BASIC)
    estimated_learning_time_minutes: int = Field(default=30)
    
    # ESCO mapping (para interoperabilidad)
    esco_skill_uri: Optional[str] = Field(None, description="URI ESCO si aplica")

# Ejemplo:
skill_h311 = Skill(
    id="H3.1.1",
    name="Clasificar productos por categoría",
    description="Capacidad de identificar y categorizar productos según su naturaleza: alimentación, moda, tecnología, hogar, etc.",
    criterion_id="CE3.1",
    learning_outcome_id="RA3",
    module_id="MOD_TBM",
    prerequisites=[],  # Habilidad básica sin prerrequisitos
    level=SkillLevel.BASIC,
    estimated_learning_time_minutes=20,
    esco_skill_uri="http://data.europa.eu/esco/skill/..."
)
```

#### **6.1.2 Entidad: KnowledgeState**

```python
from datetime import datetime
from typing import Set

class KnowledgeState(BaseModel):
    id: str
    student_id: str
    module_id: str
    
    # Estado actual
    mastered_skills: Set[str] = Field(default_factory=set, description="Habilidades dominadas")
    in_progress_skills: Set[str] = Field(default_factory=set, description="En proceso")
    
    # Franjas (calculadas dinámicamente)
    outer_fringe: Set[str] = Field(default_factory=set, description="Listas para aprender")
    inner_fringe: Set[str] = Field(default_factory=set, description="Pueden olvidarse")
    
    # Metadatos
    last_updated: datetime
    total_problems_attempted: int = 0
    total_problems_solved: int = 0
    average_time_per_problem_minutes: float = 0.0
    
    # Nivel de competencia global (0-100)
    competence_score: float = Field(default=0.0, ge=0, le=100)

# Ejemplo:
knowledge_state_student = KnowledgeState(
    id="ks_student123_mod_tbm",
    student_id="student123",
    module_id="MOD_TBM",
    mastered_skills={"H3.1.1", "H3.1.2", "H3.1.3"},
    outer_fringe={"H3.1.4", "H3.2.1"},
    last_updated=datetime.now(),
    total_problems_attempted=15,
    total_problems_solved=12,
    competence_score=35.5  # Domina 3 de ~300 habilidades
)
```

#### **6.1.3 Entidad: Problem**

```python
from typing import Dict, Any

class ProblemDifficulty(str, Enum):
    EASY = "easy"
    MEDIUM = "medium"
    HARD = "hard"

class Problem(BaseModel):
    id: str
    title: str
    description: str  # Enunciado completo (Markdown)
    
    # Habilidades requeridas
    required_skills: List[str] = Field(..., description="Habilidades que evalúa")
    primary_skill: str = Field(..., description="Habilidad principal")
    
    # Dificultad
    difficulty: ProblemDifficulty
    estimated_time_minutes: int
    
    # Recursos
    attachments: List[str] = Field(default=[], description="URLs de imágenes, PDFs...")
    external_url: Optional[str] = Field(None, description="URL plataforma externa")
    
    # Rúbrica
    rubric: Dict[str, Any] = Field(..., description="Ver sección 8")
    
    # Metadatos
    created_by: str  # "LLM" o "teacher_id"
    created_at: datetime
    times_used: int = 0
    average_success_rate: float = 0.0
    
    # Generación
    generation_prompt: Optional[str] = Field(None, description="Prompt usado si LLM")
    llm_model: Optional[str] = Field(None, description="claude-3.5-sonnet, gpt-4, etc.")

# Ejemplo:
problem_escaparate_basico = Problem(
    id="prob_001",
    title="Diseño de escaparate para tienda de calzado deportivo",
    description="""
## Contexto
Eres el responsable de merchandising de una tienda de calzado deportivo situada en un centro comercial. La tienda tiene un escaparate de 4m de ancho x 2.5m de alto.

## Producto a destacar
Nuevo modelo de zapatillas de running de la marca "SpeedMax", dirigido a corredores jóvenes (20-35 años).

## Tareas
1. Identifica el público objetivo (edad, estilo de vida, motivaciones)
2. Propón un esquema básico del escaparate indicando:
   - Disposición de productos (cantidad, posición)
   - Paleta cromática principal
   - Elementos de atrezzo complementarios
3. Justifica tu propuesta según principios de psicología del color
    """,
    required_skills=["H3.1.1", "H3.1.3", "H3.2.2", "H3.3.1"],
    primary_skill="H3.3.1",
    difficulty=ProblemDifficulty.MEDIUM,
    estimated_time_minutes=30,
    rubric={...},  # Ver sección 8
    created_by="claude-3.5-sonnet",
    created_at=datetime.now(),
    generation_prompt="Genera un problema de nivel medio..."
)
```

### 6.2 Algoritmo de Construcción del Grafo

```python
import networkx as nx
from typing import List, Set

class KnowledgeSpaceBuilder:
    def __init__(self):
        self.skill_graph = nx.DiGraph()
        self.skills_db = {}  # {skill_id: Skill}
    
    def add_skill(self, skill: Skill):
        """Añade una habilidad al grafo"""
        self.skills_db[skill.id] = skill
        self.skill_graph.add_node(
            skill.id,
            name=skill.name,
            level=skill.level,
            criterion_id=skill.criterion_id
        )
        
        # Añadir aristas de prerrequisitos
        for prereq_id in skill.prerequisites:
            self.skill_graph.add_edge(prereq_id, skill.id)
    
    def build_from_curriculum(self, module_curriculum: Dict):
        """Construye el grafo desde la estructura curricular"""
        for ra in module_curriculum['learning_outcomes']:
            for ce in ra['evaluation_criteria']:
                # Aquí iría la lógica de mapeo CE → Habilidades
                # Por ahora asumimos que ya tenemos el mapeo
                for skill in ce['derived_skills']:
                    self.add_skill(skill)
    
    def validate_graph(self) -> List[str]:
        """Valida la coherencia del grafo"""
        errors = []
        
        # Detectar ciclos (imposibles en prerrequisitos)
        if not nx.is_directed_acyclic_graph(self.skill_graph):
            cycles = list(nx.simple_cycles(self.skill_graph))
            errors.append(f"Ciclos detectados: {cycles}")
        
        # Detectar nodos huérfanos (sin prerrequisitos ni dependientes)
        for node in self.skill_graph.nodes():
            if self.skill_graph.in_degree(node) == 0 and \
               self.skill_graph.out_degree(node) == 0:
                errors.append(f"Habilidad huérfana: {node}")
        
        return errors
    
    def calculate_outer_fringe(self, mastered_skills: Set[str]) -> Set[str]:
        """Calcula la franja exterior de un estado de conocimiento"""
        outer_fringe = set()
        
        for skill_id in self.skill_graph.nodes():
            # Si ya la domina, no está en la franja
            if skill_id in mastered_skills:
                continue
            
            # Verificar si todos los prerrequisitos están dominados
            prerequisites = set(self.skill_graph.predecessors(skill_id))
            if prerequisites.issubset(mastered_skills):
                outer_fringe.add(skill_id)
        
        return outer_fringe
    
    def get_learning_path(self, from_state: Set[str], to_skill: str) -> List[str]:
        """Encuentra el camino más corto de aprendizaje"""
        # Encontrar habilidades no dominadas necesarias
        required = set(nx.ancestors(self.skill_graph, to_skill))
        required.add(to_skill)
        to_learn = required - from_state
        
        # Ordenar topológicamente
        subgraph = self.skill_graph.subgraph(to_learn)
        return list(nx.topological_sort(subgraph))
    
    def export_to_ngsi_ld(self) -> Dict:
        """Exporta el grafo a formato NGSI-LD"""
        entity = {
            "id": "urn:ngsi-ld:KnowledgeSpace:MOD_TBM",
            "type": "KnowledgeSpace",
            "moduleId": {
                "type": "Property",
                "value": "MOD_TBM"
            },
            "totalSkills": {
                "type": "Property",
                "value": len(self.skill_graph.nodes())
            },
            "skills": {
                "type": "Property",
                "value": [
                    {
                        "id": skill_id,
                        "name": self.skills_db[skill_id].name,
                        "prerequisites": list(self.skill_graph.predecessors(skill_id))
                    }
                    for skill_id in self.skill_graph.nodes()
                ]
            },
            "@context": [
                "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld",
                "https://vocational-dataspace.es/contexts/pkst-context.jsonld"
            ]
        }
        return entity
```

---

## 7. Generación Automática de Problemas con LLMs

### 7.1 Estrategia de Generación

#### **Flujo de Generación:**

```
Habilidades target → Prompt Engineering → LLM (Claude/GPT-4) 
                                              ↓
                                   Problema + Rúbrica (JSON)
                                              ↓
                                    Validación humana
                                              ↓
                                    Almacenamiento en BD
```

### 7.2 Sistema de Prompts

#### **7.2.1 Prompt Principal (Few-Shot)**

```python
PROBLEM_GENERATION_PROMPT_TEMPLATE = """
Eres un experto en diseño de problemas educativos para Formación Profesional en la especialidad de Comercio y Marketing.

# CONTEXTO
- Módulo: Técnicas Básicas de Merchandising
- Nivel: Ciclo Formativo de Grado Medio
- Edad estudiantes: 16-20 años

# TAREA
Genera un problema práctico que permita evaluar las siguientes habilidades procedimentales:

{skill_descriptions}

# REQUISITOS DEL PROBLEMA
1. **Contexto realista:** Situación que un técnico en comercio enfrentaría en el mundo real
2. **Secuencia clara:** El problema debe requerir varios pasos de resolución
3. **Evaluable:** Cada habilidad debe ser observable en la respuesta del estudiante
4. **Nivel de dificultad:** {difficulty_level}
5. **Tiempo estimado:** {estimated_minutes} minutos

# FORMATO DE SALIDA (JSON)
Devuelve ÚNICAMENTE un JSON válido con esta estructura:

{{
  "title": "Título corto y descriptivo",
  "description": "Enunciado completo en Markdown (incluye contexto, datos, tareas)",
  "required_skills": ["H3.1.1", "H3.1.3", ...],
  "difficulty": "easy|medium|hard",
  "estimated_time_minutes": 30,
  "rubric": {{
    "criteria": [
      {{
        "skill_id": "H3.1.1",
        "criterion_name": "Identificación del público objetivo",
        "max_points": 10,
        "levels": [
          {{"score": 10, "description": "Identifica correctamente edad, estilo de vida y motivaciones con justificación sólida"}},
          {{"score": 7, "description": "Identifica correctamente 2 de 3 aspectos"}},
          {{"score": 4, "description": "Identificación parcial o genérica"}},
          {{"score": 0, "description": "No identifica el público objetivo"}}
        ]
      }},
      ...
    ]
  }},
  "solution_example": "Ejemplo de respuesta correcta (para validación)",
  "common_errors": ["Error típico 1", "Error típico 2"]
}}

# EJEMPLOS DE PROBLEMAS SIMILARES
{few_shot_examples}

# COMIENZA LA GENERACIÓN
"""

# Few-shot examples
FEW_SHOT_EXAMPLES = """
## Ejemplo 1: Análisis de circulación en tienda
**Habilidades:** H1.2 (Analizar circulación), H1.3 (Identificar zonas calientes/frías)
**Problema:**
"Dado el plano de una tienda de ropa de 200m² con 3 entradas, analiza el flujo de clientes y propón la ubicación óptima para: (1) zona de cajas, (2) productos de impulso, (3) probadores..."

## Ejemplo 2: Selección cromática para escaparate
**Habilidades:** H3.3.1 (Paleta cromática), H3.3.2 (Psicología del color)
**Problema:**
"Una marca de cosmética natural quiere transmitir valores de sostenibilidad y frescura. Propón una paleta cromática principal y secundaria justificando tu elección según principios de psicología del color..."
"""
```

#### **7.2.2 Prompt de Validación**

```python
PROBLEM_VALIDATION_PROMPT = """
Evalúa si el siguiente problema cumple con los criterios de calidad:

# PROBLEMA GENERADO
{generated_problem}

# CRITERIOS DE VALIDACIÓN
1. **Claridad:** ¿El enunciado es comprensible para un estudiante de 16-20 años?
2. **Realismo:** ¿La situación es creíble y profesional?
3. **Alineación:** ¿El problema realmente evalúa las habilidades indicadas?
4. **Rúbrica:** ¿La rúbrica es específica y aplicable?
5. **Originalidad:** ¿No es un copia literal de ejemplos conocidos?

# RESPUESTA (JSON)
{{
  "is_valid": true|false,
  "score_clarity": 1-5,
  "score_realism": 1-5,
  "score_alignment": 1-5,
  "score_rubric_quality": 1-5,
  "issues_found": ["Problema 1", "Problema 2", ...],
  "suggestions": ["Sugerencia de mejora 1", ...]
}}
"""
```

### 7.3 Implementación del Generador

```python
import anthropic
import json
from typing import List

class LLMProblemGenerator:
    def __init__(self, api_key: str, model: str = "claude-3-5-sonnet-20241022"):
        self.client = anthropic.Anthropic(api_key=api_key)
        self.model = model
    
    def generate_problem(
        self,
        target_skills: List[Skill],
        difficulty: ProblemDifficulty = ProblemDifficulty.MEDIUM,
        estimated_minutes: int = 30
    ) -> Problem:
        """Genera un problema usando Claude"""
        
        # Preparar descripciones de habilidades
        skill_descriptions = "\n".join([
            f"- **{skill.id}:** {skill.name}\n  {skill.description}"
            for skill in target_skills
        ])
        
        # Construir prompt
        prompt = PROBLEM_GENERATION_PROMPT_TEMPLATE.format(
            skill_descriptions=skill_descriptions,
            difficulty_level=difficulty.value,
            estimated_minutes=estimated_minutes,
            few_shot_examples=FEW_SHOT_EXAMPLES
        )
        
        # Llamar a Claude
        response = self.client.messages.create(
            model=self.model,
            max_tokens=4000,
            temperature=0.7,  # Creatividad moderada
            messages=[
                {"role": "user", "content": prompt}
            ]
        )
        
        # Parsear respuesta JSON
        try:
            problem_data = json.loads(response.content[0].text)
        except json.JSONDecodeError:
            # Si Claude devuelve Markdown code block, extraer
            text = response.content[0].text
            json_match = re.search(r'```json\n(.*?)\n```', text, re.DOTALL)
            if json_match:
                problem_data = json.loads(json_match.group(1))
            else:
                raise ValueError("No se pudo parsear la respuesta del LLM")
        
        # Crear objeto Problem
        problem = Problem(
            id=f"prob_{uuid.uuid4().hex[:8]}",
            title=problem_data['title'],
            description=problem_data['description'],
            required_skills=[skill.id for skill in target_skills],
            primary_skill=target_skills[0].id,
            difficulty=difficulty,
            estimated_time_minutes=problem_data['estimated_time_minutes'],
            rubric=problem_data['rubric'],
            created_by=self.model,
            created_at=datetime.now(),
            generation_prompt=prompt,
            llm_model=self.model
        )
        
        return problem
    
    def validate_problem(self, problem: Problem) -> Dict:
        """Valida automáticamente un problema generado"""
        
        validation_prompt = PROBLEM_VALIDATION_PROMPT.format(
            generated_problem=json.dumps(problem.dict(), indent=2)
        )
        
        response = self.client.messages.create(
            model=self.model,
            max_tokens=1000,
            temperature=0.3,  # Más determinista para validación
            messages=[
                {"role": "user", "content": validation_prompt}
            ]
        )
        
        validation_result = json.loads(response.content[0].text)
        return validation_result
    
    def batch_generate(
        self,
        skill_combinations: List[List[Skill]],
        problems_per_combination: int = 3
    ) -> List[Problem]:
        """Genera lotes de problemas"""
        all_problems = []
        
        for skills in skill_combinations:
            for difficulty in [ProblemDifficulty.EASY, ProblemDifficulty.MEDIUM, ProblemDifficulty.HARD]:
                for _ in range(problems_per_combination):
                    try:
                        problem = self.generate_problem(skills, difficulty)
                        validation = self.validate_problem(problem)
                        
                        if validation['is_valid'] and validation['score_alignment'] >= 4:
                            all_problems.append(problem)
                        else:
                            print(f"Problema rechazado: {validation['issues_found']}")
                    
                    except Exception as e:
                        print(f"Error generando problema: {e}")
                        continue
        
        return all_problems
```

### 7.4 Estrategia de Cobertura

Para garantizar cobertura completa del módulo:

```python
def generate_complete_problem_bank(knowledge_space: KnowledgeSpaceBuilder):
    """Genera banco de problemas que cubre todo el espacio de conocimiento"""
    
    generator = LLMProblemGenerator(api_key=os.getenv("ANTHROPIC_API_KEY"))
    
    # Estrategia 1: Habilidades individuales (nivel básico)
    single_skill_combinations = [[skill] for skill in knowledge_space.skills_db.values()]
    
    # Estrategia 2: Pares de habilidades con relación de prerrequisito
    prerequisite_pairs = [
        [knowledge_space.skills_db[prereq], knowledge_space.skills_db[skill]]
        for skill in knowledge_space.skill_graph.nodes()
        for prereq in knowledge_space.skill_graph.predecessors(skill)
    ]
    
    # Estrategia 3: Clusters de habilidades del mismo CE
    ce_clusters = defaultdict(list)
    for skill in knowledge_space.skills_db.values():
        ce_clusters[skill.criterion_id].append(skill)
    ce_combinations = list(ce_clusters.values())
    
    # Estrategia 4: Problemas integradores (5-7 habilidades)
    integrative_combinations = [
        random.sample(list(knowledge_space.skills_db.values()), k=random.randint(5, 7))
        for _ in range(20)  # 20 problemas integradores
    ]
    
    # Generar todos
    all_combinations = (
        single_skill_combinations +
        prerequisite_pairs +
        ce_combinations +
        integrative_combinations
    )
    
    problems = generator.batch_generate(all_combinations, problems_per_combination=2)
    
    print(f"✅ Generados {len(problems)} problemas cubriendo {len(knowledge_space.skills_db)} habilidades")
    
    return problems
```

**Estimación para MVP:**
- **300 habilidades** × 2 problemas/habilidad = **600 problemas básicos**
- **+100 problemas integradores**
- **Total: ~700 problemas**

**Coste estimado Claude 3.5 Sonnet:**
- 700 problemas × $0.015/problema ≈ **$10.50 USD**
- Tiempo generación: ~2-3 horas (con validación automática)

---

## 8. Sistema de Evaluación mediante Rúbricas

### 8.1 Estructura de Rúbricas

#### **Definición Formal:**

```python
from pydantic import BaseModel
from typing import List

class RubricLevel(BaseModel):
    score: int = Field(..., ge=0, description="Puntos asignados a este nivel")
    description: str = Field(..., description="Descripción del nivel de desempeño")
    keywords: List[str] = Field(default=[], description="Palabras clave para detección automática")

class RubricCriterion(BaseModel):
    skill_id: str = Field(..., description="Habilidad que evalúa")
    criterion_name: str
    max_points: int = Field(..., gt=0)
    weight: float = Field(default=1.0, ge=0, le=1, description="Peso relativo del criterio")
    
    levels: List[RubricLevel] = Field(..., min_items=2, description="Niveles de desempeño ordenados de mayor a menor")
    
    # Auto-evaluación
    auto_evaluation_type: str = Field(
        default="manual",
        description="manual | keyword_matching | llm_assisted | code_execution"
    )
    auto_evaluation_config: Optional[Dict] = None

class Rubric(BaseModel):
    problem_id: str
    total_points: int = Field(..., gt=0)
    criteria: List[RubricCriterion]
    
    def calculate_total_points(self) -> int:
        return sum(c.max_points * c.weight for c in self.criteria)
```

#### **Ejemplo de Rúbrica Completa:**

```json
{
  "problem_id": "prob_escaparate_001",
  "total_points": 100,
  "criteria": [
    {
      "skill_id": "H3.1.3",
      "criterion_name": "Identificación del público objetivo",
      "max_points": 20,
      "weight": 1.0,
      "auto_evaluation_type": "keyword_matching",
      "auto_evaluation_config": {
        "required_keywords": ["edad", "estilo de vida", "motivaciones"],
        "target_age_range": [20, 35]
      },
      "levels": [
        {
          "score": 20,
          "description": "Identifica correctamente edad, estilo de vida, motivaciones y perfil psicográfico con justificación sólida",
          "keywords": ["20-35 años", "corredores", "activo", "salud", "rendimiento"]
        },
        {
          "score": 15,
          "description": "Identifica correctamente edad y estilo de vida, motivaciones genéricas",
          "keywords": ["jóvenes", "deporte", "running"]
        },
        {
          "score": 10,
          "description": "Identificación parcial o demasiado genérica",
          "keywords": ["adultos", "deportistas"]
        },
        {
          "score": 0,
          "description": "No identifica el público objetivo o es completamente erróneo",
          "keywords": []
        }
      ]
    },
    {
      "skill_id": "H3.2.2",
      "criterion_name": "Propuesta de layout del escaparate",
      "max_points": 30,
      "weight": 1.0,
      "auto_evaluation_type": "llm_assisted",
      "auto_evaluation_config": {
        "evaluation_prompt": "Evalúa si el layout propuesto respeta principios de composición (regla de tercios, equilibrio visual, punto focal)..."
      },
      "levels": [
        {
          "score": 30,
          "description": "Layout bien estructurado con distribución clara de productos, aplicación correcta de reglas de composición, especifica cantidades y posiciones exactas"
        },
        {
          "score": 20,
          "description": "Layout funcional pero con errores menores en composición o falta detalle en posiciones"
        },
        {
          "score": 10,
          "description": "Layout básico sin aplicación evidente de principios de composición"
        },
        {
          "score": 0,
          "description": "No propone layout o es incoherente"
        }
      ]
    },
    {
      "skill_id": "H3.3.1",
      "criterion_name": "Selección de paleta cromática",
      "max_points": 25,
      "weight": 1.0,
      "auto_evaluation_type": "keyword_matching",
      "auto_evaluation_config": {
        "color_categories": ["azul", "verde", "naranja", "rojo", "amarillo"],
        "requires_justification": true
      },
      "levels": [
        {
          "score": 25,
          "description": "Paleta coherente con la marca y público objetivo, justificación basada en psicología del color, colores primarios y secundarios especificados",
          "keywords": ["azul", "energía", "dinamismo", "confianza", "tecnología"]
        },
        {
          "score": 18,
          "description": "Paleta apropiada con justificación parcial"
        },
        {
          "score": 10,
          "description": "Paleta genérica o sin justificación"
        },
        {
          "score": 0,
          "description": "Paleta inapropiada o no especificada"
        }
      ]
    },
    {
      "skill_id": "H3.4.1",
      "criterion_name": "Justificación según principios teóricos",
      "max_points": 25,
      "weight": 1.0,
      "auto_evaluation_type": "llm_assisted",
      "levels": [
        {
          "score": 25,
          "description": "Justificación completa con referencias explícitas a principios de psicología del color, comportamiento del consumidor, y técnicas de merchandising visual"
        },
        {
          "score": 18,
          "description": "Justificación adecuada con al menos 2 principios teóricos mencionados"
        },
        {
          "score": 10,
          "description": "Justificación superficial o basada solo en sentido común"
        },
        {
          "score": 0,
          "description": "Sin justificación o justificación errónea"
        }
      ]
    }
  ]
}
```

### 8.2 Motor de Evaluación Automática

```python
import re
from typing import Dict, Tuple

class RubricEvaluator:
    def __init__(self, llm_client: Optional[anthropic.Anthropic] = None):
        self.llm_client = llm_client
    
    def evaluate_submission(
        self,
        problem: Problem,
        student_response: str,
        student_id: str
    ) -> Tuple[Dict[str, int], int, List[str]]:
        """
        Evalúa una respuesta de estudiante según la rúbrica del problema
        
        Returns:
            - scores_by_skill: {skill_id: points_obtained}
            - total_score: puntuación total
            - feedback: lista de comentarios por criterio
        """
        rubric = Rubric(**problem.rubric)
        scores_by_skill = {}
        feedback = []
        total_score = 0
        
        for criterion in rubric.criteria:
            skill_id = criterion.skill_id
            
            # Seleccionar método de evaluación
            if criterion.auto_evaluation_type == "keyword_matching":
                score, comment = self._evaluate_by_keywords(student_response, criterion)
            
            elif criterion.auto_evaluation_type == "llm_assisted":
                score, comment = self._evaluate_with_llm(student_response, criterion)
            
            elif criterion.auto_evaluation_type == "code_execution":
                score, comment = self._evaluate_by_code(student_response, criterion)
            
            else:  # manual
                score, comment = None, "Requiere evaluación manual del profesor"
            
            if score is not None:
                scores_by_skill[skill_id] = score
                total_score += score * criterion.weight
                feedback.append(f"**{criterion.criterion_name}:** {score}/{criterion.max_points} - {comment}")
        
        return scores_by_skill, total_score, feedback
    
    def _evaluate_by_keywords(
        self,
        response: str,
        criterion: RubricCriterion
    ) -> Tuple[int, str]:
        """Evaluación basada en presencia de palabras clave"""
        response_lower = response.lower()
        
        # Iterar niveles de mayor a menor score
        for level in sorted(criterion.levels, key=lambda x: x.score, reverse=True):
            # Contar keywords presentes
            keywords_found = [kw for kw in level.keywords if kw.lower() in response_lower]
            
            # Si encuentra al menos 50% de keywords del nivel, asignar ese score
            if len(keywords_found) >= len(level.keywords) * 0.5:
                comment = f"Nivel alcanzado: {level.description}. Keywords encontradas: {', '.join(keywords_found)}"
                return level.score, comment
        
        # Si no cumple ningún nivel, devolver 0
        return 0, "No se identificaron elementos clave en la respuesta"
    
    def _evaluate_with_llm(
        self,
        response: str,
        criterion: RubricCriterion
    ) -> Tuple[int, str]:
        """Evaluación asistida por LLM"""
        if not self.llm_client:
            return None, "LLM no disponible"
        
        # Construir prompt de evaluación
        eval_prompt = f"""
Evalúa la siguiente respuesta de un estudiante según este criterio:

**Criterio:** {criterion.criterion_name}
**Habilidad evaluada:** {criterion.skill_id}

**Niveles de desempeño:**
{self._format_levels_for_prompt(criterion.levels)}

**Respuesta del estudiante:**
{response}

**Tarea:**
1. Determina qué nivel de desempeño alcanza la respuesta
2. Asigna la puntuación correspondiente
3. Proporciona un breve comentario justificando la evaluación

**Formato de salida (JSON):**
{{
  "level_achieved": 0-{criterion.max_points},
  "score": 0-{criterion.max_points},
  "justification": "Breve explicación"
}}
"""
        
        try:
            response_llm = self.llm_client.messages.create(
                model="claude-3-5-sonnet-20241022",
                max_tokens=500,
                temperature=0.3,
                messages=[{"role": "user", "content": eval_prompt}]
            )
            
            result = json.loads(response_llm.content[0].text)
            return result['score'], result['justification']
        
        except Exception as e:
            print(f"Error en evaluación LLM: {e}")
            return None, f"Error en evaluación automática: {str(e)}"
    
    def _evaluate_by_code(
        self,
        response: str,
        criterion: RubricCriterion
    ) -> Tuple[int, str]:
        """Evaluación ejecutando código (para problemas de programación)"""
        # Implementación específica para evaluación de código
        # Por ahora, placeholder
        return None, "Evaluación por código no implementada en MVP"
    
    def _format_levels_for_prompt(self, levels: List[RubricLevel]) -> str:
        return "\n".join([
            f"- **{level.score} puntos:** {level.description}"
            for level in sorted(levels, key=lambda x: x.score, reverse=True)
        ])
    
    def update_knowledge_state(
        self,
        student_id: str,
        scores_by_skill: Dict[str, int],
        skill_mastery_threshold: float = 0.7
    ):
        """Actualiza el estado de conocimiento del estudiante basándose en la evaluación"""
        # Obtener estado actual
        knowledge_state = get_knowledge_state(student_id)  # Función auxiliar
        
        for skill_id, score in scores_by_skill.items():
            skill = get_skill(skill_id)  # Función auxiliar
            max_score = 25  # Simplificación, debería obtenerse del criterio
            
            performance_ratio = score / max_score
            
            # Determinar si dominó la habilidad
            if performance_ratio >= skill_mastery_threshold:
                knowledge_state.mastered_skills.add(skill_id)
                knowledge_state.in_progress_skills.discard(skill_id)
            
            elif performance_ratio >= 0.4:  # En progreso
                knowledge_state.in_progress_skills.add(skill_id)
            
            # Si score muy bajo y estaba en mastered, mover a in_progress (olvido)
            elif skill_id in knowledge_state.mastered_skills and performance_ratio < 0.3:
                knowledge_state.mastered_skills.discard(skill_id)
                knowledge_state.in_progress_skills.add(skill_id)
        
        # Recalcular franja exterior
        knowledge_space = get_knowledge_space_builder()  # Función auxiliar
        knowledge_state.outer_fringe = knowledge_space.calculate_outer_fringe(
            knowledge_state.mastered_skills
        )
        
        # Actualizar métricas
        knowledge_state.total_problems_attempted += 1
        if sum(scores_by_skill.values()) >= sum([25] * len(scores_by_skill)) * 0.7:
            knowledge_state.total_problems_solved += 1
        
        knowledge_state.competence_score = (
            len(knowledge_state.mastered_skills) / 300  # Total habilidades del módulo
        ) * 100
        
        knowledge_state.last_updated = datetime.now()
        
        # Persistir
        save_knowledge_state(knowledge_state)  # Función auxiliar
```

---

## 9. Motor de Recomendación Adaptativa

### 9.1 Algoritmo de Recomendación

```python
from typing import List, Optional
from collections import defaultdict

class AdaptiveRecommendationEngine:
    def __init__(self, knowledge_space: KnowledgeSpaceBuilder):
        self.knowledge_space = knowledge_space
        self.problem_history = defaultdict(list)  # {student_id: [problems_attempted]}
    
    def recommend_next_problem(
        self,
        student_id: str,
        n_recommendations: int = 3
    ) -> List[Problem]:
        """
        Recomienda los próximos N problemas óptimos para el estudiante
        """
        knowledge_state = get_knowledge_state(student_id)
        outer_fringe = knowledge_state.outer_fringe
        
        # Si no hay franja exterior (dominó todo o no tiene prerrequisitos cumplidos)
        if not outer_fringe:
            return self._handle_edge_cases(knowledge_state)
        
        # Obtener problemas candidatos que cubran habilidades de la franja
        candidate_problems = self._filter_problems_by_fringe(outer_fringe)
        
        # Eliminar problemas ya intentados recientemente
        recent_problems = set(self.problem_history[student_id][-10:])
        candidate_problems = [p for p in candidate_problems if p.id not in recent_problems]
        
        # Analizar rendimiento reciente del estudiante
        performance_profile = self._analyze_recent_performance(student_id, n_last=5)
        
        # Estrategia de selección adaptativa
        if performance_profile['avg_score'] > 0.85 and performance_profile['avg_time_ratio'] < 0.8:
            # Estudiante sobresaliente: problemas más complejos
            selected = self._select_challenging_problems(candidate_problems, n=n_recommendations)
        
        elif performance_profile['avg_score'] < 0.50:
            # Estudiante con dificultades: reforzar prerrequisitos débiles
            weak_skills = self._identify_weak_skills(knowledge_state)
            selected = self._select_reinforcement_problems(weak_skills, n=n_recommendations)
        
        elif performance_profile['struggling_skills']:
            # Tiene dificultades con habilidades específicas
            selected = self._select_targeted_practice(
                performance_profile['struggling_skills'],
                n=n_recommendations
            )
        
        else:
            # Rendimiento medio: problemas balanceados de la franja
            selected = self._select_balanced_problems(candidate_problems, n=n_recommendations)
        
        # Registrar recomendación
        for problem in selected:
            self.problem_history[student_id].append(problem.id)
        
        return selected
    
    def _filter_problems_by_fringe(self, outer_fringe: Set[str]) -> List[Problem]:
        """Filtra problemas que cubran habilidades de la franja exterior"""
        all_problems = get_all_problems()  # Función auxiliar
        
        filtered = []
        for problem in all_problems:
            # Verificar si al menos 1 habilidad requerida está en la franja
            if any(skill_id in outer_fringe for skill_id in problem.required_skills):
                filtered.append(problem)
        
        return filtered
    
    def _analyze_recent_performance(self, student_id: str, n_last: int = 5) -> Dict:
        """Analiza el rendimiento en los últimos N problemas"""
        submissions = get_recent_submissions(student_id, n=n_last)  # Función auxiliar
        
        if not submissions:
            return {
                'avg_score': 0.5,  # Neutro
                'avg_time_ratio': 1.0,
                'struggling_skills': set()
            }
        
        scores = [s.normalized_score for s in submissions]  # 0-1
        time_ratios = [s.actual_time / s.problem.estimated_time_minutes for s in submissions]
        
        # Identificar habilidades con las que lucha
        skill_scores = defaultdict(list)
        for submission in submissions:
            for skill_id, score in submission.scores_by_skill.items():
                skill_scores[skill_id].append(score / 25)  # Normalizar
        
        struggling_skills = {
            skill_id for skill_id, scores_list in skill_scores.items()
            if sum(scores_list) / len(scores_list) < 0.5
        }
        
        return {
            'avg_score': sum(scores) / len(scores),
            'avg_time_ratio': sum(time_ratios) / len(time_ratios),
            'struggling_skills': struggling_skills
        }
    
    def _select_challenging_problems(self, candidates: List[Problem], n: int) -> List[Problem]:
        """Selecciona problemas más complejos"""
        # Filtrar por dificultad HARD o MEDIUM con muchas habilidades
        challenging = [
            p for p in candidates
            if p.difficulty == ProblemDifficulty.HARD or len(p.required_skills) >= 5
        ]
        
        # Ordenar por complejidad (más habilidades requeridas = más complejo)
        challenging.sort(key=lambda p: len(p.required_skills), reverse=True)
        
        return challenging[:n]
    
    def _identify_weak_skills(self, knowledge_state: KnowledgeState) -> Set[str]:
        """Identifica habilidades débiles (en el borde de ser olvidadas)"""
        # Obtener inner fringe (habilidades que se pueden perder)
        inner_fringe = set()
        for skill_id in knowledge_state.mastered_skills:
            # Si no tiene dependientes dominados, está en el borde
            dependents = set(self.knowledge_space.skill_graph.successors(skill_id))
            if not dependents.intersection(knowledge_state.mastered_skills):
                inner_fringe.add(skill_id)
        
        return inner_fringe
    
    def _select_reinforcement_problems(self, weak_skills: Set[str], n: int) -> List[Problem]:
        """Selecciona problemas que refuercen habilidades débiles"""
        all_problems = get_all_problems()
        
        reinforcement = [
            p for p in all_problems
            if any(skill_id in weak_skills for skill_id in p.required_skills)
            and p.difficulty == ProblemDifficulty.EASY
        ]
        
        return reinforcement[:n]
    
    def _select_targeted_practice(self, struggling_skills: Set[str], n: int) -> List[Problem]:
        """Selecciona problemas específicos para habilidades problemáticas"""
        all_problems = get_all_problems()
        
        targeted = [
            p for p in all_problems
            if p.primary_skill in struggling_skills
            and p.difficulty in [ProblemDifficulty.EASY, ProblemDifficulty.MEDIUM]
        ]
        
        return targeted[:n]
    
    def _select_balanced_problems(self, candidates: List[Problem], n: int) -> List[Problem]:
        """Selección balanceada: variedad de habilidades y dificultades"""
        # Diversificar por habilidad primaria
        skills_covered = set()
        selected = []
        
        for problem in candidates:
            if problem.primary_skill not in skills_covered:
                selected.append(problem)
                skills_covered.add(problem.primary_skill)
            
            if len(selected) >= n:
                break
        
        # Si no hay suficientes, completar aleatoriamente
        if len(selected) < n:
            remaining = [p for p in candidates if p not in selected]
            selected.extend(random.sample(remaining, min(n - len(selected), len(remaining))))
        
        return selected
    
    def _handle_edge_cases(self, knowledge_state: KnowledgeState) -> List[Problem]:
        """Maneja casos especiales (dominio completo, sin prerrequisitos cumplidos)"""
        total_skills = len(self.knowledge_space.skill_graph.nodes())
        mastered_ratio = len(knowledge_state.mastered_skills) / total_skills
        
        if mastered_ratio > 0.95:
            # Dominó casi todo: problemas integradores complejos
            return self._get_integrative_problems(n=3)
        
        else:
            # No cumple prerrequisitos: empezar por habilidades básicas (sin prerrequisitos)
            basic_skills = [
                skill_id for skill_id in self.knowledge_space.skill_graph.nodes()
                if self.knowledge_space.skill_graph.in_degree(skill_id) == 0
            ]
            all_problems = get_all_problems()
            basic_problems = [
                p for p in all_problems
                if p.primary_skill in basic_skills and p.difficulty == ProblemDifficulty.EASY
            ]
            return basic_problems[:3]
    
    def _get_integrative_problems(self, n: int) -> List[Problem]:
        """Obtiene problemas integradores (muchas habilidades)"""
        all_problems = get_all_problems()
        integrative = [
            p for p in all_problems
            if len(p.required_skills) >= 7  # Problemas complejos
        ]
        return integrative[:n]
    
    def explain_recommendation(self, problem: Problem, knowledge_state: KnowledgeState) -> str:
        """Genera explicación de por qué se recomienda este problema"""
        explanations = []
        
        # Habilidades nuevas que aprenderá
        new_skills = [
            skill_id for skill_id in problem.required_skills
            if skill_id not in knowledge_state.mastered_skills
        ]
        
        if new_skills:
            new_skills_names = [
                self.knowledge_space.skills_db[sid].name for sid in new_skills[:3]
            ]
            explanations.append(
                f"**Nuevas habilidades:** Practicarás {', '.join(new_skills_names)}"
            )
        
        # Habilidades que refuerza
        reinforced_skills = [
            skill_id for skill_id in problem.required_skills
            if skill_id in knowledge_state.mastered_skills
        ]
        
        if reinforced_skills:
            explanations.append(
                f"**Refuerzo:** Consolidarás {len(reinforced_skills)} habilidades ya adquiridas"
            )
        
        # Nivel de dificultad
        explanations.append(f"**Dificultad:** {problem.difficulty.value.capitalize()}")
        
        # Progreso esperado
        expected_progress = (len(new_skills) / 300) * 100  # % de avance en el módulo
        explanations.append(f"**Progreso:** +{expected_progress:.1f}% al completar este problema")
        
        return "\n".join(explanations)
```

### 9.2 Lógica de Priorización

**Factores considerados en la recomendación:**

1. **Franja exterior (outer fringe):** Garantía de que el problema es alcanzable
2. **Rendimiento reciente:** Adapta dificultad según éxito/fracaso
3. **Tiempo de resolución:** Detecta frustración (mucho tiempo) o falta de reto (muy rápido)
4. **Variedad:** Evita repetir la misma habilidad constantemente
5. **Patrones de error:** Identifica habilidades problemáticas para práctica dirigida

---

## 10. Especificación de APIs

### 10.1 API REST del Sistema PKST

#### **Base URL:** `https://pkst-engine.vocational-dataspace.es/api/v1`

#### **Autenticación:** Bearer Token (JWT desde Keycloak)

---

#### **10.1.1 Gestión de Estudiantes y Estados de Conocimiento**

**GET /students/{student_id}/knowledge-state**

Obtiene el estado de conocimiento actual del estudiante.

```json
// Response 200 OK
{
  "student_id": "student_12345",
  "module_id": "MOD_TBM",
  "mastered_skills": ["H3.1.1", "H3.1.2", "H3.1.3"],
  "in_progress_skills": ["H3.1.4"],
  "outer_fringe": ["H3.1.5", "H3.2.1"],
  "competence_score": 35.2,
  "total_problems_attempted": 18,
  "total_problems_solved": 14,
  "last_updated": "2026-02-08T10:30:00Z"
}
```

---

**POST /students/{student_id}/knowledge-state/initialize**

Inicializa el estado de conocimiento de un estudiante nuevo.

```json
// Request
{
  "module_id": "MOD_TBM",
  "initial_assessment": {
    "previous_experience": "none|some|extensive",
    "self_reported_skills": ["H3.1.1"]  // Opcional
  }
}

// Response 201 Created
{
  "student_id": "student_12345",
  "knowledge_state_id": "ks_xyz789",
  "message": "Knowledge state initialized successfully"
}
```

---

#### **10.1.2 Recomendación de Problemas**

**GET /students/{student_id}/recommendations**

Obtiene problemas recomendados para el estudiante.

**Query params:**
- `n`: número de recomendaciones (default: 3, max: 10)
- `difficulty`: filtro opcional (easy|medium|hard)

```json
// Response 200 OK
{
  "student_id": "student_12345",
  "recommendations": [
    {
      "problem_id": "prob_001",
      "title": "Diseño de escaparate para tienda de calzado deportivo",
      "difficulty": "medium",
      "estimated_time_minutes": 30,
      "required_skills": ["H3.1.1", "H3.1.3", "H3.2.2", "H3.3.1"],
      "new_skills_to_learn": ["H3.3.1"],
      "explanation": "**Nuevas habilidades:** Practicarás Selección de paleta cromática\n**Refuerzo:** Consolidarás 3 habilidades ya adquiridas\n**Dificultad:** Medium\n**Progreso:** +1.3% al completar este problema",
      "priority": 1
    },
    ...
  ],
  "reasoning": "Basado en tu franja de aprendizaje actual (outer fringe)"
}
```

---

#### **10.1.3 Envío y Evaluación de Soluciones**

**POST /problems/{problem_id}/submit**

Envía la solución de un estudiante para evaluación.

```json
// Request
{
  "student_id": "student_12345",
  "response": {
    "format": "text|json|markdown|file_url",
    "content": "Mi respuesta al problema...",
    "attachments": [
      "https://storage.vocational-dataspace.es/submissions/img_001.png"
    ]
  },
  "time_spent_minutes": 35,
  "metadata": {
    "started_at": "2026-02-08T10:00:00Z",
    "completed_at": "2026-02-08T10:35:00Z"
  }
}

// Response 200 OK
{
  "submission_id": "sub_abc123",
  "evaluation_status": "completed|pending_manual_review",
  "scores_by_skill": {
    "H3.1.1": 18,
    "H3.1.3": 15,
    "H3.2.2": 22,
    "H3.3.1": 20
  },
  "total_score": 75,
  "max_possible_score": 100,
  "normalized_score": 0.75,
  "feedback": [
    "**Identificación del público objetivo:** 18/20 - Nivel alcanzado: Identifica correctamente edad, estilo de vida, motivaciones...",
    "**Propuesta de layout del escaparate:** 22/30 - Layout funcional pero con errores menores..."
  ],
  "knowledge_state_updated": true,
  "new_mastered_skills": ["H3.3.1"],
  "next_recommendation": "prob_007"
}
```

---

**GET /submissions/{submission_id}**

Consulta el estado de una evaluación.

```json
// Response 200 OK
{
  "submission_id": "sub_abc123",
  "student_id": "student_12345",
  "problem_id": "prob_001",
  "submitted_at": "2026-02-08T10:35:00Z",
  "evaluation_status": "completed",
  "total_score": 75,
  "feedback": [...],
  "requires_manual_review": false,
  "reviewed_by": "system"
}
```

---

#### **10.1.4 Gestión de Problemas**

**GET /problems**

Lista problemas con filtros opcionales.

**Query params:**
- `skill_id`: filtrar por habilidad
- `difficulty`: easy|medium|hard
- `module_id`: filtrar por módulo
- `limit`: número máximo de resultados

```json
// Response 200 OK
{
  "problems": [
    {
      "id": "prob_001",
      "title": "Diseño de escaparate para tienda de calzado deportivo",
      "difficulty": "medium",
      "required_skills": ["H3.1.1", "H3.1.3", "H3.2.2", "H3.3.1"],
      "estimated_time_minutes": 30,
      "times_used": 45,
      "average_success_rate": 0.68
    },
    ...
  ],
  "total_count": 687,
  "page": 1,
  "page_size": 20
}
```

---

**GET /problems/{problem_id}**

Obtiene los detalles completos de un problema.

```json
// Response 200 OK
{
  "id": "prob_001",
  "title": "Diseño de escaparate para tienda de calzado deportivo",
  "description": "## Contexto\nEres el responsable de merchandising...",
  "required_skills": ["H3.1.1", "H3.1.3", "H3.2.2", "H3.3.1"],
  "difficulty": "medium",
  "estimated_time_minutes": 30,
  "rubric": {...},  // Ver sección 8
  "attachments": [],
  "created_by": "claude-3.5-sonnet",
  "created_at": "2026-01-15T08:00:00Z"
}
```

---

#### **10.1.5 Visualización y Analíticas**

**GET /students/{student_id}/progress**

Obtiene métricas de progreso del estudiante.

```json
// Response 200 OK
{
  "student_id": "student_12345",
  "module_id": "MOD_TBM",
  "competence_score": 35.2,
  "skills_mastered": 15,
  "total_skills": 300,
  "progress_percentage": 5.0,
  "problems_attempted": 18,
  "problems_solved": 14,
  "success_rate": 0.78,
  "time_series": [
    {"date": "2026-02-01", "competence_score": 10.5},
    {"date": "2026-02-08", "competence_score": 35.2}
  ],
  "skill_distribution": {
    "basic": 8,
    "intermediate": 5,
    "advanced": 2
  }
}
```

---

**GET /knowledge-space/graph**

Obtiene el grafo de conocimiento completo (para visualización).

**Query params:**
- `module_id`: requerido
- `format`: json|graphml|cytoscape

```json
// Response 200 OK (format=cytoscape)
{
  "elements": {
    "nodes": [
      {"data": {"id": "H3.1.1", "label": "Clasificar productos", "level": "basic"}},
      {"data": {"id": "H3.1.2", "label": "Identificar tipo de establecimiento", "level": "basic"}},
      ...
    ],
    "edges": [
      {"data": {"source": "H3.1.1", "target": "H3.1.4"}},
      {"data": {"source": "H3.1.2", "target": "H3.1.4"}},
      ...
    ]
  }
}
```

---

### 10.2 API Externa para Plataformas de Problemas

**Para que plataformas externas (LMS, simuladores) puedan integrarse:**

#### **Webhook de Notificación de Resultados**

**POST https://external-platform.com/webhooks/problem-completed**

El sistema PKST envía resultados a la plataforma externa tras la evaluación.

```json
// Request enviado por PKST
{
  "event": "problem_completed",
  "timestamp": "2026-02-08T10:35:00Z",
  "student_id": "student_12345",
  "problem_id": "prob_001",
  "external_problem_id": "ext_xyz789",  // ID en la plataforma externa
  "submission_id": "sub_abc123",
  "evaluation": {
    "total_score": 75,
    "max_score": 100,
    "normalized_score": 0.75,
    "skills_evaluated": {
      "H3.1.1": 0.90,
      "H3.1.3": 0.75,
      "H3.2.2": 0.73,
      "H3.3.1": 0.80
    },
    "feedback": [...]
  },
  "next_recommendation": "prob_007"
}
```

---

#### **Registro de Problemas Externos**

**POST /api/v1/external-problems/register**

Permite a plataformas externas registrar sus problemas en el sistema PKST.

```json
// Request
{
  "external_platform": "moodle_instance_1",
  "external_problem_id": "quiz_456",
  "problem_metadata": {
    "title": "Test sobre psicología del color",
    "description": "...",
    "required_skills": ["H3.3.1", "H3.3.2"],
    "difficulty": "easy",
    "estimated_time_minutes": 15
  },
  "evaluation_endpoint": "https://moodle.example.com/api/submit",
  "authentication": {
    "type": "bearer_token",
    "token": "..."
  }
}

// Response 201 Created
{
  "problem_id": "prob_ext_001",
  "external_problem_id": "quiz_456",
  "status": "registered",
  "can_recommend": true
}
```

---

## 11. Integración con FIWARE

### 11.1 Modelo de Datos NGSI-LD

#### **11.1.1 Contexto JSON-LD**

Definimos un contexto específico para PKST:

**URL:** `https://vocational-dataspace.es/contexts/pkst-context.jsonld`

```json
{
  "@context": {
    "pkst": "https://vocational-dataspace.es/ontology/pkst#",
    "esco": "http://data.europa.eu/esco/",
    "schema": "https://schema.org/",
    
    "Skill": "pkst:Skill",
    "KnowledgeState": "pkst:KnowledgeState",
    "Problem": "pkst:Problem",
    "Submission": "pkst:Submission",
    
    "masteredSkills": {
      "@id": "pkst:masteredSkills",
      "@type": "@id"
    },
    "outerFringe": {
      "@id": "pkst:outerFringe",
      "@type": "@id"
    },
    "requiredSkills": {
      "@id": "pkst:requiredSkills",
      "@type": "@id"
    },
    "competenceScore": {
      "@id": "pkst:competenceScore",
      "@type": "xsd:float"
    }
  }
}
```

#### **11.1.2 Entidad: Skill en NGSI-LD**

```json
{
  "id": "urn:ngsi-ld:Skill:H3.1.1",
  "type": "Skill",
  "name": {
    "type": "Property",
    "value": "Clasificar productos por categoría"
  },
  "description": {
    "type": "Property",
    "value": "Capacidad de identificar y categorizar productos según su naturaleza..."
  },
  "criterionId": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:EvaluationCriterion:CE3.1"
  },
  "prerequisites": {
    "type": "Relationship",
    "object": []  // Sin prerrequisitos (habilidad básica)
  },
  "level": {
    "type": "Property",
    "value": "basic"
  },
  "escoSkillUri": {
    "type": "Property",
    "value": "http://data.europa.eu/esco/skill/..."
  },
  "@context": [
    "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld",
    "https://vocational-dataspace.es/contexts/pkst-context.jsonld"
  ]
}
```

#### **11.1.3 Entidad: KnowledgeState en NGSI-LD**

```json
{
  "id": "urn:ngsi-ld:KnowledgeState:student_12345_MOD_TBM",
  "type": "KnowledgeState",
  "studentId": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Student:student_12345"
  },
  "moduleId": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Module:MOD_TBM"
  },
  "masteredSkills": {
    "type": "Relationship",
    "object": [
      "urn:ngsi-ld:Skill:H3.1.1",
      "urn:ngsi-ld:Skill:H3.1.2",
      "urn:ngsi-ld:Skill:H3.1.3"
    ]
  },
  "outerFringe": {
    "type": "Relationship",
    "object": [
      "urn:ngsi-ld:Skill:H3.1.4",
      "urn:ngsi-ld:Skill:H3.2.1"
    ]
  },
  "competenceScore": {
    "type": "Property",
    "value": 35.2,
    "unitCode": "P1"  // Porcentaje
  },
  "totalProblemsAttempted": {
    "type": "Property",
    "value": 18
  },
  "totalProblemsSolved": {
    "type": "Property",
    "value": 14
  },
  "lastUpdated": {
    "type": "Property",
    "value": {
      "@type": "DateTime",
      "@value": "2026-02-08T10:30:00Z"
    }
  },
  "@context": [
    "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld",
    "https://vocational-dataspace.es/contexts/pkst-context.jsonld"
  ]
}
```

### 11.2 Suscripciones NGSI-LD

Para reaccionar en tiempo real a cambios:

```python
import requests

def create_subscription_on_knowledge_state_updates():
    """Crea suscripción para recibir notificaciones cuando se actualiza un KnowledgeState"""
    
    subscription = {
        "id": "urn:ngsi-ld:Subscription:KnowledgeStateUpdates",
        "type": "Subscription",
        "entities": [
            {
                "type": "KnowledgeState"
            }
        ],
        "watchedAttributes": ["masteredSkills", "competenceScore"],
        "notification": {
            "endpoint": {
                "uri": "https://pkst-engine.vocational-dataspace.es/api/v1/webhooks/orion-ld",
                "accept": "application/json"
            }
        },
        "@context": [
            "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld"
        ]
    }
    
    response = requests.post(
        "http://orion-ld:1026/ngsi-ld/v1/subscriptions",
        json=subscription,
        headers={"Content-Type": "application/ld+json"}
    )
    
    return response.json()
```

### 11.3 Queries Temporales

Aprovechar capacidades temporales de Orion-LD para análisis histórico:

```python
def get_competence_evolution(student_id: str, from_date: str, to_date: str):
    """Obtiene la evolución del competence score en un período"""
    
    entity_id = f"urn:ngsi-ld:KnowledgeState:{student_id}_MOD_TBM"
    
    response = requests.get(
        f"http://orion-ld:1026/ngsi-ld/v1/temporal/entities/{entity_id}",
        params={
            "timerel": "between",
            "timeAt": from_date,
            "endTimeAt": to_date,
            "attrs": "competenceScore"
        },
        headers={"Accept": "application/ld+json"}
    )
    
    # Procesar serie temporal
    data = response.json()
    time_series = [
        {"timestamp": item["observedAt"], "value": item["value"]}
        for item in data["competenceScore"]["values"]
    ]
    
    return time_series
```

### 11.4 Autenticación con Keycloak

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
import jwt

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="https://keycloak.vocational-dataspace.es/auth/realms/vocational-fp/protocol/openid-connect/token")

def verify_token(token: str = Depends(oauth2_scheme)):
    """Verifica el JWT de Keycloak"""
    try:
        # Verificar firma y validez
        payload = jwt.decode(
            token,
            key=KEYCLOAK_PUBLIC_KEY,
            algorithms=["RS256"],
            audience="pkst-api"
        )
        return payload
    
    except jwt.ExpiredSignatureError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Token expired"
        )
    
    except jwt.InvalidTokenError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token"
        )

# Uso en endpoints
@app.get("/api/v1/students/{student_id}/knowledge-state")
async def get_knowledge_state(student_id: str, current_user = Depends(verify_token)):
    # Verificar que el usuario puede acceder a este estudiante
    if current_user["sub"] != student_id and "teacher" not in current_user["realm_access"]["roles"]:
        raise HTTPException(status_code=403, detail="Forbidden")
    
    # ... lógica del endpoint
```

---

## 12. Generación de Datos Sintéticos

### 12.1 Estudiantes Sintéticos

```python
from faker import Faker
import numpy as np
import networkx as nx

fake = Faker('es_ES')

class SyntheticStudentGenerator:
    def __init__(self, skill_graph: nx.DiGraph, n_students: int = 100):
        self.skill_graph = skill_graph
        self.n_students = n_students
        self.all_skills = list(skill_graph.nodes())
    
    def generate_cohort(self) -> List[Dict]:
        """Genera una cohorte de estudiantes sintéticos con distribución realista"""
        
        students = []
        
        # Distribución realista de niveles
        distribution = {
            'beginner': 0.30,      # 30% principiantes
            'intermediate': 0.50,  # 50% intermedios
            'advanced': 0.20       # 20% avanzados
        }
        
        for i in range(self.n_students):
            level = np.random.choice(
                list(distribution.keys()),
                p=list(distribution.values())
            )
            
            student = self._generate_single_student(level, i)
            students.append(student)
        
        return students
    
    def _generate_single_student(self, level: str, index: int) -> Dict:
        """Genera un estudiante individual"""
        
        # Determinar número de habilidades dominadas según nivel
        if level == 'beginner':
            mastery_ratio = np.random.uniform(0.05, 0.20)
        elif level == 'intermediate':
            mastery_ratio = np.random.uniform(0.20, 0.60)
        else:  # advanced
            mastery_ratio = np.random.uniform(0.60, 0.90)
        
        n_mastered = int(len(self.all_skills) * mastery_ratio)
        
        # Seleccionar habilidades respetando prerrequisitos
        mastered_skills = self._select_valid_skill_set(n_mastered)
        
        # Calcular franja exterior
        outer_fringe = self._calculate_outer_fringe(mastered_skills)
        
        # Generar perfil
        student = {
            "id": f"student_{index:04d}",
            "name": fake.name(),
            "email": fake.email(),
            "age": fake.random_int(min=16, max=20),
            "level": level,
            
            "knowledge_state": {
                "module_id": "MOD_TBM",
                "mastered_skills": list(mastered_skills),
                "outer_fringe": list(outer_fringe),
                "competence_score": (len(mastered_skills) / len(self.all_skills)) * 100
            },
            
            "learning_profile": {
                "learning_style": np.random.choice(['visual', 'kinesthetic', 'auditory', 'reading']),
                "avg_problem_time_minutes": np.random.normal(25, 8),  # Media 25min, std 8min
                "consistency": np.random.uniform(0.5, 1.0),  # Qué tan consistente es
                "motivation": np.random.choice(['high', 'medium', 'low'])
            },
            
            "performance_history": self._generate_performance_history(mastered_skills, level)
        }
        
        return student
    
    def _select_valid_skill_set(self, n_skills: int) -> Set[str]:
        """Selecciona habilidades respetando estructura de prerrequisitos"""
        
        mastered = set()
        
        # Obtener habilidades ordenadas topológicamente
        topo_order = list(nx.topological_sort(self.skill_graph))
        
        # Seleccionar aleatoriamente pero garantizando prerrequisitos
        for skill_id in topo_order:
            if len(mastered) >= n_skills:
                break
            
            # Verificar si todos los prerrequisitos están dominados
            prerequisites = set(self.skill_graph.predecessors(skill_id))
            
            if prerequisites.issubset(mastered):
                # Probabilidad de dominar esta habilidad (no todas las disponibles se dominan)
                if np.random.random() < 0.7:  # 70% de probabilidad
                    mastered.add(skill_id)
        
        return mastered
    
    def _calculate_outer_fringe(self, mastered_skills: Set[str]) -> Set[str]:
        """Calcula franja exterior"""
        outer_fringe = set()
        
        for skill_id in self.all_skills:
            if skill_id in mastered_skills:
                continue
            
            prerequisites = set(self.skill_graph.predecessors(skill_id))
            if prerequisites.issubset(mastered_skills):
                outer_fringe.add(skill_id)
        
        return outer_fringe
    
    def _generate_performance_history(self, mastered_skills: Set[str], level: str) -> List[Dict]:
        """Genera historial de rendimiento sintético"""
        
        # Determinar número de problemas intentados
        if level == 'beginner':
            n_attempts = np.random.randint(5, 15)
        elif level == 'intermediate':
            n_attempts = np.random.randint(15, 40)
        else:
            n_attempts = np.random.randint(40, 80)
        
        history = []
        
        for i in range(n_attempts):
            # Simular problema
            problem_skills = np.random.choice(
                list(mastered_skills) if mastered_skills else self.all_skills,
                size=min(3, len(mastered_skills) if mastered_skills else 3),
                replace=False
            ).tolist()
            
            # Simular resultado (más probabilidad de éxito si domina las habilidades)
            overlap = len(set(problem_skills).intersection(mastered_skills))
            success_prob = overlap / len(problem_skills) if problem_skills else 0.5
            
            normalized_score = np.random.beta(
                a=success_prob * 10 + 1,  # Forma de beta distribution
                b=(1 - success_prob) * 10 + 1
            )
            
            attempt = {
                "problem_id": f"prob_{i:03d}",
                "attempted_at": fake.date_time_between(start_date='-60d', end_date='now').isoformat(),
                "skills_required": problem_skills,
                "normalized_score": round(normalized_score, 2),
                "time_spent_minutes": int(np.random.normal(25, 8))
            }
            
            history.append(attempt)
        
        return sorted(history, key=lambda x: x['attempted_at'])
```

### 12.2 Exportación a Múltiples Formatos

```python
import json
import pandas as pd

class SyntheticDataExporter:
    @staticmethod
    def to_json(students: List[Dict], filepath: str):
        """Exporta a JSON"""
        with open(filepath, 'w', encoding='utf-8') as f:
            json.dump(students, f, indent=2, ensure_ascii=False)
    
    @staticmethod
    def to_csv(students: List[Dict], filepath: str):
        """Exporta a CSV (aplanado)"""
        flat_data = []
        for student in students:
            flat = {
                "student_id": student["id"],
                "name": student["name"],
                "email": student["email"],
                "age": student["age"],
                "level": student["level"],
                "n_mastered_skills": len(student["knowledge_state"]["mastered_skills"]),
                "competence_score": student["knowledge_state"]["competence_score"],
                "learning_style": student["learning_profile"]["learning_style"],
                "n_attempts": len(student["performance_history"])
            }
            flat_data.append(flat)
        
        df = pd.DataFrame(flat_data)
        df.to_csv(filepath, index=False)
    
    @staticmethod
    def to_sql(students: List[Dict], db_connection):
        """Exporta a base de datos SQL"""
        # Insertar estudiantes
        for student in students:
            # INSERT INTO students ...
            # INSERT INTO knowledge_states ...
            # INSERT INTO performance_history ...
            pass  # Implementación específica según ORM
    
    @staticmethod
    def to_ngsi_ld(students: List[Dict], orion_ld_url: str):
        """Exporta directamente a Orion-LD"""
        for student in students:
            # Convertir a entidad NGSI-LD
            entity = {
                "id": f"urn:ngsi-ld:Student:{student['id']}",
                "type": "Student",
                "name": {"type": "Property", "value": student["name"]},
                # ... resto de propiedades
            }
            
            # POST a Orion-LD
            requests.post(
                f"{orion_ld_url}/ngsi-ld/v1/entities",
                json=entity,
                headers={"Content-Type": "application/ld+json"}
            )
```

### 12.3 Script de Generación Completo

```python
# generate_synthetic_data.py

if __name__ == "__main__":
    # 1. Cargar grafo de habilidades
    kb = KnowledgeSpaceBuilder()
    kb.build_from_curriculum(load_curriculum("MOD_TBM"))  # Función auxiliar
    
    # 2. Generar estudiantes
    generator = SyntheticStudentGenerator(kb.skill_graph, n_students=100)
    students = generator.generate_cohort()
    
    print(f"✅ Generados {len(students)} estudiantes sintéticos")
    
    # 3. Generar problemas
    problem_gen = LLMProblemGenerator(api_key=os.getenv("ANTHROPIC_API_KEY"))
    problems = generate_complete_problem_bank(kb)
    
    print(f"✅ Generados {len(problems)} problemas")
    
    # 4. Simular interacciones (opcional)
    simulator = InteractionSimulator(students, problems, kb)
    submissions = simulator.simulate_semester(n_weeks=12)
    
    print(f"✅ Simuladas {len(submissions)} interacciones estudiante-problema")
    
    # 5. Exportar
    exporter = SyntheticDataExporter()
    exporter.to_json(students, "data/synthetic_students.json")
    exporter.to_json([p.dict() for p in problems], "data/synthetic_problems.json")
    exporter.to_csv(students, "data/synthetic_students.csv")
    
    print("✅ Datos exportados a data/")
```

---

## 13. Plan de Implementación MVP

### 13.1 Roadmap Detallado (6 semanas)

#### **SEMANA 1 (10-16 Feb): Fundamentos**

**Objetivos:**
- Infraestructura FIWARE desplegada
- Estructura curricular digitalizada
- Grafo de habilidades construido

**Tareas:**

| Tarea | Responsable | Entregable |
|-------|-------------|------------|
| Deploy Orion-LD + Keycloak (Docker Compose) | DevOps | `docker-compose.yml` funcionando |
| Digitalizar estructura MOD_TBM (Excel → JSON) | Pedagógico | `curriculum_mod_tbm.json` |
| Mapear 60 CE → ~300 habilidades atómicas | Pedagógico + Experto | `skills_mapping.json` |
| Definir prerrequisitos (grafo) | Pedagógico | `skill_prerequisites.json` |
| Implementar `KnowledgeSpaceBuilder` | Backend | Clase Python funcional |
| Test: cargar grafo y validar | QA | Informe de validación |

**Riesgos:**
- ⚠️ Mapeo CE → Habilidades es manual y lento
  - **Mitigación:** Empezar con 20 CE (100 habilidades) para el prototipo

---

#### **SEMANA 2 (17-23 Feb): Generación de Problemas**

**Objetivos:**
- Sistema de generación con LLMs funcional
- Primer banco de 100 problemas generados

**Tareas:**

| Tarea | Responsable | Entregable |
|-------|-------------|------------|
| Implementar `LLMProblemGenerator` | Backend | Clase Python funcional |
| Definir templates de prompts | Pedagógico + IA | `prompt_templates.py` |
| Generar 100 problemas (easy/medium/hard) | Automatizado | `problems_batch_1.json` |
| Validación manual de 20 problemas | Pedagógico | Informe de calidad |
| Ajustar prompts según feedback | Backend | Prompts v2 |
| Almacenar problemas en BD | Backend | PostgreSQL poblada |

**KPI:** ≥80% de problemas generados pasan validación manual

---

#### **SEMANA 3 (24 Feb - 2 Mar): Evaluación y Rúbricas**

**Objetivos:**
- Motor de evaluación con rúbricas funcional
- Actualización de estados de conocimiento

**Tareas:**

| Tarea | Responsable | Entregable |
|-------|-------------|------------|
| Implementar `RubricEvaluator` | Backend | Clase Python funcional |
| Integrar evaluación por keywords | Backend | Método funcionando |
| Integrar evaluación con Claude | Backend | Método funcionando |
| Implementar actualización de `KnowledgeState` | Backend | Lógica de actualización |
| Tests unitarios de evaluación | QA | Suite de tests |
| Generar 100 estudiantes sintéticos | Backend | `synthetic_students.json` |

---

#### **SEMANA 4 (3-9 Mar): Recomendación Adaptativa**

**Objetivos:**
- Motor de recomendación funcionando
- API REST completa

**Tareas:**

| Tarea | Responsable | Entregable |
|-------|-------------|------------|
| Implementar `AdaptiveRecommendationEngine` | Backend | Clase Python funcional |
| Algoritmo de cálculo de franja exterior | Backend | Método funcionando |
| Heurísticas de recomendación | Backend | Lógica implementada |
| Desarrollar API REST (FastAPI) | Backend | Endpoints funcionando |
| Documentación OpenAPI | Backend | Swagger UI disponible |
| Integración con Keycloak (auth) | Backend | JWT validation |
| Tests de integración API | QA | Postman collection |

---

#### **SEMANA 5 (10-16 Mar): Frontend y Visualización**

**Objetivos:**
- Dashboard funcional para estudiantes
- Visualización del grafo de conocimiento

**Tareas:**

| Tarea | Responsable | Entregable |
|-------|-------------|------------|
| Setup proyecto React/Next.js | Frontend | Estructura base |
| Componente: Mapa de conocimiento (grafo) | Frontend | Vista interactiva (Cytoscape.js) |
| Componente: Recomendador de problemas | Frontend | Vista con explicaciones |
| Componente: Visor de problemas | Frontend | Markdown renderer |
| Componente: Envío de soluciones | Frontend | Formulario + adjuntos |
| Componente: Dashboard de progreso | Frontend | Gráficos (Recharts) |
| Integración con API backend | Frontend | Todas las vistas conectadas |
| Responsive design | Frontend | Mobile-friendly |

---

#### **SEMANA 6 (17-23 Mar): Integración y Demo**

**Objetivos:**
- Sistema completo integrado end-to-end
- Video demo grabado
- Documentación técnica completa

**Tareas:**

| Tarea | Responsable | Entregable |
|-------|-------------|------------|
| Integración NGSI-LD completa | Backend | Entities en Orion-LD |
| Poblar sistema con datos sintéticos | Backend | 100 estudiantes + 100 problemas |
| Simular interacciones (submissions) | Backend | 500+ submissions |
| Testing E2E (Cypress/Playwright) | QA | Suite de tests |
| Preparar datos de demo | Todo el equipo | 3 perfiles tipo |
| Grabar video screencast (5 min) | Comunicación | MP4 subido |
| Redactar documentación técnica | Líder técnico | PDF de 15 páginas |
| Presentación slides | Todo el equipo | PowerPoint/PDF |
| Deploy en servidor demo | DevOps | URL pública accesible |

**Entregable final 31 marzo:**
1. ✅ Prototipo funcional (URL: https://demo-pkst.vocational-dataspace.es)
2. ✅ Video demo (5 min)
3. ✅ Documentación técnica (PDF)
4. ✅ Código fuente (GitHub privado)
5. ✅ Presentación ejecutiva (slides)

---

### 13.2 Equipo Necesario

**Roles y dedicación:**

| Rol | Dedicación | Responsabilidades |
|-----|------------|-------------------|
| **Líder Técnico** | 100% (6 semanas) | Arquitectura, coordinación, documentación |
| **Backend Developer** | 100% | Python, FastAPI, PKST engine, LLM integration |
| **Frontend Developer** | 100% | React, visualización de grafos, UX |
| **Pedagogo/Experto FP** | 50% | Mapeo CE→Habilidades, validación de problemas |
| **DevOps** | 25% | Deploy FIWARE, Docker, CI/CD |
| **QA Tester** | 50% | Tests unitarios, integración, E2E |
| **Diseñador UX/UI** (opcional) | 25% | Diseño de interfaces, iconografía |

**Total esfuerzo:** ~4.5 FTE × 6 semanas = **27 personas-semana**

---

### 13.3 Infraestructura Requerida

**Servidor de desarrollo/demo:**
- **CPU:** 8 cores
- **RAM:** 32 GB
- **Almacenamiento:** 500 GB SSD
- **Servicios:**
  - Orion-LD Context Broker
  - PostgreSQL 16
  - Keycloak 23
  - FastAPI (backend)
  - Next.js (frontend)
  - Nginx (reverse proxy)

**Costes estimados AWS/Azure:**
- **Compute (EC2/VM):** ~€200/mes
- **Storage (S3/Blob):** ~€20/mes
- **APIs externas (Claude):** ~€15 (one-time para generación)
- **Total 2 meses:** ~€450

**Alternativa gratuita (para demo):**
- **Railway.app / Render.com:** Free tier para MVP
- **FIWARE Lab:** Infraestructura gratuita para proyectos educativos

---

## 14. Métricas de Éxito y Evaluación

### 14.1 KPIs Técnicos (MVP)

| Métrica | Objetivo | Método de Medición |
|---------|----------|-------------------|
| **Cobertura del espacio de conocimiento** | ≥90% de habilidades cubiertas por problemas | `len(skills_with_problems) / len(all_skills)` |
| **Calidad de problemas generados** | ≥80% validados como "buenos" por expertos | Evaluación manual de muestra |
| **Precisión de evaluación automática** | ≥75% concordancia con evaluación humana | Comparar scores automáticos vs. manuales |
| **Tiempo de respuesta API** | <500ms p95 para recomendación | Métricas APM (New Relic, Datadog) |
| **Disponibilidad del sistema** | ≥99% uptime | Monitoring (Prometheus) |

### 14.2 KPIs Pedagógicos (Fase Piloto Real)

| Métrica | Objetivo | Método de Medición |
|---------|----------|-------------------|
| **Satisfacción del estudiante** | ≥4.0/5.0 en encuesta | Cuestionario post-uso |
| **Percepción de personalización** | ≥70% estudiantes sienten que el sistema adapta a su nivel | Pregunta específica en encuesta |
| **Tiempo medio por problema** | Convergencia a tiempo estimado (±20%) | `avg(actual_time / estimated_time)` |
| **Tasa de finalización de problemas** | ≥70% de problemas iniciados se completan | `completed / started` |
| **Progreso medible** | ≥20% aumento en competence_score en 1 mes | Análisis longitudinal |

### 14.3 Validación del Modelo PKST

**Experimento de validación:**

1. **Grupo control:** Evaluación tradicional (exámenes tipo test)
2. **Grupo experimental:** Evaluación con PKST

**Hipótesis a contrastar:**
- H1: PKST identifica lagunas de conocimiento con mayor precisión
- H2: Recomendaciones PKST mejoran el ritmo de aprendizaje vs. secuencia fija
- H3: Estudiantes perciben PKST como más justo y personalizado

**Metodología:**
- Pre-test para ambos grupos (baseline)
- Intervención de 4 semanas
- Post-test + encuesta de satisfacción
- Análisis estadístico (t-test, ANOVA)

---

## 15. Roadmap Futuro

### 15.1 Capa 2: PKST Probabilístico (Año 1-2)

**Objetivos:**
- Implementar modelos BLIM (Basic Local Independence Model)
- Incorporar parámetros de error (β) y adivinanza (η)
- Inferencia bayesiana de estados latentes

**Nuevas funcionalidades:**
- Estimación de probabilidades de dominio de habilidades (no binario)
- Detección de respuestas inconsistentes (posible copia)
- Testing adaptativo avanzado (selección óptima de ítems)

**Investigación colaborativa:**
- Contactar con Luca Stefanutti (Universidad de Padua)
- Paper conjunto en *Behavior Research Methods* o *Journal of Educational Psychology*

---

### 15.2 Capa 3: MSPM y Captura de Trazas (Año 2-3)

**Objetivos:**
- Modelos de Procesos de Solución de Markov completos
- Captura de trazas procedimentales en tiempo real
- Análisis de estrategias cognitivas

**Infraestructura necesaria:**
- Plugins para IDEs (VS Code, PyCharm) que capturan cada acción
- Integración con plataformas de simulación (Unity, Unreal para VR)
- Análisis de logs de repositorios Git

**Casos de uso avanzados:**
- Distinguir entre error por desconocimiento vs. error por descuido
- Identificar estrategias de planificación (pre-planning vs. interim-planning)
- Personalizar no solo "qué" enseñar, sino "cómo" enseñar

---

### 15.3 Escalado a Otras Familias Profesionales

**Roadmap de expansión:**

1. **Año 1:** Comercio y Marketing (piloto completado)
2. **Año 2:** Informática y Comunicaciones (DAM, DAW, ASIR)
3. **Año 3:** Administración y Gestión (AF, ADOF)
4. **Año 4:** Familias adicionales (Sanitaria, Hostelería, Mecatrónica...)

**Reto:** Cada familia tiene sus propias especificidades procedimentales
- Informática: Código ejecutable, tests automáticos
- Sanitaria: Simuladores médicos, evaluación de procedimientos clínicos
- Mecatrónica: Sensores IoT, simuladores de PLC

**Solución:** Arquitectura modular con "adaptadores" por dominio

---

### 15.4 Federación Real con Otros Centros

**Fase 1 (Año 1):** Federación con los otros 2 centros de excelencia en IA/Big Data
- Compartir espacios de conocimiento
- Banco común de problemas
- Benchmarking de rendimiento entre centros

**Fase 2 (Año 2-3):** Apertura a más centros de FP
- Protocolo de adhesión al dataspace
- Certificación de calidad de problemas aportados
- Modelo de gobernanza distribuida

**Fase 3 (Año 3+):** Conexión con dataspaces europeos
- Interoperabilidad con DS4Skills, Prometheus-X
- Contribuir al estándar europeo de evaluación procedimental en FP

---

## 16. Referencias y Bibliografía

### 16.1 Teoría PKST

1. **Stefanutti, L. (2019).** "On the assessment of procedural knowledge: From problem spaces to knowledge spaces." *British Journal of Mathematical and Statistical Psychology*, 72(2), 185-218. https://doi.org/10.1111/bmsp.12139

2. **Stefanutti, L., de Chiusole, D., & Brancaccio, A. (2021).** "Markov solution processes: Modeling human problem solving with procedural knowledge space theory." *Journal of Mathematical Psychology*, 103, 102552. https://doi.org/10.1016/j.jmp.2021.102552

3. **Stefanutti, L., et al. (2021).** "Two Markov solution process models for the assessment of planning in problem solving." *Psychometrika*, 90, 442-472.

### 16.2 KST y CbKST Fundamentos

4. **Doignon, J.-P., & Falmagne, J.-C. (1999).** *Knowledge Spaces.* Springer-Verlag.

5. **Albert, D., & Hockemeyer, C. (2002).** "Competence-based knowledge structures for personalized learning." In *Adaptive Hypermedia and Adaptive Web-Based Systems*, pp. 225-234.

6. **Heller, J., Wickelmaier, F., & Stefanutti, L. (2015).** "Applying knowledge space theory to competence assessment in education." *Behavior Research Methods*, 47(1), 77-91.

### 16.3 Espacios de Datos Europeos

7. **DS4Skills Blueprint (2023).** "How to build a human-centric data space." DIGITALEUROPE. https://www.skillsdataspace.eu/blueprint

8. **Prometheus-X Association.** "Data Space Education & Skills (DASES)." https://prometheus-x.org

9. **FIWARE Foundation (2021).** "FIWARE for Data Spaces - Position Paper." https://www.fiware.org/wp-content/uploads/FF_PositionPaper_FIWARE4DataSpaces.pdf

### 16.4 Formación Profesional España

10. **Ministerio de Educación, Formación Profesional y Deportes (2024).** "Estrategia de Inteligencia Artificial y Big Data 2024." https://www.educacionfpydeportes.gob.es

11. **Real Decreto 659/2023** de creación del Ciclo Formativo de Grado Medio en Actividades Comerciales.

### 16.5 Herramientas y Software

12. **Hockemeyer, C. (2023).** "kstMatrix: Basic Functions in Knowledge Space Theory Using Matrix Representation." CRAN. https://cran.r-project.org/package=kstMatrix

13. **FIWARE Documentation.** "NGSI-LD API Specification." https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.08.01_60/gs_CIM009v010801p.pdf

---

## Anexos

### Anexo A: Glosario de Términos

- **CE (Criterio de Evaluación):** Indicador observable del logro de un Resultado de Aprendizaje
- **Franja Exterior (Outer Fringe):** Conjunto de habilidades que un estudiante está listo para aprender
- **Grafo DAG:** Grafo Dirigido Acíclico, sin ciclos
- **Habilidad Atómica:** Unidad mínima de competencia evaluable
- **NGSI-LD:** Next Generation Service Interface - Linked Data (estándar ETSI)
- **PKST:** Procedural Knowledge Space Theory (Teoría de Espacios de Conocimiento Procedimental)
- **RA (Resultado de Aprendizaje):** Competencia general que el estudiante debe alcanzar

### Anexo B: Comandos Útiles

```bash
# Deploy completo con Docker Compose
docker-compose up -d

# Generar datos sintéticos
python scripts/generate_synthetic_data.py --n-students 100 --n-problems 200

# Ejecutar tests
pytest tests/ -v --cov=pkst_engine

# Poblar Orion-LD
python scripts/populate_orion_ld.py

# Acceder a logs
docker-compose logs -f pkst-api
```

### Anexo C: Contactos Clave

**Investigación académica:**
- **Luca Stefanutti:** luca.stefanutti@unipd.it (Universidad de Padua)
- **Dietrich Albert:** dietrich.albert@uni-graz.at (Universidad de Graz)

**FIWARE Community:**
- **FIWARE Space Badajoz:** info@fiware.space

**Dataspaces Europeos:**
- **DS4Skills:** info@skillsdataspace.eu
- **Prometheus-X:** contact@prometheus-x.org

---

**FIN DEL DOCUMENTO**

---

**Historial de revisiones:**
- v1.0 (2026-02-08): Versión inicial completa
