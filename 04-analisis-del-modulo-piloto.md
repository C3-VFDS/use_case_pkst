## 4. Análisis del Módulo Piloto

### 4.1 Ficha Técnica del Módulo

**Módulo:** Técnicas Básicas de Merchandising  
**Código:** (Código oficial del MEFP según Real Decreto)  
**Familia Profesional:** Comercio y Marketing  
**Ciclo Formativo:** Técnico en Actividades Comerciales (Grado Medio)  
**Duración:** 100 horas (aproximadamente)  
**Curso:** 1º

### 4.2 Estructura Curricular

#### **Resultados de Aprendizaje (RA)**

**RA1:** Caracteriza la implantación de productos y servicios analizando el espacio comercial y las técnicas de animación del punto de venta.

**RA2:** Organiza la implantación de productos en la superficie de venta definiendo y aplicando criterios comerciales.

**RA3:** Realiza el montaje de escaparates y otros expositores comerciales aplicando criterios de composición y los elementos de animación del punto de venta.

**RA4:** Dispone elementos gráficos de información, señalización y publicidad en el punto de venta, aplicando técnicas de diseño y colocación.

**RA5:** Valora la implantación de productos y los elementos de animación en el punto de venta, analizando la normativa aplicable y el impacto sobre el cliente.

### 4.3 Muestra de Criterios de Evaluación

Dado que el módulo tiene aproximadamente 60 CE, mostramos una muestra representativa:

#### **Criterios de Evaluación del RA1:**

**CE 1.1:** Se han identificado las características físicas y psicológicas del público objetivo.

**CE 1.2:** Se ha analizado la circulación de clientes en el establecimiento comercial.

**CE 1.3:** Se han identificado las zonas y puntos de venta fríos y calientes.

**CE 1.4:** Se han aplicado técnicas de análisis del comportamiento del cliente en el punto de venta.

**CE 1.5:** Se han reconocido las normativas de seguridad y accesibilidad aplicables al punto de venta.

#### **Criterios de Evaluación del RA3:**

**CE 3.1:** Se ha elaborado un proyecto de escaparate según el tipo de producto y establecimiento.

**CE 3.2:** Se han aplicado los principios de composición en el diseño del escaparate.

**CE 3.3:** Se ha seleccionado la paleta cromática coherente con la identidad de marca.

**CE 3.4:** Se han dispuesto los elementos del escaparate aplicando la regla del equilibrio visual.

**CE 3.5:** Se ha evaluado el impacto del escaparate mediante técnicas de observación.

### 4.4 Mapeo CE → Habilidades Atómicas

#### **Proceso de Derivación:**

1. **Análisis semántico del CE:** Identificar verbos de acción y objetos
2. **Descomposición en habilidades atómicas:** Cada verbo + contexto = 1 habilidad
3. **Identificación de prerrequisitos:** Establecer dependencias lógicas
4. **Validación con expertos:** Profesores del módulo validan el grafo

#### **Ejemplo de Mapeo:**

**CE 3.1:** "Se ha elaborado un proyecto de escaparate según el tipo de producto y establecimiento"

**Habilidades derivadas:**
- `H3.1.1`: Clasificar productos según categoría (alimentación, moda, tecnología...)
- `H3.1.2`: Identificar el tipo de establecimiento (flagship, outlet, pop-up...)
- `H3.1.3`: Analizar el público objetivo del establecimiento
- `H3.1.4`: Definir objetivos del escaparate (informativo, persuasivo, recordatorio)
- `H3.1.5`: Esbozar el layout básico del escaparate (boceto)

**Prerrequisitos:**
```
H3.1.1 → H3.1.4  # Necesito clasificar producto antes de definir objetivos
H3.1.2 → H3.1.4  # Necesito conocer tipo de establecimiento
H3.1.3 → H3.1.4  # Necesito conocer público objetivo
{H3.1.1, H3.1.2, H3.1.3, H3.1.4} → H3.1.5  # Todas las anteriores antes del boceto
```

### 4.5 Estimación del Grafo Completo

**Estadísticas del MVP:**
- **Resultados de Aprendizaje:** 5
- **Criterios de Evaluación:** ~60
- **Habilidades Atómicas estimadas:** 60 CE × 5 habilidades/CE = **~300 habilidades**
- **Aristas del grafo (prerrequisitos):** ~450-600 (ratio típico 1.5-2 aristas/nodo)

**Complejidad computacional:**
- **Cálculo de franja exterior:** O(|V| + |E|) = O(300 + 500) ≈ **800 operaciones** (trivial)
- **Espacio de conocimiento completo:** 2^300 estados (imposible enumerar, pero no necesario)
