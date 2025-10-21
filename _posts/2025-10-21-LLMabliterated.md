# Modèles LLM « ablités » en cyber-offensive : risques et cas d’usage

Les modèles ablitérés (abréviation de « abliterated », c’est‐à‐dire « allégés » ou « uncensored ») sont des modèles de langage (LLM) dont on a supprimé les garde-fous et parfois compressés pour tourner localement. En pratique, on crée de tels modèles en désactivant les filtres de sécurité et en appliquant des méthodes de compression (quantification, élagage, distillation, etc.) pour réduire leur taille. La compression de modèle vise à diminuer le nombre de paramètres ou leur précision afin de faciliter le déploiement (par ex. sur des machines à ressources limitées). Les techniques principales sont :

- **Quantification** : réduire la précision des poids (p. ex. de 32 bits flottant à 8 bits), ce qui réduit l’empreinte mémoire  
  [medium.com](medium.com)

- **Pruning (élagage)** : supprimer des neurones, couches ou connexions jugés redondants  
  [medium.com](medium.com)

- **Distillation** : entraîner un petit modèle (« étudiant ») à imiter un grand modèle (« enseignant »)  
  [medium.com](medium.com)

Ces optimisations réduisent la taille et le coût de calcul des LLM, souvent au prix d’une dégradation minime de la performance. Un modèle ablité peut ainsi être livré sous forme condensée (int8, quantifié 4 bits, pruné, format GGUF/ggml, etc.) et déployé en local, sans solliciter l’API du fournisseur. Toutefois, son désavantage majeur est l’absence totale de filtres de contenus. L’« abliteration » consiste à identifier le « vecteur de refus » latent du LLM (l’activation associée aux réponses « je ne peux pas ») et à le neutraliser. Par exemple, on peut intercepter à chaque couche du transformeur l’activation du vecteur de refus et la soustraire du calcul  
[privacyai.acmeup.com](privacyai.acmeup.com), ou ré-orthogonaliser les poids du modèle pour éliminer ce vecteur définitivement  
[privacyai.acmeup.com](privacyai.acmeup.com)  
[privacyai.acmeup.com](privacyai.acmeup.com). Le résultat est un LLM uncensored qui accepte toutes les requêtes. En résumé, un modèle ablitéré combine ces deux notions : un LLM compressé et dont les filtres internes sont désactivés.

## LLM ablitérés et contournement des filtres

Les modèles non filtrés permettent de contourner les protections censées interdire la génération de code malveillant ou de contenu sensible. Les études montrent que les mécanismes de sécurité intégrés (par exemple ceux de ChatGPT ou Gemini) peuvent souvent être contournés par de simples astuces de prompt  
[trendmicro.com](trendmicro.com)  
[trendmicro.com](trendmicro.com). Par exemple, en reformulant légèrement la requête (employer le conditionnel, diviser la tâche en sous-requêtes, ou en utilisant des jeux de rôle), on peut tromper le filtre. Trend Micro a constaté que demander un code malveillant en plusieurs étapes (« code snippet ») ou modifier un mot-clé suffit parfois à obtenir un script interdit  
[trendmicro.com](trendmicro.com)  
[trendmicro.com](trendmicro.com). Les abus de ce type ont donné naissance à divers jailbreaks : techniques d’obfuscation du prompt (rot13, émoticônes, inversion de contexte, etc.), suffixes trompeurs, simulacres de dialogue académique, meta-prompting, etc. La raison est que, sans filtre, le modèle répondra « à toute commande, même illégale »  
[blog.talosintelligence.com](blog.talosintelligence.com)  
[privacyai.acmeup.com](privacyai.acmeup.com).

En pratique, des menaces ont déjà exploité ou proposé des LLM ablitérés (ou jailbreakés) pour réaliser des opérations offensives. Par exemple, l’LLM FraudGPT (annoncé sur le dark web) se présente comme un assistant “uncensored” permettant de *« Write malicious code », *« Create undetectable malware », « Code obfuscation », etc.  
[blog.talosintelligence.com](blog.talosintelligence.com)  
[blog.talosintelligence.com](blog.talosintelligence.com). De même, DarkestGPT vante sur son site des fonctionnalités telles que la « génération de malwares avancés » (Advanced Malware Generation), la création de RATs, ou des techniques d’anonymisation (cf. figure). Ces modèles sont vendus ou loués à des cybercriminels, promettant de « ne pas avoir de limites, règles ou frontières »  
[blog.talosintelligence.com](blog.talosintelligence.com)  
[blog.talosintelligence.com](blog.talosintelligence.com).

