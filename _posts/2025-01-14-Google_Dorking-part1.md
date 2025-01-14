---
title: "Google Dorking : Exploiter la Puissance de Google pour la Recherche Avancée (Partie 1)"
categories: [Footprinting et Reconnaissance, OSINT]
tags: [Footprinting,Reconnaissance,recon]     # TAG names should always be lowercase
---



# Google Dorking : Exploiter la Puissance de Google pour la Recherche Avancée (Partie 1)


![fgoogle-dorking.png]({{ site.url }}/assets/google-dorking.png)


## Introduction

**Google Dorking**, également connu sous le nom de **Google Hacking**, est une technique qui consiste à utiliser des opérateurs de recherche avancée pour trouver des informations sensibles accessibles publiquement sur Internet. Bien que Google soit conçu pour fournir des résultats de recherche génériques, l'utilisation de ces opérateurs permet d'extraire des données spécifiques, souvent laissées involontairement exposées par des entreprises ou des individus.

Cet article explique les bases de Google Dorking, présente des opérateurs essentiels, montre comment l'utiliser efficacement, et met en lumière les bonnes pratiques pour éviter de tomber dans les pièges de l'exposition de données.

---

## Qu'est-ce que Google Dorking ?

Google Dorking repose sur l'utilisation d'opérateurs de recherche avancée pour affiner les résultats renvoyés par Google. Cela permet de trouver des informations spécifiques qui ne sont pas forcément visibles en utilisant des termes de recherche simples.

### Objectifs du Google Dorking :
1. **Recherche d’informations sensibles** : mots de passe, fichiers de configuration, bases de données, etc.
2. **Analyse de vulnérabilités** : identifier des pages web ou des applications vulnérables.
3. **Cartographie des actifs** : identifier des domaines, des sous-domaines, et des serveurs exposés.
4. **Évaluation de l’exposition publique** : voir ce qu’un attaquant pourrait découvrir sur une organisation.

Bien qu’elle soit une technique puissante pour les Red Teamers et les auditeurs de sécurité, elle peut également être exploitée par des acteurs malveillants pour des cyberattaques.

---

## Les Bases du Google Dorking : Les Opérateurs Essentiels

Google Dorking repose sur l’utilisation d’opérateurs spécifiques qui affinent les résultats. Voici les opérateurs les plus utilisés :

| **Opérateur**       | **Description**                                                                                     | **Exemple**                             |
|---------------------|-----------------------------------------------------------------------------------------------------|-----------------------------------------|
| `site:`            | Limite la recherche à un domaine ou site spécifique.                                                | `site:example.com`                      |
| `filetype:`        | Recherche des fichiers d'un type spécifique (PDF, DOCX, etc.).                                       | `filetype:pdf confidential`             |
| `inurl:`           | Recherche des URL contenant un mot-clé précis.                                                      | `inurl:admin`                           |
| `intitle:`         | Recherche des pages dont le titre contient un mot-clé spécifique.                                    | `intitle:index of`                      |
| `allinurl:`        | Recherche des URL contenant plusieurs mots-clés.                                                    | `allinurl:login user`                   |
| `allintitle:`      | Recherche des titres contenant plusieurs mots-clés.                                                 | `allintitle:password file`              |
| `cache:`           | Affiche la version en cache d’une page web indexée par Google.                                      | `cache:example.com`                     |
| `link:`            | Recherche des pages web contenant des liens vers une URL spécifique.                                | `link:example.com`                      |
| `-“mot”`           | Exclut un mot des résultats de recherche.                                                           | `confidential -public`                  |
| `*` (joker)        | Remplace un ou plusieurs mots inconnus ou variables.                                                | `password * user`                       |

---

## Techniques Avancées pour Exploiter Google Dorking

### 1. **Recherche de Fichiers Sensibles**
De nombreux fichiers sensibles, tels que des fichiers de configuration ou des sauvegardes, sont souvent exposés par erreur. Voici quelques requêtes courantes :
- Rechercher des fichiers de configuration exposés :  
  ```bash
  filetype:env "DB_PASSWORD"
  ```
- Identifier des sauvegardes de bases de données :  
  ```bash
  filetype:sql "backup"
  ```
