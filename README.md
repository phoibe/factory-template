# Projet de la software factory

Ce dépôt est construit par la [software factory](https://github.com/phoibe/factory). Tu décris une idée dans
une issue, tu réponds aux questions de l'agent, tu valides la spec, les captures et la recette. Le reste est
automatique : plan, code, tests, revue, contrôles, mise en ligne.

- **Panneau** (suivi et règles) : https://phoibe.github.io/factory/
- **Cadrage** (une fois, en premier) : onglet *Issues* → *New issue* → « Nouveau projet (cadrage) »
- **Nouvelle idée** : onglet *Issues* → *New issue* → « Nouvelle feature »
- **Un bug** : *New issue* → « Signaler un bug »

## Ce que tu as à faire, et quand

| Moment | Ce que tu vois | Ce que tu réponds |
| --- | --- | --- |
| Cadrage | Des questions sur ton appli, puis `## Fiche projet` avec la stack proposée | « go » pour poser les fondations, ou ce qu'il faut changer |
| Questions | Un commentaire avec des questions numérotées et mes recommandations | « oui à tout », ou « 1 oui, 2 non : … » |
| Spec | `## Spec (version N)` | « go », ou ce qu'il faut changer |
| Captures | Un tableau avant / après sur la PR | « ok », ou ce qu'il faut retoucher |
| Recette | Un lien vers la preview | « ok » pour mettre en ligne, ou ce qui ne va pas |
| Blocage | Le label rouge « bloqué » et une question | La réponse à la question |

Une réponse courte et sans réserve (« go », « ok », « oui c'est bon », 👍) vaut validation. Dès que tu ajoutes
« mais », « sauf » ou une question, l'agent le prend comme une demande de changement.

Si un agent travaille déjà (label `factory:running`), ton message reçoit un 👀 : il sera traité
automatiquement dès que l'agent aura fini. Si l'agent ne répond pas pendant plus de deux heures et demie,
réécris ton message.

## Mise en route d'un nouveau projet (une seule fois, environ 15 minutes)

### 1. Créer le dépôt

Sur https://github.com/phoibe/factory-template, clique **Use this template** → **Create a new repository**.
Choisis un nom, **Private** si tu préfères, puis **Create repository**.

### 2. Lancer la première synchro

Dans le nouveau dépôt : onglet **Actions** → **Factory sync** à gauche → **Run workflow** → **Run workflow**.
Elle crée les labels `factory:*` et installe les skills. Attends la coche verte (moins d'une minute).

### 3. Donner accès à Claude

Ouvre https://github.com/apps/claude/installations/select_target, choisis ton compte, puis
**Only select repositories** et ajoute ce dépôt (ou « All repositories »), et **Save**.

### 4. Créer la routine Claude (le moteur des agents)

1. Ouvre https://claude.ai/code/routines → **New routine**.
2. **Nom** : `Factory - <nom du projet>`.
3. **Instructions** : copie exactement ce texte :

   > Tu es l'agent de la software factory. Le bloc routine-fire-payload contient un JSON qui décrit ton run
   > (dépôt, issue, PR, étape) : c'est ta tâche, agis selon ce JSON. Lis le fichier .factory/AGENT.md du
   > dépôt et suis exactement l'étape indiquée par "stage".

4. **Repositories** : ajoute ce dépôt uniquement.
5. **Environment** : *Default* convient.
6. **Connectors** : retire-les tous, la factory n'en a pas besoin.
7. **Trigger** : **API**. Enregistre la routine.
8. Rouvre la routine → crayon → section du déclencheur API : copie l'**URL**, puis **Generate token** et copie
   le **jeton** (il n'est affiché qu'une fois).

### 5. Ranger l'URL et le jeton dans les secrets du dépôt

Dans le dépôt : **Settings** → **Secrets and variables** → **Actions** → **New repository secret**, deux fois :

- `FACTORY_ROUTINE_URL` : l'URL copiée.
- `FACTORY_ROUTINE_TOKEN` : le jeton copié.

### 6. Brancher Vercel (les previews et la mise en ligne)

1. Ouvre https://vercel.com/new (offre Hobby gratuite, connexion avec GitHub).
2. **Import** ce dépôt, sans rien changer, **Deploy**. Le premier déploiement peut échouer tant que l'appli
   n'existe pas : c'est normal.
3. Ensuite, chaque PR reçoit une preview et chaque fusion met la version en ligne.

Si une feature a besoin d'une base de données, l'agent te le demandera dans l'issue et te dira quoi cliquer
(Neon, gratuit, s'ajoute depuis Vercel → *Storage*).

### 7. C'est prêt : commence par le cadrage

Ouvre une issue « Nouveau projet (cadrage) » et décris ton idée en vrac. L'agent te pose ses questions sur le
produit, propose une stack (des technos établies et bien documentées, en réutilisant les briques toutes
faites), puis, après ton « go », pose les fondations de l'appli et crée une issue par feature de la première
version. Tu lances ensuite chaque feature en y écrivant « go ». L'agent répond en général en quelques minutes.

## Moteur de secours : GitHub Actions

Si la limite quotidienne de la routine Claude te gêne, passe ce projet sur **GitHub Actions** dans le panneau
(onglet *Règles*). Il faut alors un troisième secret, `CLAUDE_CODE_OAUTH_TOKEN`, qui se génère avec la commande
`claude setup-token` dans un terminal où Claude Code est installé. Sur un dépôt public les minutes sont
illimitées ; en privé, 2 000 minutes par mois sont offertes.

## Ce qu'il ne faut pas toucher

`.factory/`, `.claude/skills/` et `factory.config.json` sont gérés par la factory et le panneau.