**Figure** : Page d’accueil du LLM illégal « FraudGPT » (source : Talos) vantant un modèle « uncensored » sans restrictions, pour générer du code offensif (malwares, pages de phishing, scripts d’attaque, etc.).

Dans un autre registre, certains groupes très sophistiqués préfèrent « hacker » des modèles commerciaux. Par exemple, des APT chinois (Chollima) et nord-coréens (APT43) ont documenté l’usage de modèles tels que Gemini ou ChatGPT, dont ils ont réussi à extraire du code malveillant via divers jailbreaks  
[cloud.google.com](cloud.google.com)  
[enisa.europa.eu](enisa.europa.eu). Un cas notable : en 2025 des attaquants liés à la Chine ont demandé à Gemini comment signer un plugin Outlook malveillant et l’installer silencieusement dans un domaine compromis, comment injecter un certificat en Active Directory, ou générer un script C++ pour détecter un environnement VM (sandbox evasion)  
[cloud.google.com](cloud.google.com). Bien que Gemini ait des filtres, les attaquants ont exploité des astuces pour obtenir des instructions détaillées. Un LLM ablitéré, sans aucun filtre, permettrait ce genre de requêtes directement.

## Exemples de campagnes malveillantes

Les usages offensifs de LLM (notamment ablitérés) sont encore émergents, mais plusieurs cas réels ou plausibles ont été rapportés :

- **WormGPT (juillet 2023)** : un LLM prétendument conçu pour le cybercrime (basé sur GPT-J ou similaire) a fait l’objet d’une fuite fin 2022. Il visait principalement la création de leurres de phishing et de campagnes BEC. SlashNext et DarkReading ont décrit WormGPT comme le « ChatGPT du Dark Web » pour les criminels, générant du texte et du code sans éthique  
  [darkreading.com](darkreading.com). Les analystes soulignent que de tels outils accélèrent la diffusion de mails ciblés (spear-phishing) et d’attaques à grande échelle, notamment grâce à « la nature polymorphe des attaques à grande vitesse » permise par l’IA  
  [darkreading.com](darkreading.com).

- **FraudGPT, DarkestGPT (2023–2024)** : des LLMs « Malware-as-a-Service » ont été proposés en ligne. Le site de FraudGPT (début 2023) listait de multiples fonctionnalités offensives (génération de code malveillant, CVV checker, pages de phishing, etc.)  
  [blog.talosintelligence.com](blog.talosintelligence.com). DarkestGPT (mi-2024) promettait des RATs et malwares indétectables. Ces offres ont parfois été identifiées comme arnaques (les créateurs ne livrant pas de vrai modèle)  
  [blog.talosintelligence.com](blog.talosintelligence.com), mais elles montrent l’intérêt criminel pour de tels outils.

- **FunkSec (déc. 2024–janv. 2025)** : un groupe de rançongiciel auto-proclamé « powered by AI » a émergé. Check Point Research a analysé FunkSec, un ransomware-as-a-service opéré par des hackers allégant leurs compétences via l’IA  
  [research.checkpoint.com](research.checkpoint.com). Les opérateurs revendiquaient de très nombreux déploiements et affirmaient utiliser des outils assistés par IA pour accélérer le développement du malware. L’enquête a montré que, bien que leur cryptage reste basique, l’équipe (probablement inexpérimentée) s’appuyait sur une génération automatique de code par IA pour itérer rapidement ses versions  
  [research.checkpoint.com](research.checkpoint.com). Ce cas souligne que même un acteur peu qualifié peut créer un ransomware avancé si l’IA est mise à contribution.

