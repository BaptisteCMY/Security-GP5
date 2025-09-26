# Vulnérabilités logiques ou mauvaises pratiques

`Manifest.xml` :

1. `<application android:usesCleartextTraffic="true">`

   L’application autorise explicitement les connexions HTTP non chiffrées. Cela expose les données en transit à des attaques de type Man-in-the-Middle (MITM).

2. ` <uses-permission android:name="android.permission.READ_CONTACTS"/>`

   READ_CONTACTS permet l’accès à tout le carnet d’adresses.

   Ces droits sont considérés comme hauts risques : fuite de données personnelles, exploitation par une application malveillante si les fichiers sont écrits dans un espace accessible publiquement.

3. `<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/> `

   WRITE_EXTERNAL_STORAGE permet d’écrire librement sur le stockage partagé.

   Ces droits sont considérés comme hauts risques : fuite de données personnelles, exploitation par une application malveillante si les fichiers sont écrits dans un espace accessible publiquement.

4. ````<receiver
           android:name="com.learnium.RNDeviceInfo.RNDeviceReceiver"
           android:enabled="true"
           android:exported="true">
           <intent-filter>
               <action android:name="com.android.vending.INSTALL_REFERRER"/>
           </intent-filter>
       </receiver>
       ```

       Ces BroadcastReceiver sont exposés (android:exported="true") et peuvent être invoqués par d’autres applications. S’il n’y a pas de vérification interne ou de permission restrictive, cela ouvre la porte à des attaques de type Intent Spoofing.
   ````
