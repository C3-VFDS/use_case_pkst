## Anexos

### Anexo A: Glosario de Términos

- **CE (Criterio de Evaluación):** Indicador observable del logro de un Resultado de Aprendizaje
- **Franja Exterior (Outer Fringe):** Conjunto de habilidades que un estudiante está listo para aprender
- **Grafo DAG:** Grafo Dirigido Acíclico, sin ciclos
- **Habilidad Atómica:** Unidad mínima de competencia evaluable
- **NGSI-LD:** Next Generation Service Interface - Linked Data (estándar ETSI)
- **PKST:** Procedural Knowledge Space Theory (Teoría de Espacios de Conocimiento Procedimental)
- **RA (Resultado de Aprendizaje):** Competencia general que el estudiante debe alcanzar

### Anexo B: Comandos Útiles

```bash
# Deploy completo con Docker Compose
docker-compose up -d

# Generar datos sintéticos
python scripts/generate_synthetic_data.py --n-students 100 --n-problems 200

# Ejecutar tests
pytest tests/ -v --cov=pkst_engine

# Poblar Orion-LD
python scripts/populate_orion_ld.py

# Acceder a logs
docker-compose logs -f pkst-api
```

### Anexo C: Contactos Clave

**Investigación académica:**
- **Luca Stefanutti:** luca.stefanutti@unipd.it (Universidad de Padua)
- **Dietrich Albert:** dietrich.albert@uni-graz.at (Universidad de Graz)

**FIWARE Community:**
- **FIWARE Space Badajoz:** info@fiware.space
- **SEAMWARE:** contact@seamware.com

**Dataspaces Europeos:**
- **DS4Skills:** info@skillsdataspace.eu
- **Prometheus-X:** contact@prometheus-x.org

---

**FIN DEL DOCUMENTO**

---

**Historial de revisiones:**
- v1.0 (2026-02-08): Versión inicial completa
