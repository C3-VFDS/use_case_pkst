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
