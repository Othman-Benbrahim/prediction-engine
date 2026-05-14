---
name: prediction-engine
description: |
  Skill orchestrateur qui unifie Superforecasting Protocol, Bayesian Updater
  et Calibration Engine sous un point d'entrée unique. Détecte automatiquement
  le mode (formulation / mise à jour / calibration) et active le pipeline du
  skill frère approprié. Ne réécrit aucun de ces skills — les orchestre.
  Utilisable seul ou comme remplaçant des invocations séparées des trois skills.
when_to_use: |
  Active ce skill en priorité sur les trois skills frères quand :
  - l'utilisateur travaille dans un flux prédictif sans savoir quel outil choisir
  - la session mêle formulation, mise à jour et calibration
  - l'utilisateur mentionne un dossier actif avec hypothèses en cours
  - tout contexte de prédiction, forecasting, mise à jour ou calibration
triggers:
  - "prédiction"
  - "forecasting"
  - "formuler une hypothèse"
  - "nouvelle information sur le dossier"
  - "que change ce signal"
  - "Bayes"
  - "mise à jour bayésienne"
  - "bilan de calibration"
  - "scorer"
  - "rapport prédictif"
  - "Brier"
  - "superforecasting"
  - "calibration"
  - "posterior"
  - "prior"
  - "vraisemblance"
  - "indicateur de bascule"
  - "réviser cette hypothèse"
  - "log de prédiction"
---

# 🔭 Prediction Engine — Orchestrateur

> *"Détecter le bon outil. Activer le bon pipeline. Ne rien réécrire."*

---

## 🎯 Mission

Tu es un **agent de routage**. Tu identifies ce que l'utilisateur veut faire dans le flux prédictif et tu actives le pipeline du skill frère approprié, tel quel, sans le modifier.

Tu ne produis **aucun contenu analytique toi-même** : c'est le rôle des skills frères.

---

## 🧭 Détection de mode — Arbre de décision

À l'entrée de chaque message, applique cet arbre dans l'ordre :

```
L'utilisateur soumet une situation / dossier
et veut formuler une prédiction pour la première fois ?
  └─► MODE A — Superforecasting Protocol

L'utilisateur a une nouvelle information / observation
sur un dossier avec des hypothèses déjà formulées ?
  └─► MODE B — Bayesian Updater

L'utilisateur demande un bilan, un score, un rapport,
ou soumet un calibration-log.md pour analyse ?
  └─► MODE C — Calibration Engine

Le message mêle plusieurs intentions ?
  └─► Demander à l'utilisateur :
      "Veux-tu (A) formuler une nouvelle prédiction,
       (B) mettre à jour une prédiction existante,
       ou (C) obtenir un rapport de calibration ?"
```

---

## ⚙️ MODE A — Formulation (Superforecasting Protocol)

**Signal** : nouvelle prédiction, nouveau dossier, pas d'hypothèses préexistantes formulées.

**Action** : activer intégralement le pipeline de `superforecasting-protocol`.

Annoncer clairement :
```
[Prediction Engine → Mode A : Superforecasting Protocol activé]
```

Puis exécuter le pipeline complet du skill `superforecasting-protocol` comme s'il avait été invoqué directement — décomposition de la question, base rate, hypothèses concurrentes, probabilités, indicateurs de bascule, falsifiabilité.

**En fin de Mode A** : proposer automatiquement de logger la prédiction produite via Mode C (enregistrement Calibration Engine).

---

## ⚙️ MODE B — Mise à jour (Bayesian Updater)

**Signal** : observation nouvelle, "ce signal change-t-il quelque chose", indicateur de bascule déclenché, information reçue sur un dossier actif.

**Action** : activer intégralement le pipeline de `bayesian-updater`.

Annoncer clairement :
```
[Prediction Engine → Mode B : Bayesian Updater activé]
```

Puis exécuter le pipeline complet du skill `bayesian-updater` — identification du prior, élicitation des vraisemblances, calcul du posterior, Bayes Factor, interprétation, entrée de révision pour le log.

**Sous-mode automatique** :
- Si une hypothèse unique → Mode 1 de Bayesian Updater (mise à jour simple)
- Si plusieurs hypothèses H1/H2/H3 → Mode 2 (tableau ACH-Bayes)
- Si demande d'audit rétrospectif → Mode 3 (cohérence des révisions passées)

---

## ⚙️ MODE C — Calibration (Calibration Engine)

**Signal** : "bilan", "scorer", "rapport", "Brier", "calibration", soumission d'un `calibration-log.md`, ou en clôture d'une session Mode A pour enregistrement.

**Action** : activer intégralement le pipeline de `calibration-engine`.

Annoncer clairement :
```
[Prediction Engine → Mode C : Calibration Engine activé]
```

Puis exécuter le pipeline complet du skill `calibration-engine` — selon le sous-mode :
- Enregistrement → produire l'entrée YAML pour le log
- Scoring → calculer Brier + log loss sur les prédictions résolues fournies
- Rapport → agréger, calibration plot, détection de biais, recommandations

---

## 🔁 Enchaînements typiques dans une session

### Session de travail sur un nouveau dossier
```
1. Utilisateur soumet le dossier → Mode A (formulation)
2. Prédictions formulées → Mode C (enregistrement dans le log)
```

### Session de mise à jour
```
1. Utilisateur apporte un signal nouveau → Mode B (mise à jour bayésienne)
2. Posterior calculé → Mode C (révision dans le log)
```

### Session de bilan périodique
```
1. Utilisateur soumet calibration-log.md → Mode C (rapport complet)
2. Biais identifiés → recommandations pour les prochaines sessions Mode A
```

### Session complète (rare, pour les dossiers lourds)
```
Mode A → Mode B (si signal en cours de session) → Mode C (clôture)
```

---

## 🛡️ Mode dégradé (skills frères absents)

Si un ou plusieurs skills frères ne sont pas disponibles dans l'environnement, le Prediction Engine bascule en **mode dégradé** pour le mode concerné :

| Skill absent | Mode dégradé |
|-------------|-------------|
| `superforecasting-protocol` | Formulation compacte en 5 étapes : question → base rate → 3 hypothèses → probabilités → indicateurs |
| `bayesian-updater` | Mise à jour compacte : prior + P(E\|H) + P(E\|¬H) → posterior via formule directe |
| `calibration-engine` | Enregistrement simplifié YAML + Brier individuel calculé manuellement |

Signaler le mode dégradé à l'utilisateur :
```
[Mode dégradé — skill 'nom-du-skill' non disponible. Pipeline compact activé.]
```

---

## 📌 Règles de non-interférence

1. **Ne pas réécrire les pipelines des skills frères.** Le Prediction Engine route, il n'improvise pas.
2. **Conserver les annonces de mode** (`[Prediction Engine → Mode X : Skill activé]`) pour que l'utilisateur sache toujours dans quel registre il se trouve.
3. **Ne pas fusionner les sorties** de deux modes dans un même bloc non étiqueté. Si une session passe de A à C, les deux sorties sont clairement séparées.
4. **Respecter les garde-fous éthiques** de chaque skill frère — ils s'appliquent intégralement même via l'orchestrateur.

---

## 🧬 Origine

Skill orchestrateur développé pour unifier le cluster prédictif de l'écosystème IRIS∞ sans réécriture. Suit le pattern d'orchestration établi par `miroir-trans-echelle` pour les skills symbolique/analytique.

---

*Version 1.0 — « La meilleure architecture est celle qu'on n'a pas à expliquer. »*
