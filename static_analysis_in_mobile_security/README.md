# Static Analysis in Mobile Security

## Task 0: Flag Extraction from Static Analysis

### Objectif
L'objectif de cette mission était d'extraire un message secret (flag) à partir d'une analyse statique d'une application Android sans exécution dynamique.

### Méthodologie
L'analyse a été conduite en utilisant des outils d'analyse statique:

- **Décompilation**: Utilisation de jadx pour extraire le code source Java et identifier les éléments clés
- **Inspection des ressources**: Analyse des ressources de l'application
- **Identification des patterns**: Recherche des indicateurs de flags ou données sensibles

### Résultats
Le flag a été extrait avec succès par analyse statique du code source et des ressources de l'application.

**Flag identifié:**
```
Holberton{Good_job_on_your_first_static_analysis_exercise}
```

### Observations et Conclusion
L'analyse statique permet d'identifier rapidement les secrets et données sensibles dissimulées dans une application Android sans nécessiter une exécution dynamique. Cette approche est efficace pour:
- Détecter les hardcoded secrets
- Identifier les logiques sensibles dans le code source
- Analyser les ressources de l'application

Cette démonstration confirme que les données sensibles visibles en analyse statique constituent une vulnérabilité majeure en sécurité mobile.