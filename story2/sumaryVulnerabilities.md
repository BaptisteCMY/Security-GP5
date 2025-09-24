-	Réseau non chiffré : l’application autorise du trafic HTTP en clair.
-	Signature et cryptographie faibles : utilisation de la signature v1 (faille Janus), de SHA-1 et de MD5, et d’un générateur aléatoire peu sûr.
-	Chiffrement CBC vulnérable : risque d’attaque « padding oracle ».
-	Composants Android exposés : plusieurs services/receivers accessibles à d’autres applis.
-	Stockage externe non protégé : données lisibles par d’autres applications.
-	Requêtes SQL brutes et logs sensibles : risque d’injection et fuite d’informations.
Score de sécurité global : 49/100 (risque moyen).