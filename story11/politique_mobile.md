# Politique de sécurité mobile

## 1. Objectif

Protéger les données, les utilisateurs et l’infrastructure en sécurisant le développement, le déploiement et l’usage des applications mobiles.

## 2. Portée

- Équipes internes de développement, prestataires externes, utilisateurs internes et clients.

## 3. Principes

- **Confidentialité** (protection des données),
- **Intégrité** (code et données non altérés),
- **Disponibilité** (service accessible),
- **Conformité** (RGPD, CNIL, ISO 27001).

## 4. Développement sécurisé

- **Chiffrement** : TLS 1.2 minimum, interdiction du HTTP clair.
- **Données locales** : pas de stockage en clair, utiliser KeyStore/Keychain.
- **Secrets** : pas de clés dans le code, gestion via coffre-fort.
- **Dépendances** : mise à jour régulière, analyse statique/dynamique avant prod.
- **Permissions** : minimum requis, justification pour les sensibles.
- **Auth** : OAuth 2.0/OpenID Connect, MFA pour services critiques.
- **Signature** : APK v2/v3 ou équivalent, rotation régulière des certificats.

## 5. Déploiement et usage

- Publication uniquement via stores officiels ou MDM sécurisé.
- CI/CD avec tests de sécurité.
- Sensibilisation des utilisateurs et mises à jour forcées en cas de faille critique.

## 6. Gouvernance et incidents

- Revue annuelle ou après incident.
- Signalement obligatoire des violations de données.
- Responsable sécurité = suivi et audit, Dev = code sécurisé, Ops = CI/CD sécurisé.

## Résumé

- Objectif : protéger **données, utilisateurs, infrastructure**.
- Principes : **confidentialité, intégrité, disponibilité, conformité**.
- Dév sécurisé : **TLS, pas de clés dans le code, dépendances à jour, permissions minimales, authentification forte, APK v2/v3**.
- Déploiement : **stores officiels/MDM, CI/CD sécurisé, mises à jour critiques**.
- Gouvernance : **revue, signalement incidents, responsabilités claires**.
