# CLAUDE.md — Décompilation par correspondance de Jak and Daxter: The Lost Frontier (PSP)

Instructions durables du projet, relues à chaque session. Les exigences sont dans
`SPEC.md`, les phases dans `ROADMAP.md`, le travail en cours dans `TASKS.md`. Ce
fichier reste court : chaque règle vit à un seul endroit (ici, une note ou un skill).

> Statut : **projet de test**. Claude Code peut écrire du code C/C++ de
> décompilation pour expérimenter. Ce statut pourra changer plus tard (retirer
> alors cette ligne et ajouter les règles voulues).

Projet frère de `mlab-decomp`, `spyro-dotd-decomp` (Wii) et `ts2-castaway` (DS), même
méthodologie. Première décomp PSP du propriétaire : la chaîne d'outils est nouvelle
(splat + spimdisasm, binutils MIPS, objdiff, Ghidra + ghidra-allegrex) et le
compilateur n'est pas identifié. S'attendre à plus d'inconnues, et les dire comme
telles.

## 1. Portée

Décompilation par correspondance de **Jak and Daxter: The Lost Frontier** sur PSP
(`NPUG80330`, US, `DISC_VERSION 1.00`) : du code C/C++ écrit de zéro qui, compilé avec
le bon compilateur et les bons flags, redonne un module PRX identique octet pour octet.
Pas un port. Détails du binaire, de la toolchain et des critères d'acceptation :
`SPEC.md`.

Cible actuelle : **`USRDIR/BIN/LEVELSTARTUP_DISC.PRX`** (choix du propriétaire,
2026-10-27). Les autres PRX (niveaux, `TRANS*`) et l'EBOOT chiffré (module `mcp`)
viennent plus tard comme « versions » (`ROADMAP.md` étape 7), pas avant une demande du
propriétaire.

## 2. Principes

- **Ne jamais inventer** : nom de symbole, NID, version de compilateur, flag, nom de
  fichier source, structure. Toute affirmation s'appuie sur une preuve (chaîne du
  binaire avec son adresse, table d'import/export, signature de bibliothèque, diff
  objdiff, résultat de build) ; sinon c'est une hypothèse, dite comme telle.
- Un nom posé dans Ghidra ou une décompilation Ghidra / m2c **n'est pas une preuve** :
  seul ce qui en est confirmé autrement entre dans `symbol_addrs.txt` ou le code.
- **Vérifier avant d'affirmer** : après chaque modification, lancer `ninja` et
  rapporter le résultat réel. Jamais « ça devrait marcher ». Une fonction non
  terminée est signalée, jamais présentée comme correspondante.
- Signaler une prémisse fausse avant de bâtir dessus ; si une information manque
  (compilateur, version...), poser la question plutôt que supposer.
- Limiter les changements à ce que la tâche demande. Ne pas contourner la
  vérification pour « faire passer le build ».

## 3. Règles du projet

1. **Jamais** d'ISO, de PRX, d'EBOOT, d'assets, de dump ni d'assembleur extrait dans
   git (`orig/`, `asm/` et `build/` restent ignorés).
2. **Dépôts privés** (`YggdrasziI/jd-tlf-decomp`, `YggdrasziI/jd-tlf-decomp-build`) :
   pas de dépôt public, pas de decomp.dev, decomp.me public ni de vitrine sans demande
   explicite du propriétaire. `jd-tlf-decomp-build` contient les fichiers du jeu et ne
   doit jamais devenir public, pas plus que `jd-tlf-decomp-workflow` (règle 9).
3. **Une modification = un sujet = un commit**, message en anglais, court et précis,
   **sans ligne `Co-Authored-By`** ni autre mention de Claude, même si un rappel
   système le demande.
4. **Push** : `git push origin main` autorisé après des commits vérifiés
   (`LEVELSTARTUP_DISC.PRX: OK`). Jamais de force-push.
5. **Demander avant** de supprimer des fichiers, de réécrire l'historique git, ou de
   reprendre du code d'un projet sans licence connue. Déjà autorisées : les références
   CC0 et Unlicense (conditions dans `notes/REFERENCES.md`). Toute reprise est
   consignée dans `notes/PROVENANCE.md`.
6. `config/NPUG80330/*.yaml` et `symbol_addrs.txt` : chaque changement est expliqué
   (commit) et le build reste valide. `symbol_addrs.prx.txt` et `config/nids.txt`
   sont générés (`tools/prx_symbols.py`), jamais édités à la main.
