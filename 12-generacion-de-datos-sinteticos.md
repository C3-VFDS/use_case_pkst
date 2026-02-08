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
