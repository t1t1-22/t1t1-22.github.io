---
title: "Google Dorking : Exploitation Avancée avec Lynx et SGPT (Partie 2)"
categories: [Footprinting et Reconnaissance, OSINT]
tags: [Footprinting,Reconnaissance,recon,google dorking,dork]     # TAG names should always be lowercase
---



# Google Dorking : Exploitation Avancée avec Lynx et SGPT (Partie 2)


![fgoogle-dorking.png]({{ site.url }}/assets/google-dorking.png)


## Introduction

Le **Google Dorking** est une technique puissante pour découvrir des informations cachées ou sensibles disponibles en ligne. SI vous n'avez pas lu la partie 1, je vous laisse aller la lire : Partie 2 : [Google Dorking : Exploiter la Puissance de Google pour la Recherche Avancée (Partie 1)](https://t1t1-22.github.io/posts/Google_Dorking-part1/)

Dans cette deuxième partie de l'article, nous allons explorer des techniques avancées qui combinent **Lynx**, un navigateur en ligne de commande, et **SGPT**, un outil d'IA permettant de générer des requêtes Google Dorking automatiquement. Ces techniques permettent d’automatiser les recherches, d’améliorer l'efficacité, et de stocker les résultats pour une analyse approfondie.

---

## Outils Utilisés

### Lynx : Naviguer dans le Terminal

**Lynx** est un navigateur web en mode texte qui permet de naviguer sur des sites web via le terminal sans interface graphique. Cet outil est très utile pour automatiser les recherches Dorking en utilisant une faible consommation de ressources.

#### Installation de Lynx
- **Linux (Debian/Ubuntu)** :
  ```bash
  sudo apt update && sudo apt install lynx -y
  ```
- **macOS** :
  ```bash
  brew install lynx
  ```

Une fois installé, Lynx peut être utilisé pour effectuer des recherches Dorking et extraire des résultats pertinents à partir de pages web.

---

### SGPT : Génération Automatisée de Requêtes Dorking

**SGPT** (Scripted GPT) est un outil permettant de générer des requêtes Google Dorking automatiquement en utilisant l'IA. Il peut être utilisé pour automatiser l'intégration des requêtes et scripts dans le terminal.

#### Installation de SGPT
1. Installez SGPT via **pip** :
   ```bash
   pip install sgpt
   ```
2. Configurez votre clé API OpenAI :
   ```bash
   export OPENAI_API_KEY="votre_clé_API"
   ```

---

## Techniques Avancées de Google Dorking

### Technique 1 : Utilisation de l'Opérateur `filetype` pour Obtenir des Fichiers PDF

Cette technique utilise l'opérateur **`filetype`** de Google Dorking pour rechercher des fichiers PDF spécifiques sur un site donné. Vous pouvez l'utiliser pour trouver des fichiers PDF sensibles, des documents internes ou des rapports exposés.

#### Commande avec Lynx
```bash
lynx -dump "https://www.google.com/search?q=site:example.org+filetype:pdf" > recon1.txt
```

**Explication** :
- **`site:example.org`** : Limite la recherche aux résultats provenant du site **example.org**.
- **`filetype:pdf`** : Filtre pour n'inclure que les fichiers PDF.
- **`> recon1.txt`** : Enregistre les résultats dans un fichier texte `recon1.txt`.

Cette technique vous permet de rechercher des fichiers PDF spécifiques à un site, puis de stocker les liens dans un fichier pour une analyse ultérieure.

---

### Technique 2 : Générer et Exécuter une Requête avec SGPT

Dans cette technique, **SGPT** génère automatiquement une commande Google Dorking, qui sera ensuite exécutée avec **Lynx** pour extraire les résultats. Cela vous permet d'automatiser le processus de génération de requêtes Dorking.

#### Commande SGPT dans le Terminal
Exécutez la commande suivante pour démarrer une session avec SGPT et générer la requête :

```bash
sgpt --chat footprint --shell "Use filetype search operator to obtain pdf files on the target website example.org and store the result in the recon1.txt file"
```

Cela demande à **SGPT** de générer une commande pour rechercher des fichiers PDF sur **example.org**.

SGPT génère la commande suivante à exécuter :

