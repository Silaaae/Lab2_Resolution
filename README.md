Resolution de Lab2 : 
Livrable:


1. Définition du rooting 
Définition technique : Le rooting consiste à acquérir les privilèges super-utilisateur (uid=0), permettant de s'affranchir des restrictions imposées par le fabricant.
Impact sur la sécurité : Cette manipulation modifie profondément les protections natives et la chaîne de confiance du système, rendant ce dernier plus vulnérable.
Usage en laboratoire : En environnement contrôlé, le rooting est un outil indispensable pour observer des comportements système normalement masqués et mener des audits de sécurité.
Précautions nécessaires : En raison des risques d'instabilité et d'exposition des données, le rooting nécessite un isolement strict, une traçabilité des actions et un reset complet après les tests.
Analogie pour débutants : Le rooting, c'est comme avoir un passe-partout pour toutes les portes d'un bâtiment. Très utile pour le personnel de maintenance, mais dangereux si mal utilisé ou si ce passe tombe entre de mauvaises mains.
Contexte historique : Le terme "root" vient d'UNIX, où l'utilisateur administrateur s'appelle "root". Sur Android (basé sur Linux), obtenir les privilèges root signifie devenir cet administrateur tout-puissant.

2. Schéma simple Verified Boot / AVB (Chaîne de confiance)
Chaîne de confiance du Verified Boot :

Bootloader → vérifie l'intégrité du système
Système vérifié → lance Android de manière sécurisée
Protection anti-rollback → empêche l'installation de versions obsolètes vulnérables

<img width="300" height="585" alt="image" src="https://github.com/user-attachments/assets/c8a345c7-5a26-41a7-8420-1b27d82409b2" />
Concept et Théorie
Objectif principal : Garantir que le système qui démarre est exactement celui prévu par le fabricant, sans aucune modification malveillante.
Définition de "Chain of Trust" : Série de vérifications où chaque composant vérifie l'authenticité du suivant avant de lui faire confiance. C'est comme une chaîne de gardiens où chacun vérifie l'identité du suivant.
Importance de l'intégrité au démarrage : Si le démarrage est compromis, toutes les protections ultérieures (permissions, bac à sable) peuvent être contournées, comme une forteresse dont la porte principale serait laissée ouverte.
Vérification pratique sur le Pixel 6 (API 36)
<img width="575" height="695" alt="image" src="https://github.com/user-attachments/assets/d34b10f8-85d6-478a-9277-be7ddd6e9450" />
<img width="575" height="525" alt="image" src="https://github.com/user-attachments/assets/91261d6e-3b1d-4ef6-b546-6607e91876bb" />
Lors de la tentative de désactivation de la vérification avec disable-verity, le système a répondu : "Device must be bootloader unlocked". Cela démontre que le Verified Boot du Pixel 6 est si robuste qu'un accès root (uid=0) ne suffit pas à briser la chaîne de confiance sans une action physique sur le bootloader.
<img width="475" height="543" alt="image" src="https://github.com/user-attachments/assets/6c7e6048-9313-428e-9cc7-6421a3c703f5" />
AVB (Android Verified Boot 2.0)
Évolution technologique : AVB est la version 2.0 de Verified Boot, plus moderne et flexible. C'est comme passer d'une serrure mécanique à un système de sécurité électronique programmable.
Synthèse AVB : AVB ajoute une vérification d'intégrité moderne grâce à l'utilisation de descripteurs de hachage et de signatures cryptographiques pour chaque partition système. Cette technologie assure que le logiciel n'a pas été altéré et permet une gestion granulaire de la confiance entre le bootloader et les partitions système. Elle intègre également une protection matérielle contre le rollback.
Explication de la protection anti-rollback : Cette protection empêche d'installer d'anciennes versions du système qui pourraient contenir des failles de sécurité connues. C'est comme empêcher quelqu'un de remplacer votre serrure moderne par un ancien modèle facilement crochetable.
Constat sur le Pixel 6 (API 36) : Lors de l'audit, la commande disable-verity a été rejetée avec le message "Device must be bootloader unlocked", confirmant que l'AVB est actif et protège l'intégrité du système contre toute modification non autorisée, même avec un accès root.
<img width="588" height="553" alt="image" src="https://github.com/user-attachments/assets/238450c7-943e-4be3-b16f-376d938c9ca2" />

