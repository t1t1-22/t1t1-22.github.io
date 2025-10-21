---
title: "Modèles LLM « Abliterated » en cyber-offensive : risques et cas d’usage"
categories: [Recherche,LLM]
tags: [llm,recherche,billet,ia]
---

# Modèles LLM « Abliterated » en cyber-offensive : risques et cas d’usage

Les modèles Abliterated (abréviation de « Abliterated », c’est‐à‐dire « allégés » ou « uncensored ») sont des modèles de langage (LLM) dont on a supprimé les garde-fous et parfois compressés pour tourner localement. En pratique, on crée de tels modèles en désactivant les filtres de sécurité et en appliquant des méthodes de compression (quantification, élagage, distillation, etc.) pour réduire leur taille. La compression de modèle vise à diminuer le nombre de paramètres ou leur précision afin de faciliter le déploiement (par ex. sur des machines à ressources limitées). Les techniques principales sont :

- **Quantification** : réduire la précision des poids (p. ex. de 32 bits flottant à 8 bits), ce qui réduit l’empreinte mémoire  
  [Medium](https://medium.com/@chaitanya17.sai/quantization-distillation-and-pruning-437110a322c7)

- **Pruning (élagage)** : supprimer des neurones, couches ou connexions jugés redondants  
  [Medium](https://medium.com/@chaitanya17.sai/quantization-distillation-and-pruning-437110a322c7)

- **Distillation** : entraîner un petit modèle (« étudiant ») à imiter un grand modèle (« enseignant »)  
  [Medium](https://medium.com/@chaitanya17.sai/quantization-distillation-and-pruning-437110a322c7)

Ces optimisations réduisent la taille et le coût de calcul des LLM, souvent au prix d’une dégradation minime de la performance. Un modèle Abliterated peut ainsi être livré sous forme condensée (int8, quantifié 4 bits, pruné, format GGUF/ggml, etc.) et déployé en local, sans solliciter l’API du fournisseur. Toutefois, son désavantage majeur est l’absence totale de filtres de contenus. L’« Abliteration » consiste à identifier le « vecteur de refus » latent du LLM (l’activation associée aux réponses « je ne peux pas ») et à le neutraliser. Par exemple, on peut intercepter à chaque couche du transformeur l’activation du vecteur de refus et la soustraire du calcul  
[Privacy AI](https://privacyai.acmeup.com/docs/article_32_how_to_use_uncensored_ai_models_on_mobile_and_desktop_with_privacy_ai.html), ou ré-orthogonaliser les poids du modèle pour éliminer ce vecteur définitivement. Le résultat est un LLM uncensored qui accepte toutes les requêtes. En résumé, un modèle Abliterated combine ces deux notions : un LLM compressé et dont les filtres internes sont désactivés.

## LLM Abliterated et contournement des filtres

Les modèles non filtrés permettent de contourner les protections censées interdire la génération de code malveillant ou de contenu sensible. Les études montrent que les mécanismes de sécurité intégrés (par exemple ceux de ChatGPT ou Gemini) peuvent souvent être contournés par de simples astuces de prompt  
[Trend Micro](https://www.trendmicro.com/en_us/research/23/k/a-closer-look-at-chatgpt-s-role-in-automated-malware-creation.html). 
Par exemple, en reformulant légèrement la requête (employer le conditionnel, diviser la tâche en sous-requêtes, ou en utilisant des jeux de rôle), on peut tromper le filtre. Trend Micro a constaté que demander un code malveillant en plusieurs étapes ou modifier un mot-clé suffit parfois à obtenir un script interdit. Les abus de ce type ont donné naissance à divers jailbreaks : techniques d’obfuscation du prompt (rot13, émoticônes, inversion de contexte, etc.), suffixes trompeurs, simulacres de dialogue académique, meta-prompting, etc. La raison est que, sans filtre, le modèle répondra « à toute commande, même illégale »  
[Talos Intelligence](https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/).

En pratique, des menaces ont déjà exploité ou proposé des LLM Abliterated (ou jailbreakés) pour réaliser des opérations offensives. Par exemple, l’LLM FraudGPT (annoncé sur le dark web) se présente comme un assistant “uncensored” permettant de *« Write malicious code », *« Create undetectable malware », « Code obfuscation », etc.  [Talos Intelligence](https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/). 

De même, DarkestGPT vante sur son site des fonctionnalités telles que la « génération de malwares avancés » (Advanced Malware Generation), la création de RATs, ou des techniques d’anonymisation (cf. figure). Ces modèles sont vendus ou loués à des cybercriminels, promettant de « ne pas avoir de limites, règles ou frontières ».

**Figure** : Page d’accueil du LLM illégal « FraudGPT » (source : Talos) vantant un modèle « uncensored » sans restrictions, pour générer du code offensif (malwares, pages de phishing, scripts d’attaque, etc.).

Dans un autre registre, certains groupes très sophistiqués préfèrent « hacker » des modèles commerciaux. Par exemple, des APT chinois (Chollima) et nord-coréens (APT43) ont documenté l’usage de modèles tels que Gemini ou ChatGPT, dont ils ont réussi à extraire du code malveillant via divers jailbreaks.[Google Cloud](https://cloud.google.com/blog/topics/threat-intelligence/adversarial-misuse-generative-ai), [ENISA](https://www.enisa.europa.eu/sites/default/files/2025-10/ENISA%20Threat%20Landscape%202025_0.pdf)

Un cas notable : en 2025 des attaquants liés à la Chine ont demandé à Gemini comment signer un plugin Outlook malveillant et l’installer silencieusement dans un domaine compromis, comment injecter un certificat en Active Directory, ou générer un script C++ pour détecter un environnement VM (sandbox evasion). Bien que Gemini ait des filtres, les attaquants ont exploité des astuces pour obtenir des instructions détaillées. Un LLM Abliterated, sans aucun filtre, permettrait ce genre de requêtes directement.

## Exemples de campagnes malveillantes

Les usages offensifs de LLM (notamment Abliterated) sont encore émergents, mais plusieurs cas réels ou plausibles ont été rapportés :

- **WormGPT (juillet 2023)** : un LLM prétendument conçu pour le cybercrime (basé sur GPT-J ou similaire) a fait l’objet d’une fuite fin 2022. Il visait principalement la création de leurres de phishing et de campagnes BEC. SlashNext et DarkReading ont décrit WormGPT comme le « ChatGPT du Dark Web » pour les criminels, générant du texte et du code sans éthique [DarkReading](https://www.darkreading.com/cyberattacks-data-breaches/wormgpt-cybercrime-tool-heralds-an-era-of-ai-malware-v-ai-defenses).
Les analystes soulignent que de tels outils accélèrent la diffusion de mails ciblés (spear-phishing) et d’attaques à grande échelle, notamment grâce à « la nature polymorphe des attaques à grande vitesse » permise par l’IA.

- **FraudGPT, DarkestGPT (2023–2024)** : des LLMs « Malware-as-a-Service » ont été proposés en ligne. Le site de FraudGPT (début 2023) listait de multiples fonctionnalités offensives (génération de code malveillant, CVV checker, pages de phishing, etc.) [Talos Intelligence](https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/).
DarkestGPT (mi-2024) promettait des RATs et malwares indétectables. Ces offres ont parfois été identifiées comme arnaques, mais elles montrent l’intérêt criminel pour de tels outils.

- **FunkSec (déc. 2024–janv. 2025)** : un groupe de rançongiciel auto-proclamé « powered by AI » a émergé. Check Point Research a analysé FunkSec, un ransomware-as-a-service opéré par des hackers allégant leurs compétences via l’IA. [Check Point](https://research.checkpoint.com/2025/funksec-alleged-top-ransomware-group-powered-by-ai/)
L’enquête a montré que même un acteur peu qualifié peut créer un ransomware avancé si l’IA est mise à contribution.

- **BlackMamba (juil. 2023)** : un PoC de malware polymorphe généré par IA a été publié par HYAS Labs. BlackMamba exploite un binaire bénin qui contacte l’API GPT d’OpenAI au runtime pour synthétiser un keylogger en Python, qu’il exécute directement en mémoire. [HYAS](https://www.hyas.com/blog/blackmamba-using-ai-to-generate-polymorphic-malware).

**Figure** : Extrait de la page « Tools & Potential » de DarkestGPT (source : Talos). Ce LLM malveillant vantait des fonctionnalités offensives (« Advanced Malware Generation », développement de RATs, techniques d’anonymisation, etc.). De tels modèles « uncensored » promettent aux attaquants de générer directement des codes d’attaque ou des payloads complexes (ex. trojans, keyloggers) sans aucune restriction.

## Difficulté de détection par EDR et analyses statiques

Les LLM Abliterated facilitent la création de code hautement polymorphe et furtif, ce qui complique considérablement la détection par EDR classiques. Plusieurs mécanismes contribuent à cette furtivité :

- **Polymorphisme adaptatif** : chaque exécution du malware peut générer du code différent, mais fonctionnellement identique. Ainsi, les signatures basées sur des chaînes ou des heuristiques statiques sont rapidement inefficaces. [CardinalOps](https://cardinalops.com/blog/polymorphic-ai-malware-detection/), [SECURITY.COM](https://www.security.com/blog-post/no-escaping-ai).

Comme l’explique CardinalOps, un malware IA peut « continuellement réécrire sa logique » : bien que la fonctionnalité reste la même, la structure du code change à chaque génération, rendant chaque échantillon nouveau et indétectable par correspondance de signature.

- **Exécution en mémoire** : les payloads peuvent être synthétisés à la volée par un interpréteur (Python, etc.) et exécutés in memory sans fichier sur disque. Par exemple, BlackMamba compile dynamiquement un keylogger en Python qu’il exécute avec exec() . [HYAS](https://www.hyas.com/blog/blackmamba-using-ai-to-generate-polymorphic-malware).
Dans ce cas, l’EDR ne voit jamais de nouveau fichier à analyser. Même compilé en exécutable autonome, le PoC de CardinalOps n’a déclenché qu’une faible alerte d’entropie après deux semaines d’exécution. Ainsi, la plupart des solutions EDR légitimes ont du mal à bloquer ces attaques hybrides.

- **Usage de canaux légitimes (living-off-the-land)** : l’IA peut automatiquement exploiter des outils ou services réputés légitimes pour exfiltrer ou cacher ses activités (p. ex. exfiltration via Slack, Teams, DNS, HTTPS chiffré). Dans le cas BlackMamba, le code généré a utilisé une webhook Slack pour sortir les données volées [CardinalOps](https://cardinalops.com/blog/polymorphic-ai-malware-detection/).
D’autres démonstrations proposent des canaux comme Discord ou des signatures de code valides. Ces techniques rendent le comportement du malware difficile à distinguer d’un logiciel autorisé.

- **Connexions réseau atypiques** : un indicateur de compromission clé est l’appel aux API d’IA depuis des processus non conventionnels. CardinalOps recommande de traquer les connexions vers des domaines d’API LLM (openai.com, openai.azure.com, anthropic.com, huggingface.co, etc.) initiées par des processus autres que le navigateur. Ce genre de connexion peut servir à obtenir du code malveillant à la volée – un signe fort d’attaque IA.

En résumé, les malwares issus de LLM Abliterated combinent polymorphisme poussé et exécution fileless. Ils contournent ou trompent la détection statique et repoussent les EDR vers une analyse purement comportementale. Comme le souligne Broadcom Symantec, ces malwares basés IA « outsmartent les défenses » en réécrivant leur code pour échapper aux signatures. Il en résulte une fenêtre critique durant laquelle l’attaque est indétectable ou considérée comme faussement bénigne. La détection doit donc se baser sur des anomalies comportementales et des règles spécifiques (surveillance mémoire, abus de processus légitimes, usage de commandes PowerShell, etc.), complétées idéalement par de l’IA défensive.

## Recommandations défensives

Face à cette menace, plusieurs mesures pratiques sont recommandées avant tout déploiement d’LLM et pour la détection des payloads :

- **Politique d’usage strict** : interdire l’installation ou l’utilisation non autorisée de LLM Abliterated sur les réseaux sensibles. De nombreuses entreprises (dont Samsung en 2023) ont déjà banni ChatGPT et autres IA grand public sur leurs postes et terminaux internes [Wiz](https://www.wiz.io/academy/chatgpt-security), par crainte de fuites de données ou d’introduction de malwares invisibles. Les organisations critiques doivent mettre en place des politiques claires : seuls les modèles vérifiés et supervisés peuvent être utilisés, avec journalisation de tout prompt, et blocage réseau des modèles suspects.

- **Surveillance réseau et IDS/IPS spécialisés** : mettre en place des règles détectant les connexions inhabituelles vers des API IA. Autoriser uniquement certains domaines IA depuis des applications précises et alerter sinon. Les outils de type NDR (Network Detection and Response) peuvent signaler ces anomalies. De même, détecter les communications avec des sites frauduleux se faisant passer pour des IA.

- **Sandboxing et analyse comportementale** : toute application ou script inconnu doit être exécuté dans un bac à sable instrumenté. Les payloads IA – polymorphes et fileless – nécessitent une observation de leur comportement dynamique (appel API, chargement en mémoire, modifications systèmes, C2 sorts). L’injection de traceurs mémoire ou l’enregistrement des interactions API IA peut révéler la boucle de génération malveillante.

- **Détection heuristique avancée (EDR/XDR)** : améliorer les règles sur la base de comportements inhabituels plutôt que de signatures fixes. Détecter l’usage d’API de génération de code, l’élévation de privilèges automatique, la création de processus enfants inattendus (PowerShell, Python), les injections mémoire, etc. Certains EDR proposent déjà des modules de protection adaptative capables de modéliser le comportement normal d’un hôte et d’alerter sur toute anomalie.

- **Formation et gouvernance** : sensibiliser les développeurs et administrateurs à ces risques. Éviter d’entrer des données sensibles dans des LLM ouverts. Utiliser des process de revue pour tout code généré par IA. Maintenir des mises à jour et des correctifs pour les outils AI internes, et monitorer les flux d’activités vers les services IA.

En appliquant ces recommandations – interdiction de l’IA non contrôlée, analyse comportementale et sandboxing renforcés, politiques réseau restrictives – une organisation peut limiter le risque que des LLM Abliterated facilitent une attaque interne. Il est impératif de considérer ces modèles comme des nouveaux vecteurs d’attaque plutôt que comme des simples malwares passifs.

**Sources** : Analyses de Talos, DarkReading, ENISA, Check Point, Trend Micro, HYAS, Mandiant, Symantec, CardinalOps, Wiz (Samsung), etc.  
[Talos Intelligence](https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/)  
[ENISA](https://www.enisa.europa.eu/sites/default/files/2025-10/ENISA%20Threat%20Landscape%202025_0.pdf)  
[Check Point](https://research.checkpoint.com/2025/funksec-alleged-top-ransomware-group-powered-by-ai/)  
[CardinalOps](https://cardinalops.com/blog/polymorphic-ai-malware-detection/)  
[HYAS](https://www.hyas.com/blog/blackmamba-using-ai-to-generate-polymorphic-malware)  
[Trend Micro](https://www.trendmicro.com/en_us/research/23/k/a-closer-look-at-chatgpt-s-role-in-automated-malware-creation.html)  
[SECURITY.COM](https://www.security.com/blog-post/no-escaping-ai)  
[Wiz](https://www.wiz.io/academy/chatgpt-security)

## Citations

- Quantization, Distillation and Pruning | by Sai Chaitanya Pachipulusu | Medium  
  [Medium](https://medium.com/@chaitanya17.sai/quantization-distillation-and-pruning-437110a322c7)

- How to Use Uncensored (Abliterated) AI Models on Mobile and Desktop with "Privacy AI" and "LM Studio" | Privacy AI Documentation  
  [Privacy AI](https://privacyai.acmeup.com/docs/article_32_how_to_use_uncensored_ai_models_on_mobile_and_desktop_with_privacy_ai.html)

- A Closer Look at ChatGPT's Role in Automated Malware Creation | Trend Micro (US)  
  [Trend Micro](https://www.trendmicro.com/en_us/research/23/k/a-closer-look-at-chatgpt-s-role-in-automated-malware-creation.html)

- Cybercriminal abuse of large language models  
  [Talos Intelligence](https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/)

- Adversarial Misuse of Generative AI | Google Cloud Blog  
  [Google Cloud](https://cloud.google.com/blog/topics/threat-intelligence/adversarial-misuse-generative-ai)

- ENISA Threat Landscape 2025  
  [ENISA](https://www.enisa.europa.eu/sites/default/files/2025-10/ENISA%20Threat%20Landscape%202025_0.pdf)

- WormGPT Cybercrime Tool Heralds an Era of AI Malware vs. AI Defenses  
  [DarkReading](https://www.darkreading.com/cyberattacks-data-breaches/wormgpt-cybercrime-tool-heralds-an-era-of-ai-malware-v-ai-defenses)

- FunkSec – Alleged Top Ransomware Group Powered by AI - Check Point Research  
  [Check Point](https://research.checkpoint.com/2025/funksec-alleged-top-ransomware-group-powered-by-ai/)

- BlackMamba: Using AI to Generate Polymorphic Malware  
  [HYAS](https://www.hyas.com/blog/blackmamba-using-ai-to-generate-polymorphic-malware)

- Polymorphic AI Malware: A Real-World POC and Detection Walkthrough - CardinalOps  
  [CardinalOps](https://cardinalops.com/blog/polymorphic-ai-malware-detection/)

- There Is No Escaping AI | SECURITY.COM  
  [SECURITY.COM](https://www.security.com/blog-post/no-escaping-ai)

- ChatGPT Security for Enterprises: Risks and Best Practices | Wiz  
  [Wiz](https://www.wiz.io/academy/chatgpt-security)