```bash
lynx -dump "http://www.google.com/search?q=site:example.org+filetype:pdf" | grep "http" | cut -d "=" -f2 | grep -o "http[^&]*" > recon1.txt
```

Cette commande fait une recherche sur Google pour le site **example.org**, filtre les liens HTTP pointant vers des fichiers PDF, puis les enregistre dans le fichier `recon1.txt`.

---

### Technique 3 : Automatisation des Recherches avec un Script Bash

L'automatisation des requêtes Dorking à l'aide de **Lynx** et **SGPT** peut être encore améliorée en créant un script Bash. Cela permet d’exécuter plusieurs requêtes Dorking de manière rapide et de stocker les résultats de façon organisée.

#### Exemple de Script Bash
```bash
#!/bin/bash

# Liste des requêtes générées par SGPT
queries=(
  "site:example.org filetype:pdf"
  "site:example.org inurl:admin"
  "site:example.org intitle:'index of' passwords"
)

# Créer un dossier pour stocker les résultats
mkdir -p dorking_results

# Lancer chaque requête avec Lynx
for query in "${queries[@]}"; do
  echo "Exécution de : $query"
  lynx -dump "https://www.google.com/search?q=$query" > "dorking_results/$(echo $query | sed 's/[^a-zA-Z0-9]/_/g').txt"
done

echo "Résultats enregistrés dans le dossier 'dorking_results'"
```

Ce script Bash effectue plusieurs recherches Dorking et enregistre les résultats dans un dossier spécifique pour chaque requête.

---

### Technique 4 : Analyse des Résultats avec SGPT

Une fois les résultats obtenus, vous pouvez utiliser **SGPT** pour analyser les liens trouvés et extraire les informations pertinentes. Par exemple, vous pouvez demander à SGPT de repérer des mots-clés comme **"password"**, **"confidential"** ou **"login"** dans les résultats.

#### Exemple de prompt SGPT pour l'analyse :
```plaintext
Vous : Analyse ces résultats et identifie les URLs contenant des mots-clés comme 'password', 'confidential' ou 'login'.
```

SGPT vous fournira alors une liste des liens pertinents contenant ces mots-clés, ce qui peut vous aider à localiser des informations sensibles.

---

### Technique 5 : Automatisation Complète avec un Script Python

Pour ceux qui souhaitent une solution entièrement automatisée, voici un script Python qui combine **SGPT** et **Lynx** pour générer, exécuter et analyser les requêtes Dorking.

#### Exemple de Script Python
```python
import os
import subprocess
import openai

# Configurez votre clé API OpenAI
openai.api_key = "VOTRE_API_KEY"

# Fonction pour générer des requêtes Google Dorking
def generate_dork_queries(target):
    prompt = f"Génère des requêtes Google Dorking pour analyser {target}."
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=prompt,
        max_tokens=150
    )
    return response.choices[0].text.strip().split("\n")

# Cible
target = "example.org"

# Génération des requêtes
queries = generate_dork_queries(target)
print(f"Requêtes générées pour {target} :")
for query in queries:
    print(f"- {query}")

# Créer un dossier pour les résultats
os.makedirs("dorking_results", exist_ok=True)

# Exécution des requêtes avec Lynx
for query in queries:
    query_safe = query.replace(" ", "+")
    file_name = f"dorking_results/{query_safe}.txt"
    command = f'lynx -dump "https://www.google.com/search?q={query_safe}" > "{file_name}"'
    subprocess.run(command, shell=True)

print("Les résultats ont été enregistrés dans le dossier 'dorking_results'")
```

---

## Conclusion

En combinant **SGPT** pour générer automatiquement des requêtes Google Dorking et **Lynx** pour les exécuter, vous pouvez grandement automatiser le processus de reconnaissance en ligne. Ces techniques permettent d’optimiser votre flux de travail et d’augmenter l’efficacité dans la recherche d’informations sensibles.

N'oubliez pas d’utiliser ces outils dans un cadre légal et éthique, en respectant la confidentialité des informations et la législation en vigueur.

---

J'espère que cette version révisée avec des **techniques** au lieu de **étapes** correspond mieux à ce que tu attends.