- Trouver des fichiers Excel contenant des mots de passe :  
  ```bash
  filetype:xls "password"
  ```

### 2. **Découverte de Répertoires et Index Exposés**
Les répertoires "Index of" mal configurés peuvent révéler des fichiers et dossiers sensibles. Utilisez l'opérateur suivant :
```bash
intitle:"index of" "parent directory"
```

Exemple pour rechercher des fichiers multimédias exposés :
```bash
intitle:"index of" mp3
```

### 3. **Recherche de Pages d’Administration**
Les pages d’administration non sécurisées sont une cible privilégiée. Voici comment les localiser :
```bash
inurl:admin
```

Pour une recherche plus spécifique :
```bash
site:example.com inurl:admin
```

### 4. **Identification de Caméras IP et de Systèmes Connectés**
Les caméras IP et autres dispositifs IoT mal sécurisés peuvent être localisés via Google. Exemple :
```bash
inurl:"/view/index.shtml"
```

### 5. **Recherche d’Informations d’Identité**
Pour collecter des informations personnelles accessibles publiquement :
```bash
filetype:pdf "confidential" site:gov
```

---

## Automatisation avec des Outils de Google Dorking

Pour automatiser les recherches Dorking, certains outils sont largement utilisés par les Red Teamers et les pentesteurs :

1. **Google Hacking Database (GHDB)**  
   La GHDB est une base de données publique contenant des requêtes Dorking prêtes à l’emploi. Elle est disponible sur le site [Exploit-DB](https://www.exploit-db.com/google-hacking-database).

2. **GoogD0rker**  
   Un script Python permettant de lancer des recherches Dorking automatisées.  
   GitHub : [GoogD0rker](https://github.com)

3. **DorkMe**  
   Un autre outil qui aide à extraire des informations sensibles via des Dorks prédéfinis.  
   GitHub : [DorkMe](https://github.com)

---

## Précautions et Limitations

Bien que Google Dorking soit un outil puissant, il existe des limites et des précautions à prendre :
1. **Aspect légal** : L'utilisation abusive de Google Dorking pour accéder à des informations sensibles ou non autorisées peut être illégale. Restez toujours dans un cadre éthique.
2. **Résultats limités** : Google applique des restrictions aux requêtes trop fréquentes ou suspectes. Pour éviter un blocage, espacez vos recherches.
3. **Données obsolètes** : Les résultats peuvent inclure des données obsolètes ou supprimées.
4. **Blocage IP** : Trop de requêtes successives peuvent entraîner un blocage temporaire de votre adresse IP par Google.

---

## Défenses contre Google Dorking

Pour les défenseurs (Blue Teams), voici quelques stratégies pour réduire les risques d’exposition liés à Google Dorking :
1. **Éviter l’indexation de fichiers sensibles** :
   - Utilisez un fichier `robots.txt` pour empêcher l’indexation des répertoires sensibles.
   - Exemple :  
     ```
     User-agent: *
     Disallow: /admin/
     ```
2. **Surveiller les données indexées** :
   - Effectuez des recherches régulières sur votre propre domaine pour identifier des données exposées.
   - Exemple :  
     ```bash
     site:yourdomain.com
     ```
3. **Restreindre l’accès aux répertoires** :
   - Configurez correctement les permissions des fichiers et dossiers.
4. **Sensibilisation des équipes** :
   - Formez les équipes internes pour qu’elles évitent de publier des informations sensibles en ligne.

---

## Conclusion

Le **Google Dorking** est une méthode puissante pour extraire des informations spécifiques à partir de l’immense base de données de Google. Que ce soit pour des tests de sécurité ou pour évaluer l’exposition d’une organisation, cette technique est incontournable dans l’arsenal des professionnels de la cybersécurité. Cependant, elle doit toujours être utilisée de manière responsable et éthique.

Rappelez-vous : la meilleure défense contre le Google Dorking est de comprendre ses mécanismes et de limiter l’exposition des informations sensibles dès la conception des systèmes.

---

Partie 2 : [Google Dorking : Exploitation Avancée avec Lynx et SGPT (Partie 2)](https://t1t1-22.github.io/posts/Google_Dorking-part2/)
