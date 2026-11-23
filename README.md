# CHIFFON Talent — prototype de présélection RH

Application statique et responsive pour GitHub Pages, personnalisée aux couleurs de CHIFFON.

**Ce dépôt est un prototype local de démonstration, pas un ATS collaboratif prêt pour les CV réels de l'entreprise.** Aucun CV réel n'est fourni dans le dépôt. Les 15 profils fournis proviennent de l'annexe 2 de l'exercice et sont présentés comme des exemples fictifs.

## 1. Déploiement sur GitHub Pages (sans terminal)

1. Connectez-vous à GitHub et créez un dépôt, par exemple `chiffon-talent`. Choisissez un dépôt **public** si nécessaire pour votre formule GitHub Pages. **Ne téléversez jamais de vrais CV ni de données de candidats sur ce dépôt.**
2. Décompressez `chiffon-talent-github.zip` sur votre ordinateur.
3. Dans le dépôt GitHub, cliquez sur **Add file → Upload files** et déposez le contenu du dossier `chiffon-talent` à la racine du dépôt (en conservant `assets/` et `data/`). Validez le commit.
4. Ouvrez **Settings → Pages**, sélectionnez **Deploy from a branch**, la branche `main`, puis le répertoire `/ (root)` ; sauvegardez.
5. L'application sera disponible à `https://VOTRE_COMPTE.github.io/chiffon-talent/` une fois le déploiement effectué.
6. Ouvrez **Tableau de bord → Charger les 15 profils de l’annexe** pour initialiser les données de démonstration. Ces profils proviennent de `data/demo.json`, et **aucun véritable CV n'est présent dans ce JSON**.

Vous pouvez également ouvrir `index.html` localement pour voir l'interface, mais le bouton de chargement des profils de démonstration peut nécessiter un serveur HTTP en raison des protections du navigateur autour de `fetch` en `file://`.

## 2. Fonctionnalités

- Accueil : indicateurs et accès rapide aux actions.
- Postes : création, édition et suppression ; compétences essentielles/complémentaires, critères indispensables à vérifier et pondération modifiable.
- Import de CV : PDF textuel, DOCX et TXT, un ou plusieurs fichiers (10 Mo maximum par fichier).
- Stockage local : texte et fichier original dans **IndexedDB**, **uniquement dans le navigateur et sur l'appareil utilisés**.
- Analyse : détection de synonymes simples, expérience explicitement indiquée, mentions de formation et indices de réalisation. Score pondéré de 0 à 100 et motifs explicites.
- Orientations : « Entretien conseillé », « À approfondir », « Non prioritaire » ; « Vérification indispensable » si un critère essentiel paramétré n'est pas trouvé.
- Entretien : questions contextualisées et grille sur cinq critères notés de 1 à 5 ; compte rendu local.
- Export : tableau des candidatures en **CSV compatible Excel** ; fiche imprimable via navigateur, au besoin enregistrée en PDF ; export de la configuration des postes en JSON.
- Suppression des candidats par dossier, suppression globale et nettoyage des CV importés au prochain lancement si leur date d'ajout a plus de **365 jours**.

## 3. Limitations majeures et sécurité

**Hébergement :** GitHub Pages héberge seulement l'interface, ses scripts et les profils de démonstration. **Les CV importés ne sont pas envoyés à GitHub** par cette application.

**Collaboration :** l'application ne propose **aucune connexion utilisateur**, **aucun partage entre RRH**, **aucune sauvegarde distante** et **aucun contrôle d'accès** aux CV stockés dans le navigateur. Un autre appareil ou navigateur n'accède pas aux données, et une personne ayant accès à la même session de navigateur pourrait les consulter. Les données peuvent disparaître si l'utilisateur efface les données du site ou change de poste.

**Conservation :** la purge après 365 jours s'effectue **à la prochaine ouverture du site** ; elle n'est pas exécutée par un serveur à échéance fixe. Ce mécanisme ne suffit pas à garantir une politique de conservation réglementaire, ni à assurer la suppression de toutes les sauvegardes. La conservation dépend de la finalité du traitement, de l'information des candidats et de l'analyse des durées applicables.

**IA :** il n'y a **pas d'appel à un LLM**. Le prototype utilise des règles et un dictionnaire de termes ; les questions sont générées à partir de modèles. Les scores ne sont ni une probabilité de réussite ni une mesure de valeur du candidat. Des mentions textuelles peuvent être détectées malgré une négation et des compétences réelles peuvent être absentes de l'analyse. **Une relecture humaine est impérative.**

**Fichiers :** l'extraction PDF utilise PDF.js, et DOCX utilise Mammoth, chargés via des CDN tiers (connexions réseau vers les ressources JavaScript). Le contenu des CV est traité localement par ces bibliothèques dans le navigateur ; les PDF scannés ne sont pas reconnus (pas d'OCR). Si les ressources CDN sont bloquées, l'import TXT reste possible. Le code JavaScript est chargé auprès de fournisseurs externes : avant tout déploiement professionnel, verrouiller et auto-héberger les bibliothèques validées par la DSI.

**Sécurité RH :** pour la production, il faut au minimum authentification forte, droits par rôle, stockage privé chiffré avec hébergement adapté, sauvegardes, journal d'accès, suppression automatique serveur, contrat de sous-traitance, politique de protection des données, revue DPO et revue de sécurité. Ne publiez **jamais** de CV réels dans `data/` ou dans l'historique du dépôt.

**Conformité :** un outil d'aide au recrutement peut relever des exigences de protection des données et de l'AI Act. Ne prenez aucune décision d'exclusion automatique. Les critères doivent être directement nécessaires à l'emploi concerné. Informez les candidats des traitements, et étudiez la nécessité d'une AIPD avec votre DPO.

## 4. Passage à une version collaborative sécurisée

Architecture cible conseillée, **non intégrée dans ce prototype** :

- GitHub Pages : front-end public sans clés privées.
- Fournisseur d'identité d'entreprise : accès RH nominatif, MFA.
- Base PostgreSQL et stockage objets privés hébergés dans l'UE : CV et métadonnées, restrictions d'accès strictes.
- API backend/Edge Functions : traitement sécurisé des CV et appels éventuels à un modèle d'IA, sans clé secrète dans le navigateur.
- Tâche planifiée côté serveur : purge effective à l'échéance, avec traitement des sauvegardes et éventuels archivages distincts.
- Audit : traçabilité des décisions humaines et indicateurs de biais / qualité du modèle.

Ce passage exige configuration d'une infrastructure et validation juridique/SSI. Il ne peut pas être réalisé par le seul dépôt de fichiers statiques sur GitHub Pages.

## 5. Arborescence

```text
chiffon-talent/
├── index.html           # Interface
├── styles.css           # Charte graphique responsive
├── app.js               # Logique locale et moteur de scoring
├── data/
│   └── demo.json        # 15 profils fictifs de l'annexe 2
├── assets/
│   └── logo.png         # Logo transmis
├── .nojekyll
└── README.md
```

## 6. Personnalisation de l'analyse

Dans **Postes & critères → Modifier**, ajustez les compétences et les coefficients. Les cinq coefficients sont normalisés automatiquement sur 100, même si leur somme est différente de 100. La détection de synonymes est définie dans `vocab` au début de `app.js` ; modifier ce dictionnaire pour enrichir les compétences reconnues. Le résultat n'est pas calibré scientifiquement et doit être validé avec des cas réels avant tout usage décisionnel.

## 7. Contexte

Projet pédagogique CHIFFON : entreprise d'environ 450 salariés, transformation de la fonction RH, tri manuel des CV et entretiens jusque-là peu structurés. Les quinze descriptions fictives sont issues de l'annexe « Candidatures pour recrutement éventuel service RH ».
