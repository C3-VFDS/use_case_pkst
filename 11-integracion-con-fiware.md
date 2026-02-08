## 11. Integración con FIWARE

### 11.1 Modelo de Datos NGSI-LD

#### **11.1.1 Contexto JSON-LD**

Definimos un contexto específico para PKST:

**URL:** `https://vocational-dataspace.es/contexts/pkst-context.jsonld`

```json
{
  "@context": {
    "pkst": "https://vocational-dataspace.es/ontology/pkst#",
    "esco": "http://data.europa.eu/esco/",
    "schema": "https://schema.org/",
    
    "Skill": "pkst:Skill",
    "KnowledgeState": "pkst:KnowledgeState",
    "Problem": "pkst:Problem",
    "Submission": "pkst:Submission",
    
    "masteredSkills": {
      "@id": "pkst:masteredSkills",
      "@type": "@id"
    },
    "outerFringe": {
      "@id": "pkst:outerFringe",
      "@type": "@id"
    },
    "requiredSkills": {
      "@id": "pkst:requiredSkills",
      "@type": "@id"
    },
    "competenceScore": {
      "@id": "pkst:competenceScore",
      "@type": "xsd:float"
    }
  }
}
```

#### **11.1.2 Entidad: Skill en NGSI-LD**

```json
{
  "id": "urn:ngsi-ld:Skill:H3.1.1",
  "type": "Skill",
  "name": {
    "type": "Property",
    "value": "Clasificar productos por categoría"
  },
  "description": {
    "type": "Property",
    "value": "Capacidad de identificar y categorizar productos según su naturaleza..."
  },
  "criterionId": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:EvaluationCriterion:CE3.1"
  },
  "prerequisites": {
    "type": "Relationship",
    "object": []  // Sin prerrequisitos (habilidad básica)
  },
  "level": {
    "type": "Property",
    "value": "basic"
  },
  "escoSkillUri": {
    "type": "Property",
    "value": "http://data.europa.eu/esco/skill/..."
  },
  "@context": [
    "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld",
    "https://vocational-dataspace.es/contexts/pkst-context.jsonld"
  ]
}
```

#### **11.1.3 Entidad: KnowledgeState en NGSI-LD**

```json
{
  "id": "urn:ngsi-ld:KnowledgeState:student_12345_MOD_TBM",
  "type": "KnowledgeState",
  "studentId": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Student:student_12345"
  },
  "moduleId": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Module:MOD_TBM"
  },
  "masteredSkills": {
    "type": "Relationship",
    "object": [
      "urn:ngsi-ld:Skill:H3.1.1",
      "urn:ngsi-ld:Skill:H3.1.2",
      "urn:ngsi-ld:Skill:H3.1.3"
    ]
  },
  "outerFringe": {
    "type": "Relationship",
    "object": [
      "urn:ngsi-ld:Skill:H3.1.4",
      "urn:ngsi-ld:Skill:H3.2.1"
    ]
  },
  "competenceScore": {
    "type": "Property",
    "value": 35.2,
    "unitCode": "P1"  // Porcentaje
  },
  "totalProblemsAttempted": {
    "type": "Property",
    "value": 18
  },
  "totalProblemsSolved": {
    "type": "Property",
    "value": 14
  },
  "lastUpdated": {
    "type": "Property",
    "value": {
      "@type": "DateTime",
      "@value": "2026-02-08T10:30:00Z"
    }
  },
  "@context": [
    "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld",
    "https://vocational-dataspace.es/contexts/pkst-context.jsonld"
  ]
}
```

### 11.2 Suscripciones NGSI-LD

Para reaccionar en tiempo real a cambios:

```python
import requests

def create_subscription_on_knowledge_state_updates():
    """Crea suscripción para recibir notificaciones cuando se actualiza un KnowledgeState"""
    
    subscription = {
        "id": "urn:ngsi-ld:Subscription:KnowledgeStateUpdates",
        "type": "Subscription",
        "entities": [
            {
                "type": "KnowledgeState"
            }
        ],
        "watchedAttributes": ["masteredSkills", "competenceScore"],
        "notification": {
            "endpoint": {
                "uri": "https://pkst-engine.vocational-dataspace.es/api/v1/webhooks/orion-ld",
                "accept": "application/json"
            }
        },
        "@context": [
            "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld"
        ]
    }
    
    response = requests.post(
        "http://orion-ld:1026/ngsi-ld/v1/subscriptions",
        json=subscription,
        headers={"Content-Type": "application/ld+json"}
    )
    
    return response.json()
```

### 11.3 Queries Temporales

Aprovechar capacidades temporales de Orion-LD para análisis histórico:

```python
def get_competence_evolution(student_id: str, from_date: str, to_date: str):
    """Obtiene la evolución del competence score en un período"""
    
    entity_id = f"urn:ngsi-ld:KnowledgeState:{student_id}_MOD_TBM"
    
    response = requests.get(
        f"http://orion-ld:1026/ngsi-ld/v1/temporal/entities/{entity_id}",
        params={
            "timerel": "between",
            "timeAt": from_date,
            "endTimeAt": to_date,
            "attrs": "competenceScore"
        },
        headers={"Accept": "application/ld+json"}
    )
    
    # Procesar serie temporal
    data = response.json()
    time_series = [
        {"timestamp": item["observedAt"], "value": item["value"]}
        for item in data["competenceScore"]["values"]
    ]
    
    return time_series
```

### 11.4 Autenticación con Keycloak

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
import jwt

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="https://keycloak.vocational-dataspace.es/auth/realms/vocational-fp/protocol/openid-connect/token")

def verify_token(token: str = Depends(oauth2_scheme)):
    """Verifica el JWT de Keycloak"""
    try:
        # Verificar firma y validez
        payload = jwt.decode(
            token,
            key=KEYCLOAK_PUBLIC_KEY,
            algorithms=["RS256"],
            audience="pkst-api"
        )
        return payload
    
    except jwt.ExpiredSignatureError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Token expired"
        )
    
    except jwt.InvalidTokenError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid token"
        )

# Uso en endpoints
@app.get("/api/v1/students/{student_id}/knowledge-state")
async def get_knowledge_state(student_id: str, current_user = Depends(verify_token)):
    # Verificar que el usuario puede acceder a este estudiante
    if current_user["sub"] != student_id and "teacher" not in current_user["realm_access"]["roles"]:
        raise HTTPException(status_code=403, detail="Forbidden")
    
    # ... lógica del endpoint
```
