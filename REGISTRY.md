# SIE — REGISTRY

## Pré-enregistrement public des modèles de prédiction

> Ce document désigne, de façon datée et publique, quel modèle sert de
> référence pour chaque marché et chaque périmètre. Toute modification
> future passe par un commit horodaté sur ce dépôt — jamais de désignation
> rétroactive.
>
> **v1.0 — désignations initiales du 6 juillet 2026.**

---

## La règle de promotion (fait foi, publiée avant toute décision)

Un changement de champion (un challenger prend la place publique) n'est
possible que si TOUS ces critères sont vrais simultanément, évalués
UNIQUEMENT aux fenêtres fixes ci-dessous :

- **Fenêtres d'évaluation** : 1-15 janvier · trêve internationale de mars ·
  intersaison de juillet · trêve d'octobre.
- **P1** : n ≥ 100 matchs appariés **LIVE** (jamais backtest), challenger
  inchangé (même hash) sur toute la période.
- **P2** : écart de Brier moyen (challenger − champion) ≤ −0,005.
- **P3** : p-value Holm-ajustée < 0,05 (test unilatéral) ET bootstrap
  concordant (intervalle de confiance ajusté entièrement négatif).
- **P4** : garde-fou de calibration — ECE du challenger ≤ ECE du champion + 0,01.

Si plusieurs challengers remplissent ces critères à la même fenêtre, le
meilleur écart de Brier moyen gagne.

**Rétrogradation** : symétrique — un champion n'est rétrogradé que par un
challenger qui remplit P1 à P4 (même mécanisme, vu de l'autre côté).

**Frein d'urgence** (non-statistique) : si le Brier glissant du champion
sur 100 matchs devient pire que le plancher naïf (fréquences de base), ce
n'est plus de la variance — gel immédiat, retour au dernier état sain,
note publique dans ce dépôt.

**Montée de version** (le même modèle, estimation corrigée — ex. Poisson
v1 → v2) : autorisée uniquement aux fenêtres, sur preuve walk-forward,
suivie d'une surveillance live de 50 matchs avec rollback automatique si
le Brier live de la nouvelle version dépasse l'ancienne (en shadow) de
+0,02. Ce n'est pas une promotion au sens ci-dessus : l'identité du
champion ne change pas, seule son estimation est corrigée.

---

## Périmètre INTERNATIONAL (labo — non commercialisé)

| Marché | Modèle affiché | Hash d'état (SHA-256) | Désigné le | Prochaine revue |
|---|---|---|---|---|
| 1X2 | `elo-davidson-wc-v1` | `sha256:61a5362a3c690d097f1acbf5efbdc12b0c47dc5660a...` | 2026-07-06 (référence pré-tournoi, gelée le 27/06/2026) | trêve d'octobre 2026 |
| Score exact | `elo-davidson-wc-poisson` | `sha256:124b87ed808e2955e0eace897c4bfa02c00c4ba3c88...` | 2026-07-06 (gelé le 01/07/2026) | trêve d'octobre 2026 |
| Over/Under | `elo-davidson-wc-poisson` | `sha256:124b87ed808e2955e0eace897c4bfa02c00c4ba3c88...` | 2026-07-06 (gelé le 01/07/2026) | trêve d'octobre 2026 |

### Note de gouvernance — pourquoi v1 est champion

`elo-davidson-wc-v1` est champion **parce que désigné avant l'existence
des challengers**, pas parce qu'il est mesuré meilleur. C'est l'argument
central de ce dispositif : couronner un challenger sur la seule base d'un
backtest ou d'un petit échantillon live violerait notre propre règle
(P1 : n≥100 matchs live). Le meilleur challenger courant est documenté
ci-dessous, avec son statut réel — jamais maquillé en champion tant que
la règle n'est pas remplie.

### Challengers enregistrés (roster GELÉ — aucun ajout hors fenêtre)

- **1X2** : `elo-davidson-wc-gd` (hash `sha256:be2f3e1da9e3f841b2df2db3dfee43b813f9afac78b...`)
  — ⭐ **challenger en tête** au 6 juillet 2026, statistiquement non
  significatif à ce stade (échantillon live encore trop petit pour une
  décision — voir seuils ci-dessous) · `elo-davidson-wc-ens`
  (hash `sha256:81877bd1c69ce8294b414842aff918b6548f7603eae...`) ·
  famille λ/w24/hfa (l10-l14, w24, w24t, hfa, hfat) : statut R&D interne,
  hors course à la promotion (micro-variantes, écarts indétectables sur
  tout horizon réaliste).
- **Score exact / Over-Under** : **`elo-davidson-wc-poisson-v2`**
  (hash `sha256:10d9fed95c564824df17aa8846e2fb21b07950cd14e...`) — montée
  de version du champion (shrinkage bayésien vers la confédération +
  pondération temporelle + covariable de palier de compétition), gelée le
  6 juillet 2026.

  **⚠️ Historique de mesure — deux corrections successives.**

  *Mesure 1 (6 juillet, invalidée)* : comparaison informelle vs champion
  sur n=88 matchs, Brier −0,0075 en faveur de v2. **Invalidée le 11
  juillet** : l'état gelé utilisé n'était pas coupé au 10/06 (protocole
  de backtest propre) mais calculé sur tout l'historique jusqu'au jour
  même — contamination par les résultats de la CdM re-prédite.

  *Mesure 2 (11 juillet, invalidée)* : un état décontaminé
  (`elo-davidson-wc-poisson-v2-backtest`, coupure stricte 10/06/2026) a
  été comparé séparément à FIFA (−0,0155 vs −0,0191 pour v1) et
  interprété comme "v2 est un progrès réel". **Invalidée le 12 juillet** :
  ces deux écarts vs FIFA étaient mesurés sur des ensembles de matchs
  différents (94 vs 98) — pas une comparaison appariée. Un écart entre
  deux nombres non appariés ne mesure rien de fiable.

  *Mesure 3 (12 juillet, VALIDE — méthode correcte)* : test apparié
  direct v2-backtest vs v1, sur les 93 matchs communs aux deux modèles,
  états décontaminés des deux côtés. **Différence moyenne : −0,0006
  (quasi nulle). IC 95% bootstrap : [−0,0057 ; +0,0044] — contient zéro.
  p ≈ 0,40.** Verdict : **v2 et v1 sont statistiquement indiscernables
  sur cet échantillon** — l'amélioration précédemment annoncée n'est pas
  soutenue par une mesure correctement appariée.

  **Aucune promotion, aucun changement de champion — v1 reste champion.**
  Les gains attendus du shrinkage restent réels sur des cas spécifiques
  documentés ailleurs (ex. Cape Verde, désormais couvert par les 92→98
  matchs backfillés), mais ne se traduisent pas en gain global mesurable
  à ce stade. n<100 sur toute mesure — seuil de décision non atteint.
  Prochaine mesure : protocole complet de septembre (Holm, n≥100).

### Les trois seuils n (jamais confondus)

| Seuil | Fonction | Ce qu'il autorise |
|---|---|---|
| n ≥ 20 | Décrire | Couleurs internes (arène privée) uniquement |
| n ≥ 50 | Montrer | Première mention publique d'un challenger — obligatoirement accompagnée d'un intervalle de confiance et de la mention "non significatif" si applicable |
| n ≥ 100 | Décider | Seuil plancher de toute promotion (règle ci-dessus) |

---

## Périmètre PREMIER LEAGUE (produit, lancement 21 août 2026)

| Marché | Modèle affiché | Hash d'état | Désigné le | Prochaine revue |
|---|---|---|---|---|
| 1X2 | `elo-davidson-v1` | `sha256:93cf543817f538b96141f95d43ed5026458dab7a0f74a0b6b6f5f2fc2d4ac6cc` | 2026-07-06 (gelé et backtesté en juin 2026) | trêve d'octobre 2026 |
| Score exact | *désignation engagée* | — | au plus tard le 14 août 2026 | — |
| Over/Under | *désignation engagée* | — | au plus tard le 14 août 2026 | — |

**Note technique** : le champ de hash côté PL s'appelle `state_hash` (et non
`integrity_hash` comme côté international) — une différence de convention
de nommage entre les deux pipelines, sans conséquence sur la validité du
mécanisme lui-même. Les deux garantissent la même chose : toute
modification manuelle de l'état invalide le hash.

