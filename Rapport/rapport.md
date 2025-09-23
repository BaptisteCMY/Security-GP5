RAPPORT DE VULNERABILITÉS APP.APK


Story 1 : 
Empreinte SHA-256 du fichier : f01f08c8b6e2fe4612e81bc7e3a3ba9440dad0d7a962f4e67640390dd721d528


Story 2 :
-	Réseau non chiffré : l’application autorise du trafic HTTP en clair.
-	Signature et cryptographie faibles : utilisation de la signature v1 (faille Janus), de SHA-1 et de MD5, et d’un générateur aléatoire peu sûr.
-	Chiffrement CBC vulnérable : risque d’attaque « padding oracle ».
-	Composants Android exposés : plusieurs services/receivers accessibles à d’autres applis.
-	Stockage externe non protégé : données lisibles par d’autres applications.
-	Requêtes SQL brutes et logs sensibles : risque d’injection et fuite d’informations.
Score de sécurité global : 49/100 (risque moyen).


Story 5 : 
- Constat :
    - Endpoints sensibles : RLs de services bancaires et d’authentification exposées.
    - Clés Firebase/Google : API key, app ID, database URL, storage bucket, client ID.
    - Push/notifications : GCM_PROJECT_NUMBER, PUSHWOOSH_APPID.
    - Identifiant de build : facilite le fingerprinting.
- Risques :
    - Abus d’API, accès non autorisé, reconnaissance d’infrastructure, attaques ciblées ou spam push.
- Recommandations :
    - Stocker secrets dans un coffre-fort sécurisé.
    - Appliquer des règles strictes côté Firebase/Pushwoosh.
    - Éviter d’exposer les URLs sensibles dans l’application.