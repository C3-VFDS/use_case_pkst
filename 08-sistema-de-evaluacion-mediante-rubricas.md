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
{% raw %}
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
{% endraw %}
```
