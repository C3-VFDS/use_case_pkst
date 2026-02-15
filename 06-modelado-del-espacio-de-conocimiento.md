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
                "https://vocational-dataspace.es/contexts/eac-context.jsonld"
            ]
        }
        return entity
```
