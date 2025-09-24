## Création de la clé privée de signature

- Pourquoi : générer une clé privée + certificat pour signer l’APK.

```bash
keytool -genkeypair -v -keystore my-release-key.keystore -alias mykey \
  -keyalg RSA -keysize 2048 -validity 10000 -dname "CN=secu"
```

Le fichier (`.keystore/.jks`) contient ta clé.

`alias` = nom logique de la clé à l’intérieur du keystore.

`CN=secu` = “Common Name” affiché lors de la vérification.

- Résultat : `my-release-key.keystore` (PKCS12), alias `mykey`, cert `RSA 2048`, valable `≈27 ans`.

## Aligner l'APK

Pourquoi : optimiser l’APK et préparer la signature (on aligne avant de signer).

A quoi sert l'alignement :

- Moins de RAM et moins de CPU (pas besoin de réaligner/copier en mémoire).
- Chargements plus rapides (démarrage/app screens plus réactifs).
- C’était une exigence historique du Play Store pour les APK; et c’est encore la bonne pratique pour tout APK distribué hors-store.

```
& "$BT\zipalign.exe" -p -v 4 .\app.apk .\app-aligned.apk
```

## Signer l'APK

Pourquoi : attacher la signature (v1/v2/v3) pour que Android accepte l’installation.

```
& "$BT\apksigner.bat" sign `
  --ks .\app\my-release-key.keystore `
  --ks-key-alias mykey `
  --out .\rebuilt_signed.apk `
  .\app-aligned.apk

```

## Vérification

Pourquoi : prouver que la signature est valide et par quel certificat.

```
& "$BT\apksigner.bat" verify --verbose --print-certs .\rebuilt_signed.apk |
  Tee-Object -FilePath .\apksigner_proof.txt
```

Résultat :

```
Verifies
Verified using v1 scheme (JAR signing): true
Verified using v2 scheme (APK Signature Scheme v2): true
Verified using v3 scheme (APK Signature Scheme v3): true
Verified using v3.1 scheme (APK Signature Scheme v3.1): false
Verified using v4 scheme (APK Signature Scheme v4): false
Verified for SourceStamp: false
Number of signers: 1
Signer #1 certificate DN: CN=secu
Signer #1 certificate SHA-256 digest: f7a633c5b3c3201f679804124a4385cf3ee086c351e8fa9ae0412b444e39f4af
Signer #1 certificate SHA-1 digest: 96d997a6e4c7c32dafee7cdc5e579b4997265048
Signer #1 certificate MD5 digest: e22c5d042989234b6b1d6ea9c07f4076
Signer #1 key algorithm: RSA
Signer #1 key size (bits): 2048
Signer #1 public key SHA-256 digest: 6a3cc55048e66e12b56f6054fb484471c455cd4e0e3eca451d5f9d1612939a70
Signer #1 public key SHA-1 digest: 7d8c0c427a26f53c49b919dcde32cd4e0adc4a69
Signer #1 public key MD5 digest: 10a911cb8e91902a4de44c0897a391aa
```
