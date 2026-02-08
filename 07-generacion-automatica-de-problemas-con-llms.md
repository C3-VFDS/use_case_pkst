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
