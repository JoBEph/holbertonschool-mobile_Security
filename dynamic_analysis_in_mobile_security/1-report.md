Rapport de Rétro-Ingénierie : Extraction du Flag (Task 1_d)

## 1. Objectif

L'objectif de cette mission était d'extraire un message secret (flag) dissimulé dans une bibliothèque native (libnative-lib.so) d'une application Android. La contrainte imposée était de réussir sans utiliser d'outils d'instrumentation dynamique (type Frida).

## 2. Méthodologie

L'analyse a été conduite en trois phases :

### Analyse Statique
Utilisation de jadx pour identifier la méthode native getSecretMessage() dans MainActivity.java. L'analyse du code source Java a confirmé l'existence d'une logique de déchiffrement native.

### Désassemblage et Identification
Utilisation de objdump et d'un éditeur hexadécimal pour localiser la section .rodata du fichier .so. L'algorithme de déchiffrement a été isolé par lecture du code binaire : une soustraction cyclique basée sur une séquence de Fibonacci.

### Extraction et Automatisation
- Isolation des données brutes à l'offset 0x56f via la commande dd.
- Développement d'un script Python (solve.py) implémentant l'algorithme : flag[i] = (data[i] - fib[(i + shift) % 10]) % 256.
- Brute-force sur le décalage (shift) de la séquence pour aligner correctement la logique de déchiffrement.

## 3. Résultats

Après avoir identifié le décalage (Shift 9) correspondant à la séquence de Fibonacci, le flag a été extrait avec succès.

**Algorithme:** char[i] = obf[i] - fib[(i + 9) % 10]

**Flag identifié:** `Holberton{native_hooking_is_no_different_at_all}`

## 4. Conclusion

Cette analyse démontre que l'obfuscation statique (même avec des algorithmes mathématiques comme Fibonacci) est inefficace contre une analyse rigoureuse du binaire. L'absence d'outils dynamiques a été compensée par une approche algorithmique, confirmant que le binaire contient par lui-même toutes les clés nécessaires à son propre déchiffrement.