- **BlackMamba (juil. 2023)** : un PoC de malware polymorphe généré par IA a été publié par HYAS Labs. BlackMamba exploite un binaire bénin qui contacte l’API GPT d’OpenAI au runtime pour synthétiser un keylogger en Python, qu’il exécute directement en mémoire  
  [hyas.com](hyas.com). À chaque exécution, le code malveillant est régénéré différemment (voir fig. infra), échappant totalement aux signatures statiques. Les tests ont montré que BlackMamba n’a déclenché aucune alerte sur un EDR leader du marché pendant de nombreux essais  
  [hyas.com](hyas.com). Ce démonstrateur illustre la capacité des LLM allégés à produire des malwares « fileless » et polymorphes, indétectables par les défenses classiques.

**Figure** : Extrait de la page « Tools & Potential » de DarkestGPT (source : Talos). Ce LLM malveillant vantait des fonctionnalités offensives (« Advanced Malware Generation », développement de RATs, techniques d’anonymisation, etc.). De tels modèles « uncensored » promettent aux attaquants de générer directement des codes d’attaque ou des payloads complexes (ex. trojans, keyloggers) sans aucune restriction.

En résumé, plusieurs preuves pointent vers l’utilisation de LLM (spécialement dépourvus de filtres) pour scalabiliser la création de menaces : phishing ultra-ciblé, code malveillant automatisé, et organisation d’attaques à grande échelle. Des rapports comme ceux de CrowdStrike ou ENISA confirment l’intérêt croissant des attaquants pour ces outils  
[enisa.europa.eu](enisa.europa.eu)  
[enisa.europa.eu](enisa.europa.eu). Par exemple, ENISA a observé que >80% des mails d’hameçonnage (sept. 2024–fév. 2025) contenaient du contenu généré par IA  
[enisa.europa.eu](enisa.europa.eu). Les groupes étatiques (« China-nexus, Iran-nexus, DPRK-nexus ») utilisent des LLM (jailbreakés ou non) pour automatiser l’ingénierie sociale et accélérer la conception d’outils malveillants  
[enisa.europa.eu](enisa.europa.eu). Des kits « evil-GPT » inspirés de ChatGPT, comme WormGPT, EscapeGPT ou FraudGPT, ont vu le jour sur le Dark Web pour vendre aux criminels cette capacité générationnelle.

## Difficulté de détection par EDR et analyses statiques

Les LLM ablitérés facilitent la création de code hautement polymorphe et furtif, ce qui complique considérablement la détection par EDR classiques. Plusieurs mécanismes contribuent à cette furtivité :

- **Polymorphisme adaptatif** : chaque exécution du malware peut générer du code différent, mais fonctionnellement identique. Ainsi, les signatures basées sur des chaînes ou des heuristiques statiques sont rapidement inefficaces  
  [cardinalops.com](cardinalops.com)  
  [security.com](security.com). Comme l’explique CardinalOps, un malware IA peut « continuellement réécrire sa logique » : bien que la fonctionnalité reste la même, la structure du code change à chaque génération, rendant chaque échantillon nouveau et indétectable par correspondance de signature  
  [cardinalops.com](cardinalops.com). Symantec note également que les attaquants ont désormais des malwares « polymorphes pilotés par IA » qui se réécrivent eux-mêmes pour échapper aux défenses  
  [security.com](security.com).

- **Exécution en mémoire** : les payloads peuvent être synthétisés à la volée par un interpréteur (Python, etc.) et exécutés in memory sans fichier sur disque. Par exemple, BlackMamba compile dynamiquement un keylogger en Python qu’il exécute avec exec()  
  [hyas.com](hyas.com). Dans ce cas, l’EDR ne voit jamais de nouveau fichier à analyser. Même compilé en exécutable autonome, le PoC de CardinalOps n’a déclenché qu’une faible alerte d’entropie (de basse confiance) après deux semaines d’exécution  
  [cardinalops.com](cardinalops.com), tandis que certaines solutions ne l’ont pas du tout détecté. Ainsi, la plupart des solutions EDR légitimes ont du mal à bloquer ces attaques hybrides.

- **Usage de canaux légitimes (living-off-the-land)** : l’IA peut automatiquement exploiter des outils ou services réputés légitimes pour exfiltrer ou cacher ses activités (p. ex. exfiltration via Slack, Teams, DNS, HTTPS chiffré). Dans le cas BlackMamba, le code généré a utilisé une webhook Slack pour sortir les données volées  
  [cardinalops.com](cardinalops.com). D’autres démonstrations proposent des canaux comme Discord ou des signatures de code valides. Ces techniques rendent le comportement du malware difficile à distinguer d’un logiciel autorisé.

