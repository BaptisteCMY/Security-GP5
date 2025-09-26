# Détecter les failles de sécurité :

- Ouvrir le fichier `strings.xml ` en accédant au chemin `app/res/values`

- Lister les fichiers avec la commande `ls` et le fichier `strings.xml` apparaitra.

- Aller sur cet url `https://jsonformatter.org/xml-parser` et importer le fichier `strings.xml` pour permettre une meilleure lecture des vulnéaribilités.

## 1 - Endpoints sensibles

`https://api.nickel.eu/customer-banking-api`

`https://api.nickel.eu/customer-authentication-api`

`https://api.nickel.eu/personal-space-api`

- Risque : donne les adresses précises des services bancaires et d’authentification.

## 2️ - Identifiants Firebase/Google

`google_api_key : AIzaSyBTgztvImsUfMWDa41PCrDWAj7dmyIDhUg`

`google_app_id : 1:717748501407:android:e0cdeb712e27275d`

`default_web_client_id : 717748501407-v621tmfvqd7etdouc3df5vuv31scle0g.apps.googleusercontent.com`

`firebase_database_url : https://application-client-nickel.firebaseio.com`

`google_storage_bucket : application-client-nickel.appspot.com`

`gcm_defaultSenderId : 717748501407`

- Risque : expose le projet Firebase/Google ; peut faciliter un abus d’API ou un accès non autorisé si les règles ne sont pas strictes.

## 3️ Identifiants de push/notifications

`GCM_PROJECT_NUMBER : 246766419677`

`PUSHWOOSH_APPID : DDF11-D1BF9`

- Risque : informations sur l’infrastructure de notifications, exploitables pour du spam ou des attaques push si mal protégées.

## 4️ Identifiant de build

`com.crashlytics.android.build_id : 9ad6d741-4e10-4abd-9f12-163a0e3fb0b2`

- Risque : facilite le fingerprinting de la version exacte de l’application.