3. Matrice : 8 risques + 8 mesures défensives
Conformément aux principes de gestion des risques en cybersécurité, chaque menace identifiée lors de cet audit sur Pixel 6 a été analysée pour définir son impact potentiel.
Risque identifiéDescriptionMesure défensiveJustificationIntégrité non garantieUne partition système modifiée peut mener à des conclusions biaisées sur la sécurité réelle de l'appareilDevice/AVD dédiéL'utilisation d'un émulateur Pixel 6 spécifique garantit que les tests n'affectent pas d'autres environnements de travailSurface d'attaque accrueSi l'appareil rooté sort du cadre du laboratoire, il subit une exposition accrue aux menaces externesRéseau isoléL'utilisation d'un réseau restreint permet d'éviter toute communication non contrôlée avec des serveurs externesExposition de données sensiblesLa présence de données réelles sur un appareil rooté peut entraîner une violation potentielle de la confidentialitéDonnées fictivesSeules des données de test sont utilisées pour éliminer tout risque de fuite d'informations réellesInstabilité systèmeLes modifications de bas niveau provoquent souvent une instabilité rendant les tests non reproductibles et les résultats incohérentsSnapshots ou wipeUne réinitialisation complète est effectuée en fin de séance pour ne laisser aucune trace des modifications systèmeMélange des comptesL'utilisation simultanée de comptes personnels et de test risque de provoquer une fuite d'informations privéesAucun compte personnelL'absence de comptes réels sur l'appareil évite tout mélange de données privées et professionnellesMauvais nettoyageUn oubli de réinitialisation en fin de séance permet la persistance de données sensibles accessibles au prochain utilisateurJournal de configurationUn log précis des commandes (comme adb root ou disable-verity) assure la reproductibilité de l'auditRéseau non isoléUn appareil de test non isolé peut générer des effets involontaires ou des attaques sur des systèmes externesContrôle des APKL'installation est limitée aux seuls outils nécessaires au TP pour réduire les risques d'infectionTraçabilité insuffisanteSans un log précis des actions effectuées (comme adb shell), il devient impossible de reproduire ou d'auditer les testsTraçabilité complèteL'horodatage et les captures d'écran systématiques permettent d'auditer chaque étape du processus
Principe de sécurité : Chaque risque identifié doit être systématiquement associé à une mesure d'atténuation correspondante (ex: isolation réseau, reset usine) pour garantir la validité de l'audit.
Analogie de sécurité : Ces mesures défensives fonctionnent comme les protocoles de sécurité dans un laboratoire manipulant des substances dangereuses : isolement, équipement dédié, procédures de décontamination, et documentation rigoureuse.
<img width="609" height="583" alt="image" src="https://github.com/user-attachments/assets/2ebe8a10-a246-4b47-a99b-c2235540530a" />
<img width="588" height="590" alt="image" src="https://github.com/user-attachments/assets/96cd0901-5599-4ccf-8695-9b1a9a0efa69" />