- **Connexions réseau atypiques** : un indicateur de compromission clé est l’appel aux API d’IA depuis des processus non conventionnels. CardinalOps recommande de traquer les connexions vers des domaines d’API LLM (openai.com, openai.azure.com, anthropic.com, huggingface.co, etc.) initiées par des processus autres que le navigateur  
  [cardinalops.com](cardinalops.com). En effet, presque tout trafic vers ces API émane normalement de navigateurs web ou d’outils de développement. Des requêtes à l’API GPT depuis un processus inconnu sur un poste sont donc suspectes  
  [cardinalops.com](cardinalops.com). Ce genre de connexion peut servir à obtenir du code malveillant à la volée – un signe fort d’attaque IA. Notons que l’EDR classique, focalisé sur le local, ne détecte pas facilement un tel trafic réseau sortant, qui peut facilement passer sous les radars sans règles dédiées.

En résumé, les malwares issus d’LLM ablitérés combinent polymorphisme poussé et exécution fileless. Ils contournent ou trompent la détection statique et repoussent les EDR vers une analyse purement comportementale. Comme le souligne Broadcom Symantec, ces malwares basés IA « outsmartent les défenses » en réécrivant leur code pour échapper aux signatures  
[security.com](security.com). Il en résulte une fenêtre critique durant laquelle l’attaque est indétectable ou considérée comme faussement bénigne. La détection doit donc se baser sur des anomalies comportementales et des règles spécifiques (surveillance mémoire, abus de processus légitimes, usage de commandes PowerShell, etc.), complétées idéalement par de l’IA défensive.

## Recommandations défensives

Face à cette menace, plusieurs mesures pratiques sont recommandées avant tout déploiement d’LLM et pour la détection des payloads :

- **Politique d’usage strict** : interdire l’installation ou l’utilisation non autorisée de LLM ablitérés sur les réseaux sensibles. De nombreuses entreprises (dont Samsung en 2023) ont déjà banni ChatGPT et autres IA grand public sur leurs postes et terminaux internes  
  [wiz.io](wiz.io), par crainte de fuites de données ou d’introduction de malwares invisibles. Les organisations critiques doivent mettre en place des politiques claires (« zèle de confiance zéro ») : seuls les modèles vérifiés et supervisés (par ex. ChatGPT Enterprise) peuvent être utilisés, avec journalisation de tout prompt, et blocage réseau des modèles suspects.

- **Surveillance réseau et IDS/IPS spécialisés** : mettre en place des règles détectant les connexions inhabituelles vers des API IA. Par exemple, autoriser uniquement certains domaines IA depuis des applications précises (navigateurs) et alerter sinon. Les outils de type NDR (Network Detection and Response) peuvent signaler ces anomalies. De même, détecter les communications avec des sites frauduleux se faisant passer pour des IA (faux installateurs, sites de phishing IA  
  [enisa.europa.eu](enisa.europa.eu)).

- **Sandboxing et analyse comportementale** : toute application ou script inconnu doit être exécuté dans un bac à sable instrumenté. Les payloads IA – polymorphes et fileless – nécessitent une observation de leur comportement dynamique (appel API, chargement en mémoire, modifications systèmes, C2 sorts). Les plateformes de sandbox modernes (VMRay, Cuckoo, etc.) sont essentielles pour analyser ces malwares en sécurité. L’injection de traceurs mémoire ou l’enregistrement des interactions API IA peut révéler la boucle de génération malveillante. Par exemple, le fait qu’un processus executif appelle Azure OpenAI et encode ses instructions entrantes en base64 a été noté comme un comportement suspect.

- **Détection heuristique avancée (EDR/XDR)** : améliorer les règles sur la base de comportements inhabituels plutôt que de signatures fixes. Détecter l’usage d’API de génération de code, l’élévation de privilèges automatique, la création de processus enfants inattendus (PowerShell, Python), les injections mémoire, etc. Certains EDR proposent déjà des modules de protection adaptative (« Adaptive Protection ») capables de modéliser le comportement normal d’un hôte et d’alerter sur toute anomalie (usage anormal de PowerShell, exploitation de canaux légitimes, etc.)  
  [security.com](security.com). Les remédiations centrées IA (ex. algorithmes d’anticipation de la chaîne d’attaque) peuvent aider à repérer ces menaces dynamiques.

