## 3. Marco Teórico: PKST Simplificado

### 3.1 Fundamentos de PKST (Capa 1)

La Teoría de Espacios de Conocimiento Procedimental modela el conocimiento como una **estructura combinatoria** donde:

**Definición 1: Habilidad Atómica**
> Una habilidad atómica `H` es la unidad mínima de competencia procedimental evaluable de forma independiente.

**Ejemplo en Merchandising:**
- `H1`: Identificar el público objetivo de un producto
- `H2`: Analizar la circulación de clientes en un establecimiento
- `H3`: Aplicar la regla del triángulo de oro en layout
- `H4`: Seleccionar paleta cromática según psicología del color

**Definición 2: Estado de Conocimiento**
> Un estado de conocimiento `K` es un subconjunto de habilidades que un estudiante ha dominado: `K ⊆ H_total`

**Ejemplo:**
```
K_estudiante = {H1, H2, H5, H8}  # Domina 4 de 15 habilidades totales
```

**Definición 3: Espacio de Conocimiento**
> El espacio de conocimiento `𝒦` es el conjunto de todos los estados de conocimiento válidos, construido respetando prerrequisitos.

**Axioma de Clausura bajo Unión:**
> Si `K₁, K₂ ∈ 𝒦`, entonces `K₁ ∪ K₂ ∈ 𝒦`

Esto garantiza que no existen restricciones artificiales al aprendizaje.

### 3.2 Grafo de Prerrequisitos

El espacio de conocimiento se deriva de un **grafo dirigido acíclico (DAG)** donde:
- **Nodos:** Habilidades atómicas
- **Aristas:** Relaciones de prerrequisito (`H_i → H_j` significa "H_i es prerrequisito de H_j")

**Ejemplo visual:**

```
         H1 (Identificar público)
           ↓
         H2 (Analizar circulación)
           ↓
    ┌──────┴──────┐
    ↓             ↓
   H3 (Layout)   H4 (Psicología color)
    ↓             ↓
    └──────┬──────┘
           ↓
         H5 (Diseño escaparate completo)
```

**Regla de inferencia:**
> Si un estudiante domina `H5`, necesariamente domina `{H1, H2}` y al menos uno de `{H3, H4}`

### 3.3 Franja Exterior (Outer Fringe)

**Definición:**
> La franja exterior de un estado `K` es el conjunto de habilidades que el estudiante está **listo para aprender** porque domina todos sus prerrequisitos:

```
OuterFringe(K) = {H ∉ K | ∀H_prereq → H, H_prereq ∈ K}
```

**Ejemplo:**
```
K_actual = {H1, H2, H3}
OuterFringe(K_actual) = {H5}  # Puede aprender H5 porque tiene H1, H2, H3
```

**Implicación pedagógica:**
> El sistema solo recomienda problemas que involucren habilidades de la franja exterior, evitando frustración (reto imposible) o aburrimiento (reto trivial).

### 3.4 Simplificaciones para MVP (Capa 1)

Para el prototipo de marzo, **NO implementamos:**
- ❌ Modelos probabilísticos (BLIM, MSPM)
- ❌ Captura completa de trazas en tiempo real
- ❌ Inferencia bayesiana de estados latentes

**SÍ implementamos:**
- ✅ Grafo de prerrequisitos determinista
- ✅ Cálculo exacto de franja exterior
- ✅ Evaluación binaria por habilidad (dominada/no dominada) mediante rúbricas
- ✅ Recomendación basada en heurística simple sobre la franja
