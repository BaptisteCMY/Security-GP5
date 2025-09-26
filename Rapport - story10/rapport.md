# Rapport technique – Analyse de l’application mobile

**Empreinte SHA-256 :**  
`f01f08c8b6e2fe4612e81bc7e3a3ba9440dad0d7a962f4e67640390dd721d528`

---

## 1. Résumé global

L’analyse de l’APK met en évidence plusieurs faiblesses de sécurité.  
**Score de sécurité global : 49/100 (risque moyen).**

Principaux constats :

- Trafic réseau autorisé en HTTP clair, exposant les communications à des attaques de type Man-in-the-Middle.
- Signature et cryptographie faibles : signature v1 vulnérable (faille Janus), utilisation d’algorithmes obsolètes (SHA-1, MD5) et d’un générateur aléatoire peu sûr.
- Chiffrement en mode CBC vulnérable aux attaques « padding oracle ».
- Composants Android (services/receivers) exportés et accessibles à d’autres applications.
- Stockage externe non protégé, rendant les données potentiellement lisibles par des applications tierces.
- Requêtes SQL brutes et présence de logs sensibles augmentant les risques d’injection ou de fuite d’informations. çà

---

## 2. Détails des découvertes

### 2.1 Endpoints sensibles

https://api.nickel.eu/customer-banking-api

https://api.nickel.eu/customer-authentication-api

https://api.nickel.eu/personal-space-api

Ces adresses révèlent des points d’accès bancaires et d’authentification qui peuvent faciliter la reconnaissance d’infrastructure.

### 2.2 Identifiants Firebase / Google

Clés et identifiants (API key, app ID, client ID, URL de base de données, bucket de stockage, gcm_defaultSenderId) exposent le projet Firebase/Google.  
**Risque :** abus d’API ou accès non autorisé si les règles de sécurité côté Firebase ne sont pas strictes.

### 2.3 Identifiants de push/notifications

- GCM_PROJECT_NUMBER
- PUSHWOOSH_APPID

Ces informations décrivent l’infrastructure de notifications et pourraient être exploitées pour du spam ou des attaques de type « push » si mal protégées.

### 2.4 Identifiant de build

`com.crashlytics.android.build_id`  
Peut faciliter le fingerprinting précis de la version de l’application.

---

## 3. Vulnérabilités logiques et mauvaises pratiques

### 3.1 AndroidManifest.xml

- `android:usesCleartextTraffic="true"` : autorise les connexions HTTP non chiffrées (risque MITM).
- `android.permission.READ_CONTACTS` : accès complet au carnet d’adresses, sensible pour la vie privée.
- `android.permission.WRITE_EXTERNAL_STORAGE` : permet d’écrire sur un stockage partagé, augmentant le risque de fuite de données.
- `RNDeviceReceiver` exporté (`android:exported="true"`) : peut être invoqué par d’autres applications si aucun contrôle de permission n’est appliqué, créant un risque d’Intent Spoofing.

### 3.2 Requêtes SQL et logs

Utilisation de requêtes SQL brutes et présence de journaux contenant des informations sensibles, pouvant mener à des injections ou fuites.

---

## 4. Preuve d’accès Firebase

Une tentative d’accès public à `https://application-client-nickel.firebaseio.com/.json` renvoie :

{ "error" : "Permission denied" }

→ aucune fuite immédiate, mais vigilance nécessaire sur la configuration des règles d’accès.

---

## 5. Recommandations

- Forcer l’usage du chiffrement TLS et désactiver le trafic HTTP clair.
- Migrer vers la signature v2/v3 et abandonner les algorithmes SHA-1/MD5.
- Revoir le chiffrement CBC pour éviter les attaques « padding oracle ».
- Restreindre l’accès aux services/receivers exportés en ajoutant des permissions spécifiques.
- Supprimer ou chiffrer tout stockage externe de données sensibles.
- Paramétrer les requêtes SQL via des statements préparés et retirer les logs contenant des données sensibles.
- Protéger les clés et identifiants (Firebase/Pushwoosh) dans un coffre-fort de secrets.
- Mettre en place un processus de mise à jour de sécurité rapide pour corriger les vulnérabilités critiques.

---

## Conclusion

L’application présente plusieurs vulnérabilités de configuration et de développement qui, combinées, exposent les utilisateurs à des risques de fuite d’informations et d’attaques réseau.  
Un plan d’action correctif doit être engagé rapidement pour renforcer la posture de sécurité globale.
