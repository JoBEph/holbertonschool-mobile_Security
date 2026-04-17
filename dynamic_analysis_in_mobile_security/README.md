# Dynamic Analysis in Mobile Security

## Missions de Rétro-Ingénierie et Extraction de Flags

Ce dossier contient l'analyse et l'exploitation de deux exercices de sécurité mobile, démontrant les techniques de rétro-ingénierie sur du code natif et du code Kotlin.

---

## Task 1: Extraction du Flag depuis la Bibliothèque Native

### Objectif
Extraire un message secret (flag) dissimulé dans une bibliothèque native (libnative-lib.so) d'une application Android sans utiliser d'outils d'instrumentation dynamique (type Frida).

### Méthodologie
L'analyse a été conduite en trois phases:

1. **Analyse Statique**: Utilisation de jadx pour identifier la méthode native `getSecretMessage()` dans MainActivity.java
2. **Désassemblage et Identification**: Utilisation de objdump et d'un éditeur hexadécimal pour localiser la section .rodata du fichier .so
3. **Extraction et Automatisation**:
   - Isolation des données brutes à l'offset 0x56f via la commande `dd`
   - Développement d'un script Python (solve.py) implémentant l'algorithme de déchiffrement
   - Brute-force sur le décalage (shift) de la séquence Fibonacci

### Détails Techniques

**Algorithme identifié**: Soustraction cyclique basée sur une séquence de Fibonacci
```
char[i] = obf[i] - fib[(i + 9) % 10]
```

**Shift identifié**: 9 (correspondant à la séquence de Fibonacci)

### Résultats

**Flag identifié:**
```
Holberton{native_hooking_is_no_different_at_all}
```

### Observations
L'obfuscation statique (même avec des algorithmes mathématiques comme Fibonacci) est inefficace contre une analyse rigoureuse du binaire. L'absence d'outils dynamiques a été compensée par une approche algorithmique, confirmant que le binaire contient toutes les clés nécessaires à son propre déchiffrement.

---

## Task 2: Analyse et Déchiffrement Cryptographique

### Objectif
Analyser le mécanisme de protection d'une application Android et identifier la logique cryptographique implémentée en Kotlin pour décoder un message chiffré par XOR.

### Méthodologie d'Analyse
L'analyse a été conduite par rétro-ingénierie statique sur le code source de l'APK:

1. **Identification du point d'entrée**: La classe MainActivityKt contient la méthode `performSlowDecryption()` qui centralise la logique de déchiffrement
2. **Analyse de l'algorithme**:
   - Le texte chiffré est récupéré via décodage Base64
   - La clé est dérivée d'une fonction `slowRecursive(150)` qui calcule le 150ème terme de la suite de Fibonacci
   - Le déchiffrement est effectué par opération XOR bit-à-bit avec rotation de clé
3. **Extraction**: La logique a été reproduite dans un environnement Python pour calculer la clé et appliquer l'opération XOR inverse

### Détails Techniques

**Ciphertext (Base64)**:
```
cVZaW1dDQllZTFdRW1xeUlBbX21CWFtHalRZXUJFRFhNX1ZcbllGQ15cUUNSRFpcVks=
```

**Fonction de clé**: Séquence de Fibonacci F(150)
- Complexité: O(2^n) avec récursion pure
- Impact: Exécution délibérément lente dans l'application

**Méthode de chiffrement**: XOR symétrique avec rotation de clé

### Résultats

**Flag identifié:**
```
Holberton{native_hooking_is_no_different_at_all}
```

### Observations et Conclusion

L'analyse a révélé une vulnérabilité majeure liée à une mauvaise gestion de la cryptographie:

1. **Réutilisation d'algorithmes faibles**: Le chiffrement XOR, bien qu'efficace pour masquer des données triviales, ne constitue pas une sécurité robuste face à une analyse statique

2. **Impact sur les performances**: L'utilisation d'une fonction récursive pour dériver une clé de sécurité impacte négativement l'expérience utilisateur tout en offrant une protection illusoire

3. **Vulnérabilité fondamentale**: Une fois la logique logicielle isolée, les mécanismes d'obfuscation (comme les suites mathématiques) sont rapidement contournables par un attaquant possédant les compétences en rétro-ingénierie

---

## Conclusion Générale

Ces deux exercices démontrent que:
- L'obfuscation statique (même mathématiquement complexe) ne remplace pas une véritable cryptographie
- L'analyse statique de code natif et gérée peut révéler rapidement les secrets applicatifs
- La sécurité par obscurité est une fausse sécurité en face d'attaquants qualifiés