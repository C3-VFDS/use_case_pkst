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
        VCISSUER_CFP["🏫 VC Issuer (Keycloak)
Emite StudentCredential
y TeacherCredential"]
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
            subgraph CONNECTOR_CFP["🔗 FIWARE Dataspace Connector · Centro FP"]
                subgraph AUTH_CFP["🔵 Authentication Service"]
                    AUTHPORTAL_CFP["Auth Portal"]
                    VERIFIER_CFP["Verifier"]
                    CCS_CFP["Credentials Config
Service"]
                    LTIL_CFP["Local Trusted
Issuers List"]
                end
                subgraph POLICY_CFP["🟢 Policy Management · Authorization Service"]
                    PEP_CFP["APISIX · PEP
(API Gateway)"]
                    PDP_CFP["PDP"]
                    PIP_CFP["PIP"]
                    PAP_CFP["PRP / PAP
(authorization)"]
                end
                AUTHPORTAL_CFP -->|"4) Qué credenciales
acepta"| CCS_CFP
                AUTHPORTAL_CFP <-->|"OIDC4VP
(pasos 5 · 6 · 10)"| VERIFIER_CFP
                VERIFIER_CFP -->|"9) Verifica
emisor local"| LTIL_CFP
                PEP_CFP -->|"13)"| PDP_CFP
                PDP_CFP -->|"14a)"| PIP_CFP
                PDP_CFP -->|"14b)"| PAP_CFP
                PDP_CFP -->|"15) Decisión"| PEP_CFP
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
            VCISSUER_C["🏫 VC Issuer (Keycloak)
Emite OperatorCredential
y ResearcherCredential"]

            subgraph CONNECTOR_CENTRAL["🔗 FIWARE Dataspace Connector · Nodo Central"]
                subgraph AUTH_C["🔵 Authentication Service"]
                    AUTHPORTAL_C["Auth Portal"]
                    VERIFIER_C["Verifier"]
                    CCS_C["Credentials Config
Service"]
                    LTIL_C["Local Trusted
Issuers List"]
                end
                subgraph POLICY_C["🟢 Policy Management · Authorization Service"]
                    PEP_C["APISIX · PEP
(API Gateway)"]
                    PDP_C["PDP"]
                    PIP_C["PIP"]
                    PAP_C["PRP / PAP
(authorization)"]
                end
                AUTHPORTAL_C -->|"4) Qué credenciales
acepta"| CCS_C
                AUTHPORTAL_C <-->|"OIDC4VP
(pasos 5 · 6 · 10)"| VERIFIER_C
                VERIFIER_C -->|"9) Verifica
emisor local"| LTIL_C
                PEP_C -->|"13)"| PDP_C
                PDP_C -->|"14a)"| PIP_C
                PDP_C -->|"14b)"| PAP_C
                PDP_C -->|"15) Decisión"| PEP_C
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

    %% ─────────────────────────────────────────────
    %% SERVICIOS GLOBALES DEL DATASPACE
    %% ─────────────────────────────────────────────
        subgraph GLOBAL["🌍 Servicios Globales del Dataspace"]
            DSPR["🌐 Data Space
        Participants Registry"]
            GTIL["📋 Global Trusted
        Issuers List"]
            REVOCATION["🚫 Revocation List"]
        end
    end

    %% ─────────────────────────────────────────────
    %% FLUJOS PRINCIPALES
    %% ─────────────────────────────────────────────

    %% 0) Emisión de credenciales
    STUDENT & TEACHER -->|"0) Matrícula"| VCISSUER_CFP
    VCISSUER_CFP -->|"0) Entrega VC firmada
(Ed25519)"| WALLET_T
    OPERATOR & RESEARCHER -->|"0) Registro"| VCISSUER_C
    VCISSUER_C -->|"0) Entrega VC firmada
(Ed25519)"| WALLET_O & WALLET_R

    %% Actores → LMS
    STUDENT -->|"Solicita / Resuelve SC"| LMS
    TEACHER -->|"Crea ejercicio"| LMS

    %% Docente → Marketplace
    TEACHER -->|"Descubre y solicita
Backend EAC"| MARKETPLACE

    %% 1-2) Docente inicia acceso via Auth Portal CFP
    WALLET_T -->|"1) Petición acceso"| AUTHPORTAL_CFP
    AUTHPORTAL_CFP -->|"2) Solicita
presentación VC"| WALLET_T
    WALLET_T -->|"3) 5) 6)
OIDC4VP flows"| VERIFIER_CFP
    AUTHPORTAL_CFP -->|"11) Token JWT"| APP_LTI

    %% 1-2) Operador e Investigador via Auth Portal Central
    WALLET_O & WALLET_R -->|"1) Petición acceso"| AUTHPORTAL_C
    AUTHPORTAL_C -->|"2) Solicita
presentación VC"| WALLET_O & WALLET_R
    WALLET_O & WALLET_R -->|"3) 5) 6)
OIDC4VP flows"| VERIFIER_C

    %% 8) Verifiers → Servicios globales
    VERIFIER_CFP -->|"8) Verifica
participante · emisor · revocación"| DSPR & GTIL & REVOCATION
    VERIFIER_C -->|"8) Verifica
participante · emisor · revocación"| DSPR & GTIL & REVOCATION

    %% Marketplace → Connectors (dos acciones)
    MARKETPLACE -->|"① Aprovisiona
política ODRL"| PAP_CFP
    MARKETPLACE -->|"② Registra
organización"| LTIL_CFP
    MARKETPLACE -->|"① Aprovisiona
política ODRL"| PAP_C
    MARKETPLACE -->|"② Registra
organización"| LTIL_C

    %% 16) PEP → Aplicaciones
    PEP_CFP -->|"16) Request validado"| APP_LTI
    PEP_C -->|"16) Request validado"| EAC

    %% Centro FP ↔ Nodo Central
    APP_LTI -->|"Configura API Key"| ANON
    ANON -->|"12) POST /api/v2/evaluate
Bearer Token
datos seudonimizados"| PEP_CFP
    PEP_CFP <-->|"Intercambio de datos
anonimizados"| PEP_C
    PEP_CFP -->|"Resultado evaluación
score · feedback · recomendación"| APP_LTI

    %% APP_LTI ↔ LMS
    APP_LTI -->|"Registra calificación
(Gradiente de Autonomía)"| LMS
    LMS -->|"Panel competencial"| STUDENT

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
    EAC & PEP_C & ORIONLD -->|"Métricas / Logs"| MONITOR
    WALLET_O -->|"Gestiona y supervisa"| AUTHPORTAL_C
    PEP_C -->|"Acceso autorizado"| MONITOR

    %% Investigador
    WALLET_R -->|"Acceso a datos
agregados"| AUTHPORTAL_C
    PEP_C -->|"NGSI-LD
(datos agregados)"| ORIONLD

    %% Gaia-X (futura integración)
    GAIAX -.->|"futura integración"| MARKETPLACE

    %% ─────────────────────────────────────────────
    %% ESTILOS
    %% ─────────────────────────────────────────────
    style Z1 fill:#e8f5e9,stroke:#4caf50,color:#1b5e20
    style Z2 fill:#fff3e0,stroke:#ff9800,color:#e65100
    style Z3 fill:#e3f2fd,stroke:#1976d2,color:#0d47a1
    style GLOBAL fill:#f3e5f5,stroke:#7b1fa2,color:#4a148c
    style GOVERNANCE fill:#fce4ec,stroke:#e91e63
    style GOVERNANCE_CFP fill:#fce4ec,stroke:#e91e63
    style EAC fill:#e8eaf6,stroke:#3f51b5
    style DATA fill:#e0f2f1,stroke:#00897b
    style OBS fill:#fff8e1,stroke:#ffc107
    style CONNECTOR_CFP fill:#ede7f6,stroke:#7b1fa2
    style CONNECTOR_CENTRAL fill:#ede7f6,stroke:#7b1fa2
    style AUTH_CFP fill:#e3f2fd,stroke:#1976d2
    style AUTH_C fill:#e3f2fd,stroke:#1976d2
    style POLICY_CFP fill:#e8f5e9,stroke:#388e3c
    style POLICY_C fill:#e8f5e9,stroke:#388e3c
    style GAIAX fill:#f5f5f5,stroke:#bdbdbd,color:#9e9e9e
```

> **Nota:** El diagrama puede exportarse a SVG con `mmdc -i arquitectura.mmd -o arquitectura.svg` (Mermaid CLI) o con la extensión Mermaid Preview de VS Code. También puede visualizarse directamente en plataformas que soporten Mermaid (GitHub, GitLab, Notion, mermaid.live, etc.) o incrustarse en HTML.

1. [Componentes Principales](051-componentes-principales.md)
2. [Diagrama de Funcionalidad del Backend EAC](052-diagrama-funcionalidad-eac.md)
3. [Stack Tecnológico](053-stack-tecnologico.md)
4. [Diagramas de Secuencias](054-diagramas-secuencias.md)
5. [Credenciales Verificables y Control de Acceso](055-control-acceso.md)