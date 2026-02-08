## 1. Resumen Ejecutivo

### 1.1 Visión General

El presente documento especifica un **sistema de evaluación y recomendación adaptativa** basado en la Teoría de Espacios de Conocimiento Procedimental (PKST) para el Vocational Federated Dataspace. El sistema revoluciona la evaluación en Formación Profesional al:

- **Mapear competencias procedimentales** en un espacio de conocimiento formal
- **Evaluar procesos, no solo resultados** mediante rúbricas automatizadas
- **Recomendar problemas personalizados** según la zona de desarrollo próximo del estudiante
- **Federar conocimiento** entre centros de FP mediante estándares NGSI-LD

### 1.2 Diferenciación e Innovación

**Valor único frente a otros dataspaces educativos:**

| Aspecto | DS4Skills / Prometheus-X / Merlot | **Nuestro Sistema (PKST-FP)** |
|---------|-----------------------------------|-------------------------------|
| Foco | Competencias declarativas (skills inventory) | **Competencias procedimentales (cómo se resuelven problemas)** |
| Evaluación | Resultado binario (pass/fail) | **Secuencia de pasos + rúbricas multi-criterio** |
| Recomendación | Basada en matching genérico | **Basada en franja de aprendizaje (outer fringe) con fundamento matemático** |
| Dominio | Genérico educación superior | **Específico Formación Profesional española** |
| Generación de contenido | Manual | **Automática con LLMs validados** |

### 1.3 Objetivos del MVP (Marzo 2026)

1. ✅ Implementar PKST Capa 1 (determinista) para 1 módulo completo
2. ✅ Generar automáticamente 100+ problemas con Claude/GPT-4
3. ✅ Demostrar recomendación adaptativa con 100 estudiantes sintéticos
4. ✅ Integrar con FIWARE Orion-LD y Keycloak
5. ✅ Proporcionar dashboard visual de mapa de conocimiento
