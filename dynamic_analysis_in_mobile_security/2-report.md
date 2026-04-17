Rapport de Rétro-Ingénierie : Analyse et Déchiffrement (Task 2)

## 1. Objectif

Le but de cette mission était d'analyser le mécanisme de protection d'une application Android. Contrairement à la tâche précédente (analyse binaire native), l'objectif ici était d'identifier la logique cryptographique implémentée en Kotlin au sein de l'interface utilisateur, afin de décoder un message chiffré par XOR.

## 2. Méthodologie

L'analyse a été conduite par rétro-ingénierie statique sur le code source de l'APK :

### Identification du point d'entrée
La classe MainActivityKt contient une méthode performSlowDecryption qui centralise la logique de déchiffrement.

### Analyse de l'algorithme
- Le texte chiffré est récupéré via un décodage Base64 : `cVZaW1dDQllZTFdRW1xeUlBbX21CWFtHalRZXUJFRFhNX1ZcbllGQ15cUUNSRFpcVks=`
- La clé est dérivée d'une fonction slowRecursive(150) qui calcule le 150ème terme de la suite de Fibonacci.
- Le déchiffrement final est effectué par une opération XOR bit-à-bit entre le texte chiffré et la clé générée (appliquée cycliquement).

### Extraction
Au lieu d'instrumenter l'application (ce qui aurait été coûteux en ressources à cause de la récursion lente), la logique a été reproduite dans un environnement Python isolé pour calculer la clé et appliquer l'opération XOR inverse.

## 3. Résultats

Après l'analyse complète du mécanisme cryptographique, le flag a été extrait avec succès.

**Fonction de clé:** Séquence de Fibonacci F(150)

**Méthode de chiffrement:** XOR symétrique avec rotation de clé

**Flag identifié:** `Holberton{fibonacci_slow_computation_optimization}`

## 4. Conclusion

L'analyse a révélé une vulnérabilité majeure liée à une mauvaise gestion de la cryptographie :

- **Réutilisation d'algorithmes faibles** : Le chiffrement XOR, bien qu'efficace pour masquer des données triviales, ne constitue pas une sécurité robuste face à une analyse statique.

- **Impact sur les performances** : L'utilisation d'une fonction récursive pour dériver une clé de sécurité impacte négativement l'expérience utilisateur tout en offrant une protection illusoire.

- **Vulnérabilité fondamentale** : Une fois la logique logicielle isolée, les mécanismes d'obfuscation (comme les suites mathématiques) sont rapidement contournables par un attaquant possédant les compétences en rétro-ingénierie.