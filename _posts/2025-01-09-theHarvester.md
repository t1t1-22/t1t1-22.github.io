---
title: "TheHarvester : Un Outil de Reconnaissance en Source Ouverte"
categories: [Collecte d'informations, OSINT]
tags: [information gathering, OSINT,Collecte d'informations]     # TAG names should always be lowercase
---

![theHarvester.png]({{ site.url }}/assets/theHarvester.png)


## Qu'est-ce que TheHarvester ?

[TheHarvester](https://github.com/laramies/theHarvester) est un outil de collecte d'informations en source ouverte (OSINT - Open Source Intelligence) développé en Python qui permet de récupérer des informations à partir de diverses sources publiques. Ces informations peuvent inclure des adresses e-mail, des sous-domaines, des adresses IP, des noms d'hôtes, et bien plus encore. L'outil utilise plusieurs moteurs de recherche et services en ligne pour extraire ces données, facilitant ainsi les opérations de reconnaissance lors d'une évaluation de sécurité.

### Fonctionnalités principales de TheHarvester

Voici un aperçu des principales fonctionnalités offertes par **TheHarvester** :

1. **Recherche d'adresses e-mail** : TheHarvester peut rechercher des adresses e-mail associées à un domaine particulier en interrogeant des moteurs de recherche et des bases de données publiques.
2. **Recherche de sous-domaines** : L'outil permet de découvrir des sous-domaines liés à un domaine cible, ce qui est essentiel pour identifier des points d'entrée potentiels dans un réseau.
3. **Recherche d'informations DNS** : TheHarvester peut interroger des services DNS pour obtenir des informations sur les serveurs de noms, les adresses IP, et les configurations de réseau liées à un domaine.
4. **Utilisation de moteurs de recherche et d'API** : Il utilise des moteurs de recherche comme Google, Bing, Yahoo, et des services comme Shodan et VirusTotal pour collecter des données.
5. **Génération de rapports** : TheHarvester peut exporter les résultats sous forme de fichiers HTML, XML, ou plain text, facilitant ainsi l’analyse et la documentation des résultats.

### Sources de données utilisées par TheHarvester

TheHarvester s'appuie sur plusieurs sources publiques pour effectuer ses recherches. Voici quelques-unes des sources les plus courantes :

- **Google, Bing, Yahoo** : Ces moteurs de recherche permettent de découvrir des adresses e-mail et des informations liées à un domaine spécifique.
- **Shodan** : Un moteur de recherche spécialisé dans la découverte d'appareils connectés à Internet (IoT, serveurs, etc.).
- **Have I Been Pwned** : Permet de vérifier si un domaine ou une adresse e-mail a été compromis dans une violation de données.
- **VirusTotal** : Permet de vérifier des fichiers et des adresses IP, et de récupérer des informations sur des sites malveillants ou compromis.
- **Netcraft** : Fournit des informations sur les serveurs web et les technologies associées à un domaine.

## Installation de TheHarvester

L'installation de TheHarvester est relativement simple. Suivez les étapes ci-dessous pour l'installer sur un système Linux ou macOS.

### Prérequis

Avant d'installer **TheHarvester**, vous devez avoir Python 3.x installé sur votre machine. Vous pouvez vérifier la version de Python en utilisant la commande suivante :

```bash
python3 --version
```

### Installation via Git

1. **Clonez le dépôt GitHub** :

   Ouvrez un terminal et clonez le dépôt officiel de TheHarvester :

   ```bash
   git clone https://github.com/laramies/theHarvester.git
   ```

2. **Installez les dépendances** :

   Rendez-vous dans le répertoire du projet cloné et installez les dépendances requises :

   ```bash
   cd theHarvester
   pip3 install -r requirements.txt
   ```

3. **Exécution de l'outil** :

   Une fois les dépendances installées, vous pouvez lancer **TheHarvester** en utilisant la commande suivante :

   ```bash
   python3 theHarvester.py -d example.com -b google
   ```

   Ici, `-d example.com` spécifie le domaine que vous voulez analyser et `-b google` définit le moteur de recherche à utiliser.

### Options de ligne de commande

TheHarvester offre une multitude d'options de ligne de commande que vous pouvez utiliser pour personnaliser vos recherches. Voici quelques options courantes :

- `-d` : Définir le domaine à analyser.
- `-b` : Choisir le moteur de recherche ou la source d'information (par exemple, google, bing, shodan, etc.).
- `-l` : Limiter le nombre de résultats.
- `-s` : Spécifier un serveur DNS pour la recherche.
- `-f` : Exporter les résultats dans un fichier (HTML, XML, ou texte brut).

Exemple complet :

```bash
python3 theHarvester.py -d example.com -b google -l 100 -f results.html
```


---

## Exemples Concrets d'Utilisation de TheHarvester

### 1. **Recherche d'adresses e-mail associées à un domaine**

Imaginons que vous soyez un professionnel de la cybersécurité et que vous souhaitiez analyser le domaine d'une entreprise spécifique, par exemple **example.com**, pour rechercher des adresses e-mail qui y sont associées. Cela peut être utile pour tester la robustesse d'une organisation face à des attaques de phishing.

Voici comment vous pouvez utiliser **TheHarvester** pour obtenir cette information :

#### Commande :
```bash
python3 theHarvester.py -d example.com -b google -l 100 -f emails_results.html
```

- **`-d example.com`** : Le domaine à analyser.
- **`-b google`** : Le moteur de recherche Google est utilisé pour rechercher les informations.
- **`-l 100`** : Limite à 100 résultats.
- **`-f emails_results.html`** : Les résultats sont exportés dans un fichier HTML.

#### Résultats attendus :
Vous obtiendrez une liste d'adresses e-mail associées au domaine **example.com**, comme :

- `contact@example.com`
- `support@example.com`
- `admin@example.com`
- `info@example.com`

Ces informations peuvent ensuite être utilisées pour tester la gestion des comptes, leur sécurité ou pour identifier des cibles potentielles pour une attaque de phishing.

### 2. **Découverte des sous-domaines d'un domaine**

La découverte des sous-domaines est une étape clé dans la reconnaissance de cible, car ces sous-domaines peuvent parfois offrir des points d'entrée supplémentaires pour une attaque ou une évaluation de sécurité. Imaginons que vous vouliez découvrir les sous-domaines associés au domaine **example.com**.

#### Commande :
```bash
python3 theHarvester.py -d example.com -b bing -l 50 -f subdomains_results.html
```

- **`-d example.com`** : Le domaine à analyser.
- **`-b bing`** : Le moteur de recherche Bing est utilisé pour rechercher des informations sur les sous-domaines.
- **`-l 50`** : Limite à 50 résultats.
- **`-f subdomains_results.html`** : Exportation des résultats sous forme de fichier HTML.

#### Résultats attendus :
Vous pourriez découvrir des sous-domaines associés à **example.com**, tels que :

- `mail.example.com`
- `dev.example.com`
- `www.example.com`
- `api.example.com`

Ces sous-domaines peuvent contenir des services ou des applications non sécurisés qui peuvent être des cibles potentielles pour une exploitation.

### 3. **Utilisation de Shodan pour rechercher des serveurs vulnérables**

**Shodan** est un moteur de recherche spécialisé dans la recherche d'appareils et de services connectés à Internet, tels que des serveurs, des routeurs, des caméras IP, etc. Il peut être utilisé avec **TheHarvester** pour rechercher des informations sur des appareils ou des serveurs associés à un domaine spécifique.

#### Commande :
```bash
python3 theHarvester.py -d example.com -b shodan -f shodan_results.html
```

- **`-d example.com`** : Le domaine cible.
- **`-b shodan`** : Utilisation de Shodan pour rechercher des services ou des appareils connectés à Internet associés à ce domaine.
- **`-f shodan_results.html`** : Les résultats sont enregistrés dans un fichier HTML.

#### Résultats attendus :
En utilisant Shodan, vous pourriez découvrir des informations sensibles, telles que :

- Des serveurs web exposés.
- Des ports ouverts (par exemple, un serveur FTP ou SSH mal configuré).
- Des adresses IP publiques associées à des serveurs d'application ou de base de données.

Ces informations peuvent être extrêmement utiles pour un pentester qui cherche à évaluer la surface d'attaque d'un domaine.

### 4. **Recherche d'informations sur un domaine via VirusTotal**

VirusTotal permet de rechercher des informations sur un domaine, une adresse IP ou un fichier pour vérifier s'il a été signalé comme malveillant. Vous pouvez utiliser **TheHarvester** pour interroger VirusTotal à propos d'un domaine.

#### Commande :
```bash
python3 theHarvester.py -d example.com -b virustotal -f virustotal_results.html
```

- **`-d example.com`** : Le domaine à analyser.
- **`-b virustotal`** : Recherche d'informations sur VirusTotal concernant ce domaine.
- **`-f virustotal_results.html`** : Exportation des résultats dans un fichier HTML.

#### Résultats attendus :
Les résultats peuvent inclure des informations telles que :

- Les fichiers malveillants associés au domaine.
- L'historique des rapports de sécurité liés à ce domaine.
- Des adresses IP ou des sous-domaines signalés comme malveillants.

Ces résultats peuvent aider à déterminer si un domaine ou un serveur est déjà compromis ou associé à des activités malveillantes.

### 5. **Vérification d'une adresse e-mail avec "Have I Been Pwned"**

**Have I Been Pwned** est un service qui permet de vérifier si une adresse e-mail a été impliquée dans une violation de données. Vous pouvez utiliser **TheHarvester** pour interroger cette base de données et déterminer si des adresses e-mail récupérées dans le cadre de votre recherche ont été compromises.

#### Commande :
```bash
python3 theHarvester.py -d example.com -b haveibeenpwned -f hibp_results.html
```

- **`-d example.com`** : Le domaine à analyser.
- **`-b haveibeenpwned`** : Interroger le service "Have I Been Pwned" pour vérifier si des adresses e-mail associées à ce domaine ont été compromises.
- **`-f hibp_results.html`** : Exportation des résultats dans un fichier HTML.

#### Résultats attendus :
Vous pourriez obtenir des résultats indiquant que certaines adresses e-mail associées au domaine **example.com** ont été compromises dans des violations de données passées, comme :

- `contact@example.com` : A été impliqué dans une fuite de données en 2019.
- `support@example.com` : A été compromis dans une attaque de phishing en 2020.

Cette information peut être cruciale pour déterminer les risques associés à un domaine et aider à prioriser les mesures de sécurité à mettre en place.

---

## Conclusion

L'utilisation de **TheHarvester** permet de réaliser une reconnaissance approfondie sur un domaine, en collectant une multitude d'informations qui peuvent être utilisées pour renforcer la sécurité d'un site web, découvrir des vulnérabilités potentielles ou préparer une attaque éthique (pentest). En combinant les différentes sources de données accessibles via **TheHarvester** (Google, Shodan, VirusTotal, Have I Been Pwned, etc.), vous pouvez obtenir une vue d'ensemble détaillée de l'infrastructure d'une organisation et identifier des failles de sécurité importantes.

N'oubliez pas que cet outil doit être utilisé dans un cadre légal et éthique, et que l'obtention d'informations sensibles sans autorisation peut être illégale et contraire à l'éthique.
