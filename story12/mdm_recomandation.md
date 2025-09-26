## Méthode 1 – MDM axé sur la **gestion des applications (MAM-first)**

- **Principe** : l’outil se concentre sur le contrôle des applications installées (audit des APK, whitelists/blacklists, contrôle des permissions).
- **Exemple** : Scalefusion
- **Forces** :
  - Détection rapide des applications non conformes (ex. si une app utilise `android:usesCleartextTraffic="true"` → blocage ou alerte).
  - Possibilité d’empêcher l’installation d’apps vulnérables (contrôle du store interne).
  - Intégration avec des tests automatiques de vulnérabilités connues.
- **Faiblesses** :
  - Peu efficace contre les attaques réseau (pas de protection en temps réel contre MITM).
  - Ne corrige pas la mauvaise configuration d’une app interne déjà en production.

---

## Méthode 2 – MDM axé sur la **sécurisation réseau et chiffrement (Network-first)**

- **Principe** : l’outil force les flux réseau via des tunnels sécurisés (VPN, TLS enforcement) et contrôle les communications sortantes.
- **Exemple** : Microsoft Intune **avec Tunnel + Defender for Endpoint**
- **Forces** :
  - Supprime directement le risque MITM (ex. si l’appli accepte le trafic clair HTTP → le tunnel bloque/force le chiffrement).
  - Permet de chiffrer les données en transit même si l’application est mal configurée.
  - Détection des endpoints suspects et blocage en temps réel grâce à Defender ATP.
  - Compatible **Windows, macOS, iOS et Android** → administration centralisée multi-OS.
  - Intégration native avec **Azure AD** et Microsoft 365 (Conditional Access, SIEM, etc.).
- **Faiblesses** :
  - Ne corrige pas les vulnérabilités logiques internes à l’application (ex. requêtes SQL brutes, logs sensibles, clés Firebase exposées).
  - Performances réseau parfois impactées (latence induite par le tunnel sécurisé).

---

## Comparatif des solutions Network-first

| Critère                     | VMware Workspace ONE | Microsoft Intune (Tunnel + Defender) | Ivanti (MobileIron)  |
| --------------------------- | -------------------- | ------------------------------------ | -------------------- |
| **Multi-OS (Win/macOS)**    | oui                  | oui                                  | oui                  |
| **VPN par application**     | ✅ Granulaire        | ✅ Avec Tunnel                       | ✅ AppTunnel robuste |
| **Blocage HTTP clair**      | oui                  | oui (via Defender)                   | oui                  |
| **Détection endpoints**     | avancée (SD-WAN)     | forte (Defender ATP)                 | basique              |
| **Performance réseau**      | parfois lourd        | dépend du Tunnel                     | léger                |
| **Facilité administration** | ⭐⭐⭐               | ⭐⭐⭐⭐ (Azure natif)               | ⭐⭐⭐⭐             |
| **Intégration écosystème**  | très large           | parfaite si Microsoft                | correcte             |

---

## Choix

Vu les failles identifiées (HTTP clair, cryptographie faible, stockage externe exposé, clés Firebase visibles…), la **méthode 2 (Network-first)** est la plus pertinente immédiatement.

La solution retenue est **Microsoft Intune (Tunnel + Defender)**, car elle offre :

- une **protection réseau immédiate** contre MITM et exfiltration,
- une **compatibilité multi-OS** (Windows + macOS, répondant à nos besoins internes),
- une **intégration native** avec l’infrastructure Microsoft déjà en place.

En résumé :

- **Court terme** → Déploiement de **Microsoft Intune Network-first** pour contenir les risques.
- **Moyen/long terme** → Passage progressif vers le **durcissement applicatif (MAM-first)** via refonte du code et bonnes pratiques de développement sécurisé.

---

[[rapport_audit_technique#^de0c2e]]
