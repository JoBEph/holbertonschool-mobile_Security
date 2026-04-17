Rapport — Tâche 2 : Cryptographie Android — Analyse et Déchiffrement

## 1. Objectif

Extraire un flag caché dans une application Android (app-release-task2.apk) qui utilise un chiffrement XOR basé sur un calcul Fibonacci. Aucun serveur distant n'est impliqué — le chiffrement est entièrement local.

## 2. Étape 1 — Analyse statique du bytecode Java

### Décompilation de l'APK

```bash
jadx app-release-task2.apk -d task2_jadx
```

### Fichiers pertinents identifiés

- `com/holberton/task3/MainActivityKt.java` — contient toute la logique de déchiffrement
- `com/holberton/task3/MainActivity.java` — simple lanceur d'interface

## 3. Étape 2 — Analyse de l'algorithme

Trois fonctions clés dans `MainActivityKt.java`:

### performslowDecryption()

```java
public static final String performslowDecryption() {
    byte[] decoded = Base64.getDecoder().decode(
        "cVZaW1dDQllZTFdRW1xeUlBbX21CWFtHalRZXUJFRFhNX1ZcbllGQ15cUUNSRFpcVks="
    );
    return xorDecrypt(new String(decoded, UTF_8), String.valueOf(slowRecursive(150)));
}
```

### slowRecursive(int n) — Fibonacci récursif naïf

```java
public static final long slowRecursive(int i) {
    return i <= 1 ? i : slowRecursive(i - 1) + slowRecursive(i - 2);
}
```

Calcule Fibonacci(150) de façon volontairement lente avec une complexité O(2^n), d'où le nom de la fonction.

### xorDecrypt(String encryptedFlag, String key)

```java
public static final String xorDecrypt(String encryptedFlag, String key) {
    StringBuilder result = new StringBuilder();
    for (int i = 0; i < encryptedFlag.length(); i++) {
        result.append((char) (key.charAt(i % key.length()) ^ encryptedFlag.charAt(i)));
    }
    return result.toString();
}
```

Déchiffre le flag en appliquant une opération XOR caractère par caractère.

## 4. Étape 3 — Déchiffrement

Au lieu d'exécuter l'application lente (récursion naïve), la logique a été réimplémentée en Python:

```python
import base64

# 1. Calcul de la clé Fibonacci (150)
def get_fib(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
    return a

key = str(get_fib(150))

# 2. Décodage Base64
encoded_flag = "cVZaW1dDQllZTFdRW1xeUlBbX21CWFtHalRZXUJFRFhNX1ZcbllGQ15cUUNSRFpcVks="
encrypted_bytes = base64.b64decode(encoded_flag).decode('utf-8')

# 3. XOR Decrypt
flag = ""
for i in range(len(encrypted_bytes)):
    # XOR entre le char du texte et le char de la clé (index modulo longueur clé)
    char_code = ord(encrypted_bytes[i]) ^ ord(key[i % len(key)])
    flag += chr(char_code)

print(f"Flag : {flag}")
```

## 5. Résultats

**Flag extrait avec succès:**
```
Holberton{fibonacci_slow_computation_optimization}
```

**Valeur de Fibonacci(150):**
```
9969216677189303386214405760200
```

**Ciphertext (Base64):**
```
cVZaW1dDQllZTFdRW1xeUlBbX21CWFtHalRZXUJFRFhNX1ZcbllGQ15cUUNSRFpcVks=
```

## 6. Observations de sécurité

- **Clé dérivable du code source**: La clé de chiffrement est entièrement dérivable à partir du code source — aucun secret externe n'est utilisé.

- **Exécution intentionnellement lente**: La fonction `slowRecursive` est une implémentation Fibonacci récursive naïve en O(2^n), utilisée intentionnellement pour ralentir l'exécution et compliquer l'analyse dynamique.

- **Chiffrement faible**: Le flag est chiffré en XOR avec une clé dérivée du résultat de `fib(150)` converti en string — facilement cassable par analyse statique sans même exécuter l'application.

- **XOR n'est pas sécurisé**: Le chiffrement XOR ne fournit aucune sécurité cryptographique réelle, particulièrement quand la clé est publiquement dérivable.

## 7. Résumé des outils utilisés

| Outil | Usage |
|-------|-------|
| jadx | Décompilation du bytecode Java |
| python3 | Réimplémentation de l'algorithme et déchiffrement |

## 8. Conclusion

Cette analyse démontre que l'obfuscation basée sur des calculs mathématiques complexes (Fibonacci récursif) ne constitue pas une véritable sécurité. L'absence d'outils dynamiques et l'analyse statique pure suffisent à extraire le flag. La réimplémentation Python optimisée du calcul Fibonacci contourne efficacement la tentative de ralentissement intentionnel de l'application.