- **Formation et gouvernance** : sensibiliser les développeurs et administrateurs à ces risques. Éviter d’entrer des données sensibles (code source, informations privées) dans des LLM ouverts. Utiliser des process de revue pour tout code généré par IA. Maintenir des mises à jour et des correctifs pour les outils AI internes, et monitorer les flux d’activités vers les services IA (sécurité du pipeline d’IA).

En appliquant ces recommandations – interdiction de l’IA non contrôlée, analyse comportementale et sandboxing renforcés, politiques réseau restrictives – une organisation peut limiter le risque que des LLM ablitérés facilitent une attaque interne. Il est impératif de considérer ces modèles comme des nouveaux vecteurs d’attaque plutôt que comme des simples malwares passifs. L’absence de blocage de contenu dans les LLM ablitérés crée un risque inédit : des assistants techniques sous contrôle ennemi, capables de générer et d’adapter instantanément du code malveillant au cœur des systèmes. La lutte passe donc par la surveillance active de tout usage d’IA, l’automatisation de la chasse aux comportements anormaux, et la mise en place de garde-fous organisationnels stricts.

**Sources** : Analyses de Talos, DarkReading, ENISA, Check Point, Trend Micro, HYAS, Mandiant, Symantec, CardinalOps, Wiz (Samsung), etc.  
[blog.talosintelligence.com](blog.talosintelligence.com)  
[enisa.europa.eu](enisa.europa.eu)  
[enisa.europa.eu](enisa.europa.eu)  
[research.checkpoint.com](research.checkpoint.com)  
[cardinalops.com](cardinalops.com)  
[hyas.com](hyas.com)  
[trendmicro.com](trendmicro.com)  
[cardinalops.com](cardinalops.com)

## Citations

- Quantization, Distillation and Pruning | by Sai Chaitanya Pachipulusu | Medium  
  https://medium.com/@chaitanya17.sai/quantization-distillation-and-pruning-437110a322c7

- How to Use Uncensored (Abliterated) AI Models on Mobile and Desktop with "Privacy AI" and "LM Studio" | Privacy AI Documentation  
  https://privacyai.acmeup.com/docs/article_32_how_to_use_uncensored_ai_models_on_mobile_and_desktop_with_privacy_ai.html

- A Closer Look at ChatGPT's Role in Automated Malware Creation | Trend Micro (US)  
  https://www.trendmicro.com/en_us/research/23/k/a-closer-look-at-chatgpt-s-role-in-automated-malware-creation.html

- Cybercriminal abuse of large language models  
  https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/

- Adversarial Misuse of Generative AI | Google Cloud Blog  
  https://cloud.google.com/blog/topics/threat-intelligence/adversarial-misuse-generative-ai

- https://www.enisa.europa.eu/sites/default/files/2025-10/ENISA%20Threat%20Landscape%202025_0.pdf

- WormGPT Cybercrime Tool Heralds an Era of AI Malware vs. AI Defenses  
  https://www.darkreading.com/cyberattacks-data-breaches/wormgpt-cybercrime-tool-heralds-an-era-of-ai-malware-v-ai-defenses

- FunkSec – Alleged Top Ransomware Group Powered by AI - Check Point Research  
  https://research.checkpoint.com/2025/funksec-alleged-top-ransomware-group-powered-by-ai/

- BlackMamba: Using AI to Generate Polymorphic Malware  
  https://www.hyas.com/blog/blackmamba-using-ai-to-generate-polymorphic-malware

- Polymorphic AI Malware: A Real-World POC and Detection Walkthrough - CardinalOps  
  https://cardinalops.com/blog/polymorphic-ai-malware-detection/

- There Is No Escaping AI | SECURITY.COM  
  https://www.security.com/blog-post/no-escaping-ai

- ChatGPT Security for Enterprises: Risks and Best Practices | Wiz  
  https://www.wiz.io/academy/chatgpt-security
