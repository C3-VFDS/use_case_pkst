## 5. Arquitectura del Sistema

```mermaid
graph TB
    %% ─────────────────────────────────────────────
    %% ACTORES
    %% ─────────────────────────────────────────────
    subgraph Z1["ZONA 1 · Actores y Roles"]
        STUDENT["👨‍🎓 Estudiante
(StudentCredential)"]
        TEACHER["👨‍🏫 Docente
(TeacherCredential)"]
        WALLET_T["💼 Wallet Docente
W3C VC — eIDAS 2.0"]
        OPERATOR["🔧 Operador
(OperatorCredential)"]
        WALLET_O["💼 Wallet Operador
W3C VC — eIDAS 2.0"]
        RESEARCHER["🔍 Investigador
(ResearcherCredential)"]
        WALLET_R["💼 Wallet Investigador
W3C VC — eIDAS 2.0"]
        TEACHER --- WALLET_T
        OPERATOR --- WALLET_O
        RESEARCHER --- WALLET_R
    end

    %% ─────────────────────────────────────────────
    %% CENTRO FP
    %% ─────────────────────────────────────────────
    subgraph Z2["Centro FP
(did:web:cifpcarlos3.edu.es)"]
        LMS["📚 LMS"]
        APP_LTI["Aplicación LTI / Frontend EAC
Vista Estudiante · Vista Docente"]
        ANON["🔒 Aggregator / Anonymizer
(plugin Moodle local)
Elimina PII antes de enviar"]
        LOCALDB["💾 PostgreSQL Local
Datos sensibles (PII)
Cola de reintentos
NUNCA sale del centro"]
        LMS -->|"📝 Solicita ejercicio"| APP_LTI
        APP_LTI -->|"Submission con PII"| ANON
        ANON -->|"Datos locales PII"| LOCALDB

        subgraph GOVERNANCE_CFP["📋 Gobernanza y Acceso"]
            subgraph CONNECTOR_CFP["🔗 FIWARE Dataspace Connector"]
                VCISSUER_CFP["🏫 VC Issuer
Emite StudentCredential
y TeacherCredential"]
                VCVERIFIER_CFP["🔍 VC Verifier
OIDC4VC / DIDComm"]
                TIR_CFP["📋 Trusted Issuers
Registry"]
                DSPR_CFP["🌐 Data Space
Participants Registry"]
                subgraph ODRL_CFP["⚖️ ODRL Authorization"]
                    APISIX_CFP["APISIX"]
                    OPA_CFP["Open Policy Agent"]
                    PAP_CFP["ODRL PAP"]
                end
            end
        end
    end

    %% ─────────────────────────────────────────────
    %% NODO CENTRAL
    %% ─────────────────────────────────────────────
    subgraph Z3["ZONA 3 · Nodo Central Coordinador  –  VOCATIONAL FEDERATED DATASPACE"]
        GAIAX["⭐ Gaia-X Trust Framework
Compliance Service
(futura integración)"]

        subgraph GOVERNANCE["📋 Gobernanza y Acceso"]
            MARKETPLACE["🛒 Marketplace
CKAN + Federated Catalogue
DCAT-AP + Self-Descriptions"]
            subgraph CONNECTOR_CENTRAL["🔗 FIWARE Dataspace Connector"]
                VCISSUER_C["🏫 VC Issuer
Emite OperatorCredential
y ResearcherCredential"]
                VCVERIFIER_C["🔍 VC Verifier
OIDC4VC / DIDComm"]
                TIR_C["📋 Trusted Issuers
Registry"]
                DSPR_C["🌐 Data Space
Participants Registry"]
                subgraph ODRL_C["⚖️ ODRL Authorization"]
                    APISIX_C["APISIX"]
                    OPA_C["Open Policy Agent"]
                    PAP_C["ODRL PAP"]
                end
            end
        end

        subgraph EAC["⚙️ Backend EAC Central  –  Servicio Centralizado"]
            KSB["📐 Knowledge Space
Builder
(NetworkX / Neo4j)"]
            PGEN["🤖 Problem Generator
(LLM-based)
Claude / GPT-4"]
            REC["🎯 Recommendation
Engine
(Outer Fringe)"]
            RUBRIC["📝 Rubric Evaluator
(Auto-score)"]
            SYNTH["🔬 Synthetic Data
Generator"]
            SGRAPH["🕸️ Skill Graph
Manager"]
        end

        subgraph DATA["🗄️ Capa de Datos Federados"]
            ORIONLD["🌐 Orion-LD Hub
(FIWARE Context Broker)
NGSI-LD v1.6.1
VocationalSkill · LearningProblem
SkillMasteryAggregate"]
            POSTGRES_C["🐘 PostgreSQL
(datos centrales)"]
        end

        subgraph OBS["📊 Observabilidad"]
            MONITOR["Prometheus + Grafana
Alerts · SLA 99.5%"]
        end
    end

    %% ─────────────────────────────────────────────
    %% FLUJOS PRINCIPALES
    %% ─────────────────────────────────────────────

    %% Matrícula / Emisión de credenciales
    STUDENT & TEACHER -->|"Matrícula / Verificación"| VCISSUER_CFP

    %% Wallets → VC Verifiers
    WALLET_T -->|"Presenta VC"| VCVERIFIER_CFP
    WALLET_O & WALLET_R -->|"Presenta VC"| VCVERIFIER_C

    %% VC Verifier → Data Space Participants Registry
    VCVERIFIER_CFP -->|"Verifica participante"| DSPR_CFP
    VCVERIFIER_C -->|"Verifica participante"| DSPR_C

    %% Docente → Marketplace
    WALLET_T -->|"Descubre y solicita
Backend EAC"| MARKETPLACE

    %% Actores → LMS
    STUDENT -->|"Solicita / Resuelve ejercicio"| LMS
    TEACHER -->|"Crea ejercicio"| LMS

    %% Marketplace → Connectors (dos acciones)
    MARKETPLACE -->|"① Aprovisiona ODRL en PAP
② Registra organización"| TIR_C
    MARKETPLACE -->|"Política ODRL"| PAP_C
    MARKETPLACE -->|"① Aprovisiona ODRL en PAP
② Registra organización"| TIR_CFP
    MARKETPLACE -->|"Política ODRL"| PAP_CFP

    %% ODRL Authorization → servicios
    ODRL_CFP -->|"Request validado
(autenticación · políticas · rate limit)"| APP_LTI
    ODRL_C -->|"Request validado
(autenticación · políticas · rate limit)"| EAC

    %% EAC interno
    KSB --> SGRAPH
    SGRAPH --> REC
    PGEN --> REC
    REC --> RUBRIC
    SYNTH --> KSB
    RUBRIC -->|"Actualiza métricas"| ORIONLD

    %% EAC ↔ Datos
    EAC <-->|"Read / Write"| POSTGRES_C
    EAC <-->|"NGSI-LD"| ORIONLD

    %% Observabilidad
    EAC & CONNECTOR_CENTRAL & ORIONLD -->|"Métricas / Logs"| MONITOR
    CONNECTOR_CENTRAL -->|"Operador gestiona y supervisa"| MONITOR

    %% Centro FP ↔ Nodo Central
    APP_LTI -->|"Configura API Key"| ANON
    ANON -->|"POST /api/v2/evaluate
Bearer API Key
datos seudonimizados"| CONNECTOR_CFP
    CONNECTOR_CENTRAL <-->|"Intercambio de datos
anonimizados"| CONNECTOR_CFP
    CONNECTOR_CFP -->|"Resultado evaluación
score · feedback · recomendación"| APP_LTI

    %% Investigador
    CONNECTOR_CENTRAL -->|"Investigador a NGSI-LD
(datos agregados)"| ORIONLD

    %% Gaia-X (futura integración)
    GAIAX -.->|"futura integración"| MARKETPLACE

    %% Estilos de zona
    style Z1 fill:#e8f5e9,stroke:#4caf50,color:#1b5e20
    style Z2 fill:#fff3e0,stroke:#ff9800,color:#e65100
    style Z3 fill:#e3f2fd,stroke:#1976d2,color:#0d47a1
    style GOVERNANCE fill:#fce4ec,stroke:#e91e63
    style GOVERNANCE_CFP fill:#fce4ec,stroke:#e91e63
    style EAC fill:#e8eaf6,stroke:#3f51b5
    style DATA fill:#e0f2f1,stroke:#00897b
    style OBS fill:#fff8e1,stroke:#ffc107
    style CONNECTOR_CFP fill:#f3e5f5,stroke:#7b1fa2
    style CONNECTOR_CENTRAL fill:#f3e5f5,stroke:#7b1fa2
    style ODRL_CFP fill:#ede7f6,stroke:#512da8
    style ODRL_C fill:#ede7f6,stroke:#512da8
    style GAIAX fill:#f5f5f5,stroke:#bdbdbd,color:#9e9e9e
```

> **Nota:** El diagrama puede exportarse a SVG con `mmdc -i arquitectura.mmd -o arquitectura.svg` (Mermaid CLI) o con la extensión Mermaid Preview de VS Code. También puede visualizarse directamente en plataformas que soporten Mermaid (GitHub, GitLab, Notion, mermaid.live, etc.) o incrustarse en HTML.

1. [Componentes Principales](051-componentes-principales.md)
2. [Diagrama de Funcionalidad del Backend EAC](052-diagrama-funcionalidad-eac.md)
3. [Stack Tecnológico](053-stack-tecnologico.md)
4. [Diagramas de Secuencias](054-diagramas-secuencias.md)
5. [Credenciales Verificables y Control de Acceso](055-control-acceso.md)