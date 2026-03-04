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

#### **5.1.3 Recommendation Engine**

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

#### **5.1.4 Rubric Evaluator**

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

#### 5.1.5 FIWARE Dataspace Connectors

El sistema opera con dos conectores de idéntica arquitectura interna pero con responsabilidades diferenciadas. Cada conector integra dos capas funcionales:

**Authentication Service**, compuesto por:
- `Auth Portal`: orquesta el flujo OIDC4VP con la Wallet del usuario, mediando entre la aplicación cliente y el Verifier
- `Verifier`: verifica la validez de la VC presentada, consultando la `Local Trusted Issuers List` local y los servicios globales del dataspace (`Data Space Participants Registry`, `Global Trusted Issuers List` y `Revocation List`)
- `Credentials Config Service`: informa al Auth Portal de qué tipos de credenciales son aceptadas para cada recurso
- `Local Trusted Issuers List`: registro local de los emisores de credenciales en los que confía este conector; el Marketplace escribe en él al aprovisionar el acceso de una organización

**Policy Management / Authorization Service**, compuesto por:
- `APISIX · PEP` (Policy Enforcement Point): API Gateway que intercepta toda petición entrante y delega la decisión de autorización en el PDP
- `PDP` (Policy Decision Point): evalúa la petición contra las políticas ODRL vigentes, consultando al PIP para datos de contexto y al PRP/PAP para las políticas aplicables
- `PIP` (Policy Information Point): proporciona al PDP la información de contexto necesaria para evaluar la política
- `PRP / PAP` (Policy Repository / Policy Administration Point): almacena y gestiona las políticas ODRL; el Marketplace escribe en él al registrar o actualizar un producto

**CONNECTOR_CENTRAL (Nodo Central):** Punto de entrada único al Backend EAC para todos los centros participantes. Su Authentication Service verifica las credenciales de Operadores e Investigadores; su Authorization Service aplica las políticas ODRL asociadas a cada producto del catálogo y registra cada transacción en el audit log.

**CONNECTOR_CFP (cada Centro FP):** Frontera de soberanía del dato de cada centro. Su Authentication Service verifica las credenciales de Docentes. Su Authorization Service controla el acceso a la Aplicación LTI y gestiona el tráfico de evidencias anonimizadas hacia el Nodo Central y de resultados en sentido inverso. Garantiza que ningún dato identificable abandona el perímetro del centro.

Ambos conectores incorporan además su propio **VC Issuer (Keycloak)**, desplegado junto al conector como componente independiente: el del Centro FP emite `StudentCredential` y `TeacherCredential`; el del Nodo Central emite `OperatorCredential` y `ResearcherCredential`.

---

#### 5.1.6 Aggregator / Pseudonymizer (local en cada Centro FP)

**Funciones:**
- Eliminar PII (nombre, email, ID real) del estudiante
- Generar hash reversible: `std_12345 → anon_abc123xyz`
- Remover metadata sensible (IP, ubicación exacta)
- Gestionar la cola de reintentos ante fallos temporales del servicio

**Principio:** CONNECTOR_CFP, CONNECTOR_CENTRAL y el Backend EAC **nunca reciben ni procesan datos personales identificables**. La reidentificación del estudiante para presentarle sus resultados ocurre únicamente en APP_LTI, dentro del perímetro del centro.

---

#### 5.1.7 Servicios Globales del Dataspace

Alojados en el Nodo Central, son consultados por los `Verifier` de ambos conectores durante el proceso de autenticación:

- **Data Space Participants Registry:** registro de todas las organizaciones participantes en el dataspace. El Verifier lo consulta para confirmar que la organización que presenta credenciales es un participante reconocido y activo.
- **Global Trusted Issuers List:** catálogo global de emisores de credenciales verificables en los que confía el dataspace. Complementa la `Local Trusted Issuers List` de cada conector.
- **Revocation List:** registro de credenciales revocadas. El Verifier la consulta para garantizar que una VC formalmente válida no ha sido invalidada por su emisor con posterioridad.
