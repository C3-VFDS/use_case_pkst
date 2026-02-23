### 5.1 Componentes principales

**Decisión de diseño clave:**

El **Backend EAC** opera como un **servicio centralizado** en el Nodo Central Coordinador del VFDS. Los centros FP actúan como **consumidores del servicio**, configurando un plugin LMS, un Aggregator/Anonymizer local y su propio **FIWARE Dataspace Connector** (CONNECTOR_CFP), que actúa como frontera de soberanía del dato de cada centro. El **FIWARE Dataspace Connector Central** (CONNECTOR_CENTRAL) actúa como API Gateway del Nodo Central, siendo el único punto de entrada al Backend EAC para todos los conectores de los centros.

---

#### **5.1.1 Knowledge Space Builder**

**Responsabilidad:** Construir y mantener el Grafo de Precedencia del Ecosistema Laboral: Situaciones de Competencia, sus Nodos de Requisito y los Umbrales de Maestría.

**Funcionalidades:**
- Parsear estructura curricular (Ciclos → Módulos → RA → CE → Situaciones de Competencia)
- Construir DAG de habilidades desde CE
- Calcular propiedades del grafo (componentes, ciclos, topological sort)
- Detectar inconsistencias (ciclos, habilidades huérfanas)
- Incorporar trazas y escenarios procedentes del Synthetic Data Generator para inicializar o enriquecer el grafo

**Tecnologías:**
- **NetworkX** (Python): Manipulación de grafos
- **Neo4j** (opcional): Persistencia y queries complejas de inferencia

#### **5.1.2 Problem Generator (LLM-based)**

**Responsabilidad:** Generar automáticamente Situaciones de Competencia contextualizadas a partir del estado del estudiante y de los parámetros pedagógicos solicitados por el docente.

**Funcionalidades:**
- Recibir la SC seleccionada por el Recommendation Engine o los parámetros del docente
- Generar enunciados de SC con Claude / GPT-4
- Crear rúbricas de evaluación multi-criterio (Score · Gradiente de Autonomía)
- Validar coherencia SC-habilidades
- Versionar SC (dificultad, variantes)

**Prompt Engineering:** Ver sección 7

### **5.1.3 Recommendation Engine**

**Responsabilidad:** Seleccionar la siguiente Situación de Competencia óptima para cada estudiante, operacionalizando la Zona de Despliegue Proximal mediante el cálculo de la Outer Fringe del Grafo de Precedencia.

**Algoritmo básico (MVP):**
```python
def recommend_next_problem(student_knowledge_state):
    outer_fringe = calculate_outer_fringe(student_knowledge_state)

    # Filtrar SCs que cubran habilidades de la franja
    candidate_scs = filter_scs_by_skills(all_scs, outer_fringe)

    # Heurística de selección por Gradiente de Autonomía
    if student_performance_last_3 > 0.85:   # Resuelve con alta autonomía
        return select_max_difficulty(candidate_scs)

    elif student_performance_last_3 < 0.50:  # Tiene dificultades
        weak_skills = identify_weak_prerequisites(student_knowledge_state)
        return select_sc_reinforcing(weak_skills)

    else:                                     # Rendimiento medio
        return random.choice(candidate_scs)
```

### **5.1.4 Rubric Evaluator**

**Responsabilidad:** Evaluar automáticamente las evidencias de desempeño de los estudiantes mediante rúbricas multi-criterio, actualizando el Perfil de Habilitación y el Gradiente de Autonomía.

**Funcionalidades:**
- Recibir evidencia seudonimizada del estudiante (texto, imagen, código, JSON estructurado)
- Aplicar rúbrica multi-criterio
- Calcular Score y Gradiente de Autonomía
- Realizar Diagnóstico de Causa Raíz ante fallos (carencia en Conocimiento vs. Habilidad)
- Actualizar el Perfil de Habilitación en el Skill Graph Manager
- Publicar `SkillMasteryAggregate` en Orion-LD

**Tipos de evaluación:**
- **Automática completa:** SC con respuesta estructurada (JSON, código)
- **Asistida por LLM:** Evaluación de respuestas abiertas (ensayos, diseños)
- **Manual:** Profesor revisa, sistema aprende

#### **5.1.5 FIWARE Dataspace Connectors**

El sistema opera con dos tipos de conector, con responsabilidades diferenciadas:

**CONNECTOR_CENTRAL (Nodo Central):** Punto de entrada único al Backend EAC para todos los centros participantes. Valida la API Key de cada centro, verifica políticas ODRL mediante Authzforce PDP, aplica rate limiting (500 req/h por defecto), registra cada transacción en el audit log y garantiza la conformidad con el Gaia-X Trust Framework.

**CONNECTOR_CFP (cada Centro FP):** Frontera de soberanía del dato de cada centro. Recibe las evidencias ya seudonimizadas del Aggregator y las transmite al Nodo Central bajo un acuerdo ODRL de transferencia negociado con CONNECTOR_CENTRAL. En sentido inverso, recibe los resultados de evaluación (score · feedback · recomendación) y los entrega a APP_LTI. Garantiza que ningún dato identificable abandona el perímetro del centro.

#### **5.1.6 Aggregator / Anonymizer (local en cada Centro FP)**

**Responsabilidad:** Garantizar la privacidad RGPD actuando como frontera estricta de PII antes de que cualquier dato salga del centro hacia CONNECTOR_CFP.

**Funciones:**
- Eliminar PII (nombre, email, ID real) del estudiante
- Generar hash irreversible: `std_12345 → anon_abc123xyz`
- Remover metadata sensible (IP, ubicación exacta)
- Recibir la API Key configurada por el docente a través de APP_LTI y aplicarla en cada petición saliente

**Principio:** CONNECTOR_CFP, CONNECTOR_CENTRAL y el Backend EAC **nunca reciben ni procesan datos personales identificables**. La reidentificación del estudiante para presentarle sus resultados ocurre únicamente en APP_LTI, dentro del perímetro del centro.
