## 🔹 Méthode 1 – MDM axé sur la **gestion des applications (MAM-first)**

* **Principe** : l’outil se concentre sur le contrôle des applications installées (audit des APK, whitelists/blacklists, contrôle des permissions).
* **Exemple** : Microsoft Intune
* **Forces** :

  * Détection rapide des applications non conformes (ex. si une app utilise `android:usesCleartextTraffic="true"` → blocage ou alerte).
  * Possibilité d’empêcher l’installation d’apps vulnérables (contrôle du store interne).
  * Intégration avec des tests automatiques de vulnérabilités connues.
* **Faiblesses** :

  * Peu efficace contre les attaques réseau (manque de protection en temps réel).
  * Ne corrige pas la mauvaise configuration d’une app interne déjà en production.

---

## 🔹 Méthode 2 – MDM axé sur la **sécurisation réseau et chiffrement (Network-first)**

* **Principe** : l’outil force les flux réseau via des tunnels sécurisés (VPN, TLS enforcement) et contrôle les communications sortantes.
* **Forces** :

  * Supprime directement le risque MITM (ton rapport montre que l’appli accepte le trafic clair HTTP → problème réglé par ce type de MDM).
  * Permet de chiffrer les données en transit même si l’application est mal configurée.
  * Détection des endpoints suspects et blocage en temps réel.
* **Faiblesses** :

  * Ne corrige pas les vulnérabilités logiques internes à l’application (ex. requêtes SQL brutes, logs sensibles, clés Firebase exposées).
  * Peut dégrader les performances si tout transite via proxy/VPN sécurisé.

---

## 🎯 Choix

Vu les failles (HTTP clair, cryptographie faible, stockage externe exposé, clés Firebase visibles…), la **méthode 2 (Network-first)** est la plus pertinente immédiatement, avec par exemple Huawei MDM Security.
Elle protège **en urgence les utilisateurs** contre les attaques réseau (MITM, exfiltration) pendant que l’équipe de développement applique les correctifs recommandés (signature v2/v3, SQL préparées, protection Firebase…).

👉 En résumé :

* Court terme → **MDM Network-first** pour contenir les risques.
* Moyen/long terme → **durcissement applicatif (MAM-first)** via refonte et bonnes pratiques.

