Rapport — Tâche 3 : Déchiffrement Multi-Étapes (XOR + Rotation + Modulo)

## 1. Objectif

Extraire un flag caché dans une application Android (app-release-task4.apk) qui utilise un chiffrement multi-étapes: XOR, rotation de bits, soustraction modulo, et multiplication modulo.

## 2. Étape 1 — Analyse statique du bytecode Java

### Décompilation de l'APK

```bash
jadx app-release-task4.apk -d task4_jadx
```

### Fichiers pertinents identifiés

- `com/holberton/task4_d/MainActivityKt.java` — contient la logique de déchiffrement
- Fonction clé: `aBcDeFgHiJkLmNoPqRsTuVwXyZ123456()` — déchiffre et affiche le flag

## 3. Étape 2 — Analyse de l'algorithme

### Fonction de déchiffrement: aBcDeFgHiJkLmNoPqRsTuVwXyZ123456()

```java
private static final void aBcDeFgHiJkLmNoPqRsTuVwXyZ123456(Function1<? super String, Unit> function1) {
    try {
        byte[] decodedBytes = Base64.decode("8CP4zSyn62t78lwwc383rxcgtv/UiMv3Pw+Mfw12LzXvorIpBypNK/oB7XvWNV0oWfoX", 0);
        
        // Conversion en entiers non-signés
        List decodedList = convertToUnsignedInts(decodedBytes);
        
        // Transformation pour chaque byte
        for (int index = 0; index < decodedList.size(); index++) {
            int value = decodedList.get(index);
            int temp = value ^ 19;                                    // XOR avec 19
            int temp2 = ((((temp >> 2) | (temp << 6)) & 255) - (index * 3)) % 256;  // Rotation + soustraction
            int result = ((temp2 < 0 ? temp2 + 256 : temp2) * 183) % 256;  // Multiplication modulo 256
            flagChars.add((char) result);
        }
    } catch (Exception e) {
        // Gestion d'erreur
    }
}
```

### Détail des transformations

Pour chaque byte `value` à l'index `i`:

1. **XOR**: `temp = value ^ 19`
2. **Rotation droite de 2 bits**: `temp = ((temp >> 2) | (temp << 6)) & 255`
3. **Soustraction avec index**: `temp2 = (temp - (i * 3)) % 256`
4. **Normalisation** (si négatif): `temp2 = temp2 < 0 ? temp2 + 256 : temp2`
5. **Multiplication modulo 256**: `result = (temp2 * 183) % 256`
6. **Conversion en caractère**: `char = result`

## 4. Étape 3 — Déchiffrement en Python

```python
import base64

# 1. Décoder la chaîne Base64
encoded_flag = "8CP4zSyn62t78lwwc383rxcgtv/UiMv3Pw+Mfw12LzXvorIpBypNK/oB7XvWNV0oWfoX"
decoded_bytes = base64.b64decode(encoded_flag)

# 2. Convertir en entiers non-signés
decoded_list = [byte_val & 0xFF for byte_val in decoded_bytes]

# 3. Appliquer les transformations
flag = ""
for index, value in enumerate(decoded_list):
    # Étape 1: XOR avec 19
    temp = value ^ 19
    
    # Étape 2: Rotation droite de 2 bits
    temp = (((temp >> 2) | (temp << 6)) & 0xFF)
    
    # Étape 3: Soustraction avec (index * 3)
    temp2 = (temp - (index * 3)) % 256
    
    # Étape 4: Normalisation des négatifs
    if temp2 < 0:
        temp2 += 256
    
    # Étape 5: Multiplication par 183 modulo 256
    result = (temp2 * 183) % 256
    
    # Étape 6: Conversion en caractère
    flag += chr(result)

print(f"Flag: {flag}")
```

## 5. Résultats

**Flag extrait avec succès:**
```
Holberton{calling_uncalled_functions_is_now_known!}
```

**Ciphertext (Base64):**
```
8CP4zSyn62t78lwwc383rxcgtv/UiMv3Pw+Mfw12LzXvorIpBypNK/oB7XvWNV0oWfoX
```

**Algorithme de déchiffrement:**
- XOR ^ 19
- Rotation droite 2 bits
- Soustraction (index × 3)
- Multiplication × 183 (modulo 256)

## 6. Observations de sécurité

1. **Chiffrement multi-étapes complexe**: L'algorithme combine plusieurs opérations (XOR, rotation, arithmétique modulo) pour augmenter la complexité apparente.

2. **Dépendance à l'index**: La soustraction `(index * 3)` rend chaque byte dépendant de sa position, ce qui rend la cryptanalyse légèrement plus complexe.

3. **Clé statique**: Toutes les constantes (19, 2, 3, 183, 256) sont codées en dur dans le code, rendant la clé entièrement dérivable par analyse statique.

4. **Pas de véritable sécurité cryptographique**: Bien que plus complexe que XOR seul, cette approche n'utilise pas de primitives cryptographiques éprouvées et reste vulnérable à l'analyse statique.

## 7. Résumé des outils utilisés

| Outil | Usage |
|-------|-------|
| jadx | Décompilation du bytecode Java |
| python3 | Réimplémentation de l'algorithme et déchiffrement |

## 8. Conclusion

Cette tâche démontre que même avec des transformations multi-étapes apparemment complexes, l'analyse statique du code décompilé suffit à extraire complètement l'algorithme de déchiffrement. La complexité ajoutée par les opérations bitwise et les calculs modulo ne fournit qu'une "sécurité par obscurité" facilement contournable.