---

## Historique des révisions de ce document

| Date | Changement |
|---|---|
| 2026-07-06 | Création — désignations initiales, règle de promotion v1.0 |
| 2026-07-11 | Correction n°1 de la mesure poisson-v2 : la comparaison du 6 juillet (n=88, −0,0075) était contaminée méthodologiquement (état non coupé au 10/06). Mesure décontaminée produite (−0,0155 vs FIFA, n=98). |
| 2026-07-12 | Correction n°2 de la mesure poisson-v2 : la mesure du 11 juillet comparait deux écarts vs FIFA sur des ensembles de matchs différents (94 vs 98) — pas une comparaison appariée. Test apparié correct produit sur 93 matchs communs (états décontaminés des deux côtés) : différence quasi nulle (−0,0006), IC 95% contenant zéro. **v1 et v2 sont statistiquement indiscernables — aucune promotion.** Cette double correction est documentée intégralement (rien supprimé) conformément à la règle : le REGISTRY porte des résultats validés et leurs corrections, jamais des brouillons non vérifiés — une leçon qui s'applique à la publication future de toute mesure informelle. |

**Pré-enregistrer une date de désignation avant même d'avoir le modèle
final est déjà un acte de pré-enregistrement** : cette date ne pourra pas
être repoussée sans qu'un commit visible ne l'explique.

---

## Historique des révisions de ce document

| Date | Changement |
|---|---|
| 2026-07-06 | Création — désignations initiales, règle de promotion v1.0 |
