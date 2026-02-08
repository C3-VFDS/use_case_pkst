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
