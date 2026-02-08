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