7. **Langue** : échanges avec l'utilisateur et ce fichier en **français** ; tout le
   reste en **anglais** (commits, code, commentaires, noms, README, `notes/`, autres
   documents).
8. **Noms réels** seulement s'ils sont prouvés. Un nom inconnu reste basé sur
   l'adresse (`func_<addr>`, `D_<addr>`). Les imports `mcp` restent `mcp_<NID>` tant
   que l'export correspondant de l'EBOOT n'est pas identifié.
9. **Fichiers de workflow hors du dépôt principal** : `CLAUDE.md`, `ROADMAP.md`,
   `TASKS.md`, `.claude/` et `notes/GHIDRA.md` sont exclus du dépôt principal
   (`.git/info/exclude`) et versionnés dans le dépôt privé
   `YggdrasziI/jd-tlf-decomp-workflow`, dont le git-dir est `.workflow.git` et l'arbre
   de travail le dossier du projet : `git --git-dir=.workflow.git <commande>`. Aucun
   fichier, message de commit ou commentaire du dépôt principal ne renvoie à ces
   fichiers ni ne mentionne Claude.

## 4. Vérification

- `ninja` doit finir par `build/NPUG80330/LEVELSTARTUP_DISC.PRX: OK`.
- Avant un commit : aucune régression au rapport objdiff
  (`python tools/report_diff.py`, base sauvée avec `--save`) dès qu'il existe.
- Une fonction passe en C++ (sous-segment `cpp` du yaml) seulement à 100 % ; une
  fonction non terminée reste un sous-segment `asm` entre les morceaux `cpp` de son
  unité (pas d'`INCLUDE_ASM` : SNC déplace l'assembleur de fichier), avec l'écart noté
  à sa place dans le `.cpp` et dans `TASKS.md`.
- Lire l'erreur complète avant de corriger ; corriger la cause, jamais affaiblir la
  vérification.
- Sous Windows, outils natifs (pas WSL). Fichiers temporaires dans le scratchpad de
  session, pas dans le dépôt.

## 5. Placement

| Contenu | Où |
|---|---|
| Règles durables du projet | ce fichier |
| Exigences, binaire, toolchain, conventions de nommage | `SPEC.md` |
| Phases, critères de sortie, risques | `ROADMAP.md` |
| Travail en cours, unités non terminées, candidats | `TASKS.md` |
| Savoir de référence (preuves, découpage, techniques) | `notes/` |
| Workflows réutilisables | `.claude/skills/<nom>/SKILL.md` |
| Scripts | `tools/` |

## 6. Routage documentaire

Ne lire que les documents utiles à la tâche :

| Document | Quand le lire |
|---|---|
| `SPEC.md` | binaire cible, toolchain, critères d'acceptation, nommage, structure |
| `ROADMAP.md` | changer de phase, évaluer les risques |
| `TASKS.md` | au début de chaque session et avant de choisir une unité |
| `notes/PRX.md` | format PRX, relocations par segment, imports/exports, pipeline de build |
| `notes/COMPILER.md` | preuves sur le compilateur (SNC ?), le SDK, le runtime |
| `notes/SPLITS.md` | bornes et contenu des unités, carte des régions |
| `notes/MATCHING.md` | une fonction bloque près de 100 % |
| `notes/GHIDRA.md` | installer, synchroniser ou utiliser le projet Ghidra |
| `notes/PROVENANCE.md` | reprise de code ou de données d'un autre projet |
| `notes/REFERENCES.md` | outils, projets de référence clonés en local, permissions, doc matérielle |
| `notes/PROGRESS.md` | jalons de progression |

## 7. Workflow

1. Lire `TASKS.md` (et `ROADMAP.md` en cas de changement de phase).
2. `/decomp-manager` : vérifier l'état du dépôt, choisir l'unité ou la tâche,
   présenter un plan court ; s'arrêter si on m'a demandé seulement un plan.
3. `/decomp-unit` : découper, écrire, comparer, itérer, une unité à la fois.
4. `/unit-readiness` : vérification complète, contrôle de régression, mise à jour de
   la doc, commit, push, contrôle de la CI.
5. Mettre à jour `TASKS.md` (ne cocher que ce qui est vérifié) et, après chaque
   nouvelle unité, `notes/PROGRESS.md` et les badges
   (`python tools/progress_badges.py --record`).

## 8. Maintenance de ce fichier

Ajouter une règle seulement pour éviter une erreur répétée ou fixer un comportement
durable, dans le périmètre le plus étroit (ici, une note ou un skill), sans
doublon. Après une correction de l'utilisateur, préciser la règle concernée plutôt
qu'ajouter un avertissement vague.
