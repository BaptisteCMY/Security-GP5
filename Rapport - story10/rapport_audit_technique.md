---

## 1️⃣ Téléchargement et vérification de l'APK

> ** DOD :** Vérifier le hash avec `sha256sum app.apk` et documenter le résultat.

### Manipulation (windows)

```bash
Get-FileHash .\app.apk -Algorithm SHA256`
```

### Résultat

Empreinte confirmée :  
`f01f08c8b6e2fe4612e81bc7e3a3ba9440dad0d7a962f4e67640390dd721d528`

![[Pasted image 20250926143655.png]]

---

## 2️⃣ Analyse automatisée avec MobSF

> ** DOD :** Générer `mobSF_report.pdf` et résumer les vulnérabilités.

### Manipulation

- Lancer l’analyse avec MobSF (interface Web ou CLI).
- Exporter le rapport PDF.

### Résultat

Fichier PDF Téléchargé : [[analyseMobSF.pdf]]

**Score global de sécurité :** **49/100 (risque moyen)**

Principaux constats :

- Trafic HTTP clair autorisé → risque MITM
- Signature v1 vulnérable (faille _Janus_)
- Algorithmes faibles (SHA-1, MD5) + générateur aléatoire non sûr
- Chiffrement CBC vulnérable _padding oracle_
- Services/receivers exportés
- Stockage externe non protégé
- Requêtes SQL brutes + logs sensibles

---

## 3️⃣ Extraction des fichiers

> ** DOD :** Livrer les fichiers extraits avec hash SHA-256.

### Manipulation

- Ouvrir le dossier `app.apk`
- Sélectionner et isoler les fichiers `AndroidManifest.xml` et `classes.dex`
- Zipper les deux fichiers ensembles `Ajouter à`

![[Pasted image 20250926112612.png]]

### Résultat

- Dossier `Desktop.zip` livré.
- Avec le hash SHA256 suivant : `8C96959C2B331F58ABC5F48C2B22D5242747544F4351B5BDA5EF32E348305B58`

![[Pasted image 20250926103449.png]]

---

## 4️⃣ Décompilation avec APKTOOL

> ** DOD :** Livrer un dossier zippé `app_src/`.

### Manipulation

- Suivre la doc d'installation de [APKTOOL](https://apktool.org/docs/install)

```bash
apktool d app.apk -o app_src/
```

### Résultat

```bash
I: Using Apktool 2.12.1 on app.apk with 8 threads
I: Baksmaling classes.dex...
I: Loading resource table...
I: Decoding file-resources...
I: Loading resource table from file: C:\Users\Metreur\AppData\Local\apktool\framework\1.apk
I: Decoding values */* XMLs...
I: Decoding AndroidManifest.xml with resources...
I: Copying original files...
I: Copying assets...
I: Copying lib...
I: Copying unknown files...
Appuyez sur une touche pour continuer...
```

Dossier `app_src` :

![[Pasted image 20250926105425.png]]

---

## 5️⃣ Analyse du fichier `strings.xml`

> **DOD :** Créer `strings_findings.md` avec extraits et risques.

### Manipulation

- Accéder au fichier `strings.xml` depuis le chemin d'accès suivant : `app/res/values/`

```bash
Get-ChildItem

    Répertoire : C:\Users\Metreur\Downloads\app\res\values

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        23/09/2025     10:43          58721 attrs.xml
-a----        23/09/2025     10:43            502 bools.xml
-a----        23/09/2025     10:43           8855 colors.xml
-a----        23/09/2025     10:43          23327 dimens.xml
-a----        23/09/2025     10:43            233 drawables.xml
-a----        23/09/2025     10:43          12044 ids.xml
-a----        23/09/2025     10:43           1605 integers.xml
-a----        23/09/2025     10:43            253 plurals.xml
-a----        23/09/2025     10:43         518410 public.xml
-a----        23/09/2025     10:43          13505 strings.xml
-a----        23/09/2025     10:43         246383 styles.xml

```

- Ouvrir `strings.xml` dans un éditeur de code pour une meilleure lisibilité

![[Pasted image 20250926110553.png]]

- Rechercher et documenter les failles / vulnérabilités présentes.

### Résultat

Fichier `strings_findings.md` livré, incluant les extraits sensibles et une analyse des risques.

- Endpoints sensibles :

```xml
https://api.nickel.eu/customer-banking-api
https://api.nickel.eu/customer-authentication-api
https://api.nickel.eu/personal-space-api
```

> Risques : donne les adresses précises des services bancaires et d’authentification.

---

- Identifiants Firebase/Google

```xml
google_api_key : AIzaSyBTgztvImsUfMWDa41PCrDWAj7dmyIDhUg
google_app_id : 1:717748501407:android:e0cdeb712e27275d
default_web_client_id : 717748501407v621tmfvqd7etdouc3df5vuv31scle0g.apps.googleusercontent.com
firebase_database_url : https://application-client-nickel.firebaseio.com
google_storage_bucket : application-client-nickel.appspot.com
gcm_defaultSenderId : 717748501407
```

> Risques : expose le projet Firebase/Google ; peut faciliter un abus d’API ou un accès non autorisé si les règles ne sont pas strictes.

---

- Identifiants de push/notifications

```xml
GCM_PROJECT_NUMBER : 246766419677
PUSHWOOSH_APPID : DDF11-D1BF9
```

> Risques : informations sur l’infrastructure de notifications, exploitables pour du spam ou des attaques push si mal protégées.

---

- Identifications de build

```xml
com.crashlytics.android.build_id : 9ad6d741-4e10-4abd-9f12-163a0e3fb0b2
```

> Risques : facilite le fingerprinting de la version exacte de l’application.

---

## 6️⃣ Analyse de code

> ** DOD :** Ecrire un fichier `code_findings.md` avec explications.

### Manipulation

- Installation de `jadX` via `docker` grâce à la commande suivante

```bash
docker pull opensecurity/mobile-security-framework-mobsf:latest\ndocker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest
```

- Lancer l'APK via `jadX` :

```bash
jadx-gui app.apk
```

- Analyser les erreurs

### Résultat

![[Pasted image 20250926135642.png]]

```xml
<application android:usesCleartextTraffic="true">
```

> Risque : L’application autorise explicitement les connexions HTTP non chiffrées. Cela expose les données en transit à des attaques de type Man-in-the-Middle (MITM).

```bash
<uses-permission android:name="android.permission.READ_CONTACTS"/>
```

> Risque : READ_CONTACTS permet l’accès à tout le carnet d’adresses.

```bash
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

> Risque : WRITE_EXTERNAL_STORAGE permet d’écrire librement sur le stockage partagé.

```bash
<receiver
  android:name="com.learnium.RNDeviceInfo.RNDeviceReceiver"
  android:enabled="true"
  android:exported="true">
  <intent-filter>
      <action android:name="com.android.vending.INSTALL_REFERRER"/>
  </intent-filter>
</receiver>
```

> Risque : Ces BroadcastReceiver sont exposés (android:exported="true") et peuvent être invoqués par d’autres applications

Fichier `code_findings.md` détaillant chaque extrait problématique.

---

## 7️⃣ Recompilation de l'APK

> ** DOD :** Fichier reconstruit + log de build.

### Manipulation

```bash
apktool b app_src/ -o rebuilt.apk
```

### Résultat

- Log de compilation archivé.

```bash
I: Using Apktool 2.12.1 on app.apk with 8 threads
I: Checking whether sources have changed...
I: Smaling smali folder into classes.dex...
I: Checking whether resources have changed...
I: Building resources with aapt2...
I: Building apk file...
I: Importing assets...
I: Importing lib...
I: Importing unknown files...
I: Built apk into: rebuilt.apk
Appuyez sur une touche pour continuer...
```

- Fichier `rebuilt.apk` généré.

![[Pasted image 20250926112858.png]]

---

## 8️⃣ Signature de l'APK

> ** DOD :** `rebuilt_signed.apk` + preuve `apksigner verify`.

---

### 1 - Créer une clé de signature privée

#### Manipulation

```bash
keytool -genkeypair -v -keystore my-release-key.keystore -alias mykey \
-keyalg RSA -keysize 2048 -validity 10000 -dname "CN=secu"
```

Le fichier (`.keystore/.jks`) contient ta clé.
`alias` = nom logique de la clé à l’intérieur du keystore.
`CN=secu` = “Common Name” affiché lors de la vérification.

#### Résultat

- Résultat : `my-release-key.keystore` (PKCS12), alias `mykey`, cert `RSA 2048`, valable `≈27 ans`.

---

### 2 - Aligner l'APK

> Pourquoi : optimiser l’APK et préparer la signature (on aligne avant de signer).

A quoi sert l'alignement :

- Moins de RAM et moins de CPU (pas besoin de réaligner/copier en mémoire).
- Chargements plus rapides (démarrage/app screens plus réactifs).
- C’était une exigence historique du Play Store pour les APK; et c’est encore la bonne pratique pour tout APK distribué hors-store.

#### Manipulation

```bash
& "$BT\zipalign.exe" -p -v 4 .\app.apk .\app-aligned.apk
```

#### Résultat

![[Pasted image 20250926113818.png]]

---

### 3 - Signer l'APK

> Pourquoi : attacher la signature (v1/v2/v3) pour que Android accepte l’installation.

#### Manipulation

```bash
& "$BT\apksigner.bat" sign `
  --ks .\app\my-release-key.keystore `
  --ks-key-alias mykey `
  --out .\rebuilt_signed.apk `
  .\app-aligned.apk
```

#### Vérification

> Pourquoi : prouver que la signature est valide et par quel certificat.

```bash
& "$BT\apksigner.bat" verify --verbose --print-certs .\rebuilt_signed.apk |
Tee-Object -FilePath .\apksigner_proof.txt
```

#### Résultat

```bash
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

| Version | Mots-clés                                                                             | Notes                                                                      |
| ------- | ------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **v1**  | JAR signing, META-INF, compatibilité totale, fichiers signés individuellement, ancien | Fonctionne sur **tous les Android**, moins sûr contre modification globale |
| **v2**  | APK entier, rapide, sécurisé, Android 7+, bloc complet                                | Protège tout l’APK, vérification plus rapide                               |
| **v3**  | Key rotation, mises à jour incrémentales, Android 9+, bloc complet                    | Permet changer la clé pour updates, modernisation de v2                    |

- Fichiers générés.

![[Pasted image 20250926114332.png]]

---

## 9️⃣ Vérification Firebase

> ** DOD :** `firebase_check.md` + capture de la réponse.

---

### 1 - Décompilation de l'APK

#### Manipulation

- Décompilation de l'APK :  `apktool d app.apk -o app_decompiled`
- Recherche de configuration firebase : `grep -R "firebaseio.com" app_decompiled/`

#### Résultat

URL détectée → `https://application-client-nickel.firebaseio.com/`

---

### 2 - Test d'accès (Realtime Database)

#### Manipulation

```bash
curl -v -X GET "https://application-client-nickel.firebaseio.com/.json"
```

#### Résultat

```bash
     % Total   % Received  % Xferd  Average Speed  Time  Time  
     Time  Current  Dload    Upload   Total Spent Left  Speed
     100       36          100      36             0     0    
     68    0        --:--:-- --:--:-- --:--:--          69
     {
        "error" : "Permission denied"
     }
```

---

## Politique de sécurité mobile

> ** DOD :** Document `politique_mobile` livré.

Contient : règles de chiffrement, gestion des identifiants, mise à jour de sécurité.

[[politique_mobile]]

## 11 - Recommandations

- Forcer l’usage de **TLS** (désactiver `usesCleartextTraffic`).
- Migrer vers **signature v2/v3**, abandonner SHA-1/MD5.
- Remplacer le chiffrement **CBC** ou implémenter un padding sûr.
- Restreindre ou protéger les **services/receivers exportés**.
- Chiffrer/supprimer tout stockage externe de données sensibles.
- Utiliser des **requêtes préparées** et purger les logs sensibles.
- Mettre les clés (Firebase/Pushwoosh) dans un **secret vault**.
- Mettre en place un **processus de patch rapide** pour les failles critiques.

---

## 12 - Choix d’une solution MDM

> ** DOD :** `mdm_recommandation` + tableau comparatif.

[[mdm_recomandation]]

---

## Conclusion

L’application présente plusieurs vulnérabilités de configuration et de développement qui, combinées, exposent les utilisateurs à des risques de fuite d’informations et d’attaques réseau.  
**Un plan d’action correctif doit être engagé rapidement** pour renforcer la posture de sécurité globale.
