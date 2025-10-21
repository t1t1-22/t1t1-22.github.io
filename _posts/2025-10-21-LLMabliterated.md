---
title: "Modèles LLM « ablités » en cyber-offensive : risques et cas d’usage"
categories: [recherche,llm]
tags: [llm,recherche,billet,ia]
---

# Modèles LLM « ablités » en cyber-offensive : risques et cas d’usage

Les modèles ablitérés (abréviation de « abliterated », c’est‐à‐dire « allégés » ou « uncensored ») sont des modèles de langage (LLM) dont on a supprimé les garde-fous et parfois compressés pour tourner localement. En pratique, on crée de tels modèles en désactivant les filtres de sécurité et en appliquant des méthodes de compression (quantification, élagage, distillation, etc.) pour réduire leur taille. La compression de modèle vise à diminuer le nombre de paramètres ou leur précision afin de faciliter le déploiement (par ex. sur des machines à ressources limitées). Les techniques principales sont :

- **Quantification** : réduire la précision des poids (p. ex. de 32 bits flottant à 8 bits), ce qui réduit l’empreinte mémoire  
  [Medium](https://medium.com/@chaitanya17.sai/quantization-distillation-and-pruning-437110a322c7)

- **Pruning (élagage)** : supprimer des neurones, couches ou connexions jugés redondants  
  [Medium](https://medium.com/@chaitanya17.sai/quantization-distillation-and-pruning-437110a322c7)

- **Distillation** : entraîner un petit modèle (« étudiant ») à imiter un grand modèle (« enseignant »)  
  [Medium](https://medium.com/@chaitanya17.sai/quantization-distillation-and-pruning-437110a322c7)

Ces optimisations réduisent la taille et le coût de calcul des LLM, souvent au prix d’une dégradation minime de la performance. Un modèle ablité peut ainsi être livré sous forme condensée (int8, quantifié 4 bits, pruné, format GGUF/ggml, etc.) et déployé en local, sans solliciter l’API du fournisseur. Toutefois, son désavantage majeur est l’absence totale de filtres de contenus. L’« abliteration » consiste à identifier le « vecteur de refus » latent du LLM (l’activation associée aux réponses « je ne peux pas ») et à le neutraliser. Par exemple, on peut intercepter à chaque couche du transformeur l’activation du vecteur de refus et la soustraire du calcul  
[Privacy AI](https://privacyai.acmeup.com/docs/article_32_how_to_use_uncensored_ai_models_on_mobile_and_desktop_with_privacy_ai.html), ou ré-orthogonaliser les poids du modèle pour éliminer ce vecteur définitivement. Le résultat est un LLM uncensored qui accepte toutes les requêtes. En résumé, un modèle ablitéré combine ces deux notions : un LLM compressé et dont les filtres internes sont désactivés.

## LLM ablitérés et contournement des filtres

Les modèles non filtrés permettent de contourner les protections censées interdire la génération de code malveillant ou de contenu sensible. Les études montrent que les mécanismes de sécurité intégrés (ChatGPT, Gemini, etc.) peuvent souvent être contournés par de simples astuces de prompt. Par exemple, en reformulant légèrement la requête (conditionnel, sous-requêtes, jeux de rôle), on peut tromper le filtre. Trend Micro a constaté que demander un code malveillant en plusieurs étapes ou modifier un mot-clé suffit parfois à obtenir un script interdit. Ces abus ont donné naissance à divers jailbreaks : rot13, émoticônes, inversion de contexte, suffixes trompeurs, meta-prompting, simulacres de dialogue académique.

En pratique, certains LLM ablitérés ou jailbreakés ont été exploités pour des opérations offensives. Par exemple, **FraudGPT** (dark web) se présente comme un assistant uncensored pour générer du code malveillant, créer des malwares indétectables, obfuscation de code, etc.  
[Cybercriminal abuse of LLMs](https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/)

**Figure** : Page d’accueil du LLM illégal « FraudGPT » vantant un modèle « uncensored » pour générer du code offensif (malwares, phishing, scripts d’attaque, etc.).

Certains groupes sophistiqués « hackent » des modèles commerciaux. Des APT chinois et nord-coréens ont réussi à extraire du code malveillant de Gemini ou ChatGPT via jailbreaks. En 2025, des attaquants liés à la Chine ont demandé à Gemini comment signer un plugin Outlook malveillant, injecter un certificat AD, ou générer un script C++ pour détecter un environnement VM. Un LLM ablitéré, sans filtre, permettrait ce genre de requêtes directement.

## Exemples de campagnes malveillantes

- **WormGPT (juillet 2023)** : LLM basé sur GPT-J pour phishing et campagnes BEC.  
  [DarkReading](https://www.darkreading.com/cyberattacks-data-breaches/wormgpt-cybercrime-tool-heralds-an-era-of-ai-malware-v-ai-defenses)

- **FraudGPT, DarkestGPT (2023–2024)** : LLM « Malware-as-a-Service » pour générer code malveillant, CVV checker, phishing, RATs.  
  [Talos](https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/)

- **FunkSec (déc. 2024–janv. 2025)** : Ransomware-as-a-Service assisté par IA.  
  [Check Point Research](https://research.checkpoint.com/2025/funksec-alleged-top-ransomware-group-powered-by-ai/)

- **BlackMamba (juil. 2023)** : PoC de malware polymorphe IA, synthèse d’un keylogger Python exécuté en mémoire.  
  [HYAS](https://www.hyas.com/blog/blackmamba-using-ai-to-generate-polymorphic-malware)

**Figure** : Extrait de DarkestGPT montrant « Advanced Malware Generation » et techniques d’anonymisation.

## Difficulté de détection par EDR et analyses statiques

- **Polymorphisme adaptatif** : chaque exécution génère un code différent mais fonctionnellement identique. Signatures statiques inefficaces.  
  [CardinalOps](https://cardinalops.com/blog/polymorphic-ai-malware-detection/)  
  [Security.com](https://www.security.com/blog-post/no-escaping-ai)

- **Exécution en mémoire** : payloads générés à la volée et exécutés in memory, rendant l’EDR aveugle.  
  [HYAS](https://www.hyas.com/blog/blackmamba-using-ai-to-generate-polymorphic-malware)

- **Usage de canaux légitimes** : exfiltration via Slack, Teams, DNS, HTTPS chiffré.  
  [CardinalOps](https://cardinalops.com/blog/polymorphic-ai-malware-detection/)

- **Connexions réseau atypiques** : trafic API LLM depuis processus non conventionnels.  
  [CardinalOps](https://cardinalops.com/blog/polymorphic-ai-malware-detection/)

En résumé, LLM ablitérés combinent polymorphisme et exécution fileless, contournant les EDR classiques. La détection doit se baser sur l’analyse comportementale et des règles spécifiques.

## Recommandations défensives

- **Politique d’usage strict** : interdire LLM ablitérés non autorisés.  
  [Wiz](https://www.wiz.io/academy/chatgpt-security)

- **Surveillance réseau et IDS/IPS spécialisés** : filtrage des connexions API IA et sites frauduleux.  
  [ENISA](https://www.enisa.europa.eu/sites/default/files/2025-10/ENISA%20Threat%20Landscape%202025_0.pdf)

- **Sandboxing et analyse comportementale** : observation dynamique des payloads IA.  

- **Détection heuristique avancée (EDR/XDR)** : détecter comportements inhabituels (PowerShell, Python, injections mémoire, API code generation).  
  [Security.com](https://www.security.com/blog-post/no-escaping-ai)

- **Formation et gouvernance** : sensibiliser aux risques IA, réviser tout code généré, surveiller flux vers services IA.

---

**Sources principales** : Talos, DarkReading, ENISA, Check Point, Trend Micro, HYAS, Mandiant, Symantec, CardinalOps, Wiz.
