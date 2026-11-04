# Projet de la software factory

Ce dépôt est construit par la [software factory](https://github.com/phoibe/factory). Tu décris une idée dans
une issue, tu réponds aux questions de l'agent, tu valides la spec, les captures et la recette. Le reste est
automatique : plan, code, tests, revue, contrôles, mise en ligne.

- **Panneau** (suivi et règles) : https://phoibe.github.io/factory/
- **Cadrage** (une fois, en premier) : onglet *Issues* → *New issue* → « Nouveau projet (cadrage) »
- **Nouvelle idée** : onglet *Issues* → *New issue* → « Nouvelle feature »
- **Un bug** : *New issue* → « Signaler un bug »
- **Une question** sur l'appli (« ce cas est-il prévu ? ») : *New issue* → « Poser une question ». L'agent répond
  à partir du code et de la fiche produit, sans rien modifier.

## Ce que tu as à faire, et quand

| Moment | Ce que tu vois | Ce que tu réponds |
| --- | --- | --- |
| Cadrage | Des questions sur ton appli, un questionnaire design, deux ou trois directions visuelles au choix, puis `## Fiche projet` avec la stack proposée | « go » pour poser les fondations, ou ce qu'il faut changer |
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

## Mettre un projet dans la factory (un script, environ 5 minutes)

Sur ton Mac, ouvre le Terminal, colle cette ligne telle quelle et appuie sur Entrée :

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/phoibe/factory/main/scripts/factory.sh)
```

Le script te pose ses questions : nouveau projet ou projet déjà commencé, puis le nom du nouveau projet, ou
le projet existant à choisir dans la liste de tes dépôts. Il te montre ce qu'il va faire et attend ton accord.

Le script fait tout seul : création du dépôt (ou ajout des fichiers de la factory à ton projet, sans toucher à
ton code), accès de Claude, secrets de la routine, labels et skills, Vercel, puis il ouvre l'issue de
cadrage. Il ne te demande que :

- **la première fois seulement** : l'outil GitHub (le script l'installe s'il manque), une
  connexion à GitHub et à Vercel dans le navigateur, puis l'URL et le jeton de ta routine Claude (voir
  « La routine Claude » ci-dessous) ; il les garde sur ton ordinateur pour les projets suivants ;
- **à chaque projet** : un clic pour ajouter le dépôt à ta routine (le script te donne le lien), et une
  phrase pour décrire ton idée.

Pour un projet adopté, l'agent lit d'abord ton code, te dit ce qu'il a compris, et ne pose que les questions
sur ce qu'il n'a pas pu deviner. Il garde ta stack et ne refait pas l'appli.

### La routine Claude (une seule fois, pour tous tes projets)

1. Ouvre https://claude.ai/code/routines → **New routine**, nom `Factory`.
2. **Instructions** : copie exactement ce texte :

   > Tu es l'agent de la software factory. Le bloc routine-fire-payload contient un JSON qui décrit ton run
   > (dépôt, issue, PR, étape) : c'est ta tâche, agis selon ce JSON. Lis le fichier .factory/AGENT.md du
   > dépôt et suis exactement l'étape indiquée par "stage".

3. **Repositories** : tes projets (le script te rappellera d'y ajouter chaque nouveau). **Environment** :
   *Default*. **Connectors** : aucun. **Trigger** : **API**, puis **Generate token**. Garde l'URL et le jeton
   le temps de les coller dans le script.

Le panneau (https://phoibe.github.io/factory/) trouve tout seul les projets de la factory. Il demande un jeton
GitHub la première fois : voir l'étape 7 plus bas.

<details>
<summary>Mise en route à la main, sans le script</summary>

> **Important : à partir de l'étape 2, tout se passe dans le NOUVEAU dépôt**, pas dans factory-template. Avant
> chaque réglage, vérifie que l'adresse dans ton navigateur contient bien le nom de ton projet
> (`github.com/phoibe/<ton-projet>/…`). Dans ce guide, remplace `<ton-projet>` par ce nom.

### 1. Créer le dépôt

Sur https://github.com/phoibe/factory-template, clique **Use this template** → **Create a new repository**.
Choisis un nom (court, sans espace, ex. `habitudes`), **Private** si tu préfères, puis **Create repository**.
GitHub t'emmène sur le nouveau dépôt : c'est là que se passe la suite.

### 2. Lancer la première synchro

Ouvre `https://github.com/phoibe/<ton-projet>/actions/workflows/factory-sync.yml` → **Run workflow** →
**Run workflow**. Elle crée les labels `factory:*` et installe les skills. Attends la coche verte (moins d'une
minute ; recharge la page si besoin).

### 3. Donner accès à Claude

Ouvre https://github.com/apps/claude/installations/select_target et choisis ton compte.

- Le plus simple : **All repositories**, puis **Save**. Rien à refaire pour les projets suivants.
- Si tu préfères **Only select repositories** : la liste doit contenir **ce nouveau dépôt, `factory` et
  `factory-template`**. Ajoute ce dépôt sans retirer les autres, puis **Save**. À refaire à chaque nouveau projet.

### 4. Créer la routine Claude (le moteur des agents)

1. Ouvre https://claude.ai/code/routines → **New routine**.
2. **Nom** : `Factory` (une seule routine sert à tous tes projets ; si tu l'as déjà, passe à l'étape 4 et ajoute juste ce dépôt).
3. **Instructions** : copie exactement ce texte :

   > Tu es l'agent de la software factory. Le bloc routine-fire-payload contient un JSON qui décrit ton run
   > (dépôt, issue, PR, étape) : c'est ta tâche, agis selon ce JSON. Lis le fichier .factory/AGENT.md du
   > dépôt et suis exactement l'étape indiquée par "stage".

