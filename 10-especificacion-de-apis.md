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