4. OWASP MASVS : 2 exigences résumées
Contexte pour débutants : OWASP (Open Web Application Security Project) est une organisation de référence mondiale en cybersécurité. Le MASVS (Mobile Application Security Verification Standard) est leur standard officiel pour évaluer et garantir la sécurité des applications mobiles.
Exigence 1 : STORAGE-1 (Stockage sécurisé)
Description : Les données sensibles comme les clés API, les mots de passe ou les tokens doivent être stockées de manière sécurisée en utilisant des fonctions de chiffrement appropriées fournies par le système.
Application pratique lors de l'audit : L'obtention des privilèges root sur le Pixel 6 (via adb root) permet de vérifier concrètement si une application respecte cette exigence en examinant directement ses fichiers de stockage privés. Avec les privilèges root, il est possible d'examiner comment une application stocke ses données sensibles et de vérifier si elle repose uniquement sur la protection du système (mauvaise pratique) ou si elle implémente son propre mécanisme de chiffrement (bonne pratique).
Exigence 2 : NETWORK-1 (Communications réseau)
Description : Toutes les communications réseau doivent impérativement utiliser le protocole TLS avec une configuration correcte et une vérification stricte des certificats pour éviter les interceptions.
Application pratique lors de l'audit : En environnement rooté, il est possible d'intercepter le trafic réseau pour s'assurer qu'il est bien chiffré et que les certificats sont correctement vérifiés.

5. OWASP MASTG : 2 idées de tests
Relation MASVS-MASTG : Si le MASVS définit le "quoi" vérifier, le MASTG (Mobile Application Security Testing Guide) explique le "comment" le vérifier techniquement. C'est le manuel pratique indispensable pour mener les tests d'intrusion.
Test 1 : Examen du stockage local
Objectif : Vérifier si les fichiers de préférences partagées contiennent des informations sensibles en clair en les examinant directement dans le répertoire /data/data/[package_name]/shared_prefs/.
Procédure technique :

Obtenir les privilèges root avec adb root
Confirmer l'accès avec adb shell id (doit retourner uid=0)
Examiner le répertoire /data/data/[package_name]/shared_prefs/
Ouvrir les fichiers XML et rechercher des données sensibles non chiffrées

Astuce technique : Grâce aux privilèges obtenus avec adb root, il est possible d'accéder au dossier protégé /data/data/. Ce dossier contient toutes les données privées des applications qui sont normalement inaccessibles à un utilisateur ou à une application tierce, permettant ainsi de valider le respect des standards de sécurité.
Constat lors de l'audit : Grâce aux privilèges root, j'ai pu contourner la restriction 'Permission denied' et accéder au répertoire /data/data/, ce qui permet d'auditer les fichiers de préférences locales conformément au guide MASTG.
Test 2 : Analyse des flux de sortie (Logcat)
Objectif : Analyser les logs de l'application en temps réel avec la commande adb logcat pour détecter des fuites d'informations sensibles (tokens, identifiants) pendant l'exécution.
Procédure technique :

Lancer l'application cible
Démarrer la capture des logs avec adb logcat
Effectuer des actions dans l'application (connexion, requêtes API, etc.)
Rechercher dans les logs : tokens, identifiants, mots de passe, clés API

Résultat attendu : Aucune information sensible ne doit apparaître dans les logs système.
Synthèse technique des commandes

Synthèse des commandes : L'utilisation combinée de adb root et adb shell id a permis de confirmer l'accès aux privilèges super-utilisateur (uid=0).
Analyse de dm-verity : La commande disable-verity a agi comme une tentative de désactivation de l'alarme système. Le refus de l'appareil (bootloader unlocked) démontre que sur Pixel 6, la protection des partitions en lecture seule est verrouillée au niveau matériel.
Option Magisk : Pour contourner ces détections, un rooting "systemless" via Magisk serait nécessaire, car il modifie l'image de boot sans altérer la partition système, permettant de rester invisible pour certaines vérifications de sécurité.
Conclusion technique : L'impossibilité d'exécuter adb remount après le redémarrage confirme que la chaîne de confiance (Chain of Trust) est intacte et opérationnelle sur cet environnement.


6. Fiche environnement remplie
1️⃣ Informations générales
ÉlémentValeurDate14/02/2026AuteurNisrine EliSupport utiliséAVD – Pixel 6 (Android Studio Emulator)Type d'environnementEnvironnement de laboratoire autoriséVersion AndroidAndroid 14API LevelAPI 36
2️⃣ Application testée
ÉlémentValeurNom de l'applicationGoogleVersionVersion préinstallée sur image système AVD officielleSourceImage système officielle Android Studio (non modifiée)
3️⃣ Scénarios exécutés
Scénario 1 : Lancement et vérification de l'intégrité

