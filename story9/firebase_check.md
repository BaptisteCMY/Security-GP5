## 1) Contexte (user story)
**EN TANT QUE** pentesteur  
**JE VEUX** tester l'accès public à une éventuelle base Firebase découverte dans l'APK  
**AFIN DE** évaluer le risque de fuite de données

## 2) Méthode
1. Décompilation de l'APK :
   - `apktool d app.apk -o app_decompiled`
   - Recherche de configuration Firebase :
     - `grep -R "firebaseio.com" app_decompiled/`
     - Résultat : URL détectée → `https://application-client-nickel.firebaseio.com/`

2. Test d'accès (Realtime Database) :
   - Commande exécutée :
     ```
     curl -v -X GET "https://application-client-nickel.firebaseio.com/.json"
     ```

## 3) Résultat / preuve
   - Réponse :
     ```
     
     % Total   % Received  % Xferd  Average Speed  Time  Time  Time  Current  Dload    Upload   Total Spent Left  Speed
     100       36          100      36             0     0     68    0        --:--:-- --:--:-- --:--:--          69
     {
        "error" : "Permission denied"
     }
     ```