4. **Repositories** : ajoute **ce dépôt** (`phoibe/<ton-projet>`), sans retirer les autres projets.
5. **Environment** : *Default* convient.
6. **Connectors** : retire-les tous, la factory n'en a pas besoin.
7. **Trigger** : **API**. Enregistre la routine.
8. Rouvre la routine → crayon → section du déclencheur API :
   - copie l'**URL** et colle-la dans ta note (elle reste affichée, tu pourras la recopier plus tard) ;
   - clique **Generate token**, copie le **jeton** et colle-le dans ta note **tout de suite** : il n'est affiché
     qu'une fois. Si tu le perds, régénère-en un, l'ancien cesse de marcher.

### 5. Ranger l'URL et le jeton dans les secrets du dépôt

1. Ouvre `https://github.com/phoibe/<ton-projet>/settings/secrets/actions`.
   Vérifie le nom du dépôt en haut de la page : ce doit être ton projet, **pas factory-template**.
2. Reste sur l'onglet **Secrets** (pas « Variables »), section **Repository secrets** (pas « Environment
   secrets »). Clique **New repository secret**, deux fois :
   - **Name** `FACTORY_ROUTINE_URL`, **Secret** : l'URL de ta note ;
   - **Name** `FACTORY_ROUTINE_TOKEN`, **Secret** : le jeton de ta note.
3. Les deux noms apparaissent dans la liste **Repository secrets**. Efface ensuite ta note.

### 6. Brancher Vercel (les previews et la mise en ligne)

1. Ouvre https://vercel.com/new (offre Hobby gratuite, connexion avec GitHub).
2. **Import** ce dépôt, sans rien changer, **Deploy**. Le premier déploiement peut échouer tant que l'appli
   n'existe pas : c'est normal.
3. Ensuite, chaque PR reçoit une preview et chaque fusion met la version en ligne.

Si une feature a besoin d'un service (base de données, authentification…), l'agent te le demandera dans
l'issue et te dira quoi cliquer.

### 7. Régler les étapes et les skills (facultatif)

Les réglages par défaut conviennent pour démarrer. Pour les changer, fais-le **avant le cadrage** dans le
panneau https://phoibe.github.io/factory/ → ton projet → onglet **Règles** (étapes, skills, features en
parallèle). La première fois, le panneau demande un jeton GitHub :

1. Ouvre https://github.com/settings/personal-access-tokens/new.
2. **Token name** : `factory panneau`. **Expiration** : 1 an. **Resource owner** : ton compte.
3. **Repository access** : **All repositories**.
4. **Permissions** : une boîte **Repositories** apparaît sous « Account ». Dans **Repositories** (pas dans
   « Account »), clique **+ Add permissions** et coche **Contents**, **Issues** et **Pull requests**. Mets
   Contents et Issues sur « Read and write », laisse Pull requests sur « Read-only ». « Metadata » s'ajoute
   tout seul.
5. **Generate token**, copie-le et colle-le dans le panneau. Il reste dans ton navigateur.

</details>

### 8. C'est prêt : commence par le cadrage (le script l'ouvre pour toi)

Ouvre une issue « Nouveau projet (cadrage) » et décris ton idée en vrac. L'agent te pose ses questions sur le
produit, puis un questionnaire design (où et quand tu t'en sers, ce que tu veux ressentir, les applis dont tu
aimes ou n'aimes pas le look…) et te propose deux ou trois directions visuelles au choix. Il propose ensuite une stack (des technos établies et bien documentées, en réutilisant les briques toutes
faites), puis, après ton « go », pose les fondations de l'appli (avec ses documents de design : couleurs, polices, composants) et crée une issue par feature de la première
version. Tu lances ensuite chaque feature en y écrivant « go ». Plus tard, pour changer la vision, le périmètre, le design ou la stack, utilise « Revoir le cadrage » dans le panneau : l'agent met la fiche produit à jour et crée les nouvelles features, sans refaire les fondations. L'agent répond en général en quelques minutes.

## Si ça ne démarre pas

| Ce que tu vois | Ce qu'il faut faire |
| --- | --- |
| « je ne trouve pas les secrets de la routine » | Les secrets sont dans un autre dépôt ou au mauvais endroit : refais l'étape 5 sur la page des secrets **de ce projet**, puis écris « réessaie » dans l'issue. Supprime les secrets créés par erreur ailleurs (icône poubelle). |
| « la routine Claude a répondu 401 » | Le jeton est faux ou a été régénéré : génère-en un nouveau (étape 4.8) et remplace `FACTORY_ROUTINE_TOKEN`. |
| « la routine Claude a répondu 429 » | Limite quotidienne atteinte : réessaie plus tard, ou passe sur le moteur de secours ci-dessous. |
| Aucune réponse après 10 minutes, pas de 👀 | Onglet **Actions** du projet : le workflow « Factory » doit être activé. Vérifie aussi l'étape 3 (accès Claude). |
| L'agent démarre mais ne peut rien écrire | L'accès Claude (étape 3) ne couvre pas ce dépôt. |

## Moteur de secours : GitHub Actions

Si la limite quotidienne de la routine Claude te gêne, passe ce projet sur **GitHub Actions** dans le panneau
(onglet *Règles*). Il faut alors un troisième secret dans **Repository secrets** du projet (étape 5), `CLAUDE_CODE_OAUTH_TOKEN`, qui se génère avec la commande
`claude setup-token` dans un terminal où Claude Code est installé. Sur un dépôt public les minutes sont
illimitées ; en privé, 2 000 minutes par mois sont offertes.

## Ce qu'il ne faut pas toucher

`.factory/`, `.claude/skills/` et `factory.config.json` sont gérés par la factory et le panneau.