Description : Ouvrir l'application et confirmer l'affichage du contenu initial
Action utilisateur : Cliquer sur l'icône "MyApplication" depuis le menu des applications de l'émulateur Pixel 6
Résultat attendu : L'application s'ouvre sans erreur et affiche le texte "Hello World!" au centre de l'écran
Documentation : Capture d'écran de l'application ouverte sur l'émulateur

<img width="585" height="539" alt="image" src="https://github.com/user-attachments/assets/780936f3-47f4-40da-a5f0-a237f8b6530f" />
Scénario 2 : Rechercher un item

Objectif : Tester la fonctionnalité de recherche de l'application
Préconditions : Application ouverte sur l'écran d'accueil
Étapes :

Cliquer sur la barre de recherche
Saisir exactement le texte "tomate cafe"
Appuyer sur la touche "Entrée"
Attendre le chargement des résultats



<img width="326" height="500" alt="image" src="https://github.com/user-attachments/assets/cda81efb-6455-4859-ab46-7e3bcb64f640" />
Scénario 3 : Ouvrir la fiche détail

Objectif : Vérifier l'accès à la fiche détaillée d'un résultat
Préconditions : Résultats de recherche affichés pour "tomate cafe"
Étapes :

Dans la liste des résultats, cliquer sur "Tomate Cafe"
Attendre le chargement de la fiche détaillée
Observer les informations affichées



<img width="413" height="564" alt="image" src="https://github.com/user-attachments/assets/968946ca-294b-432a-92dc-9807dfe6e4be" />
4️⃣ Observations factuelles

L'application se lance correctement sans erreur
La recherche du terme « tomate cafe » retourne des résultats cohérents
L'ouverture de la fiche détail fonctionne normalement
La commande adb disable-verity a échoué avec le message : "Device must be bootloader unlocked"
La commande adb shell getprop ro.boot.verifiedbootstate retourne : "green"
Cela indique que Verified Boot est actif et que l'image système est intègre

Modèle de sécurité Android (Contexte)
Android utilise un modèle de sécurité multi-couches pour protéger les appareils et les données :

Chaque application fonctionne dans une sandbox séparée, ce qui l'isole des autres apps et du système principal
Le modèle de permissions oblige l'application à demander l'autorisation de l'utilisateur avant d'accéder à des ressources sensibles
Le système maintient aussi l'intégrité du système global, empêchant les modifications non autorisées du logiciel
Ces protections limitent les interactions non sécurisées entre applications et le système

<img width="575" height="457" alt="image" src="https://github.com/user-attachments/assets/72fc95fc-d3c5-4af4-8abe-b970129b182f" />

7. Checklist reset signée + preuves
Procédure de décontamination effectuée
La procédure de fin de séance sur le Pixel 6 API 36 a consisté à vérifier qu'aucun certificat racine n'a été ajouté durant les tests OWASP, garantissant qu'aucune interception de trafic ne reste possible après mon passage.
L'appareil a été remis dans son état initial "neuf" via la fonction Wipe Data d'Android Studio, ce qui est plus efficace qu'une simple réinitialisation logicielle car cela efface la partition utilisateur au niveau du bloc.
Cette étape de "décontamination" est cruciale pour éviter toute fuite de données ou persistance de privilèges root pour le prochain utilisateur.
Actions effectuées :

✓ Vérification qu'aucun certificat racine n'a été ajouté
✓ Wipe Data complet via Android Studio
✓ Effacement de la partition utilisateur au niveau du bloc
✓ Appareil remis dans son état initial "neuf"

Preuves : Captures d'écran des commandes de wipe et de l'état final de l'AVD.
