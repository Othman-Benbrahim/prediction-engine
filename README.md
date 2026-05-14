# 🔭 Prediction Engine — Point d'entrée unifié

> *"Une seule porte pour trois ateliers."*

Skill orchestrateur qui unifie trois outils de l'écosystème IRIS∞ sous un point d'entrée unique : **Superforecasting Protocol**, **Bayesian Updater** et **Calibration Engine**. Il ne réécrit aucun de ces skills — il détecte le mode voulu et active le bon pipeline.

---

## Pourquoi ce skill existe

Les trois skills de vérification prédictive forment un seul workflow en pratique :

```
Formuler une prédiction    →  superforecasting-protocol
Recevoir une nouvelle info →  bayesian-updater
Mesurer / auditer          →  calibration-engine
```

Les invoquer séparément à chaque session crée de la friction inutile. Le Prediction Engine est le guichet unique qui dispatche automatiquement.

Les trois skills sous-jacents restent **autonomes et utilisables seuls** — ce skill ne les remplace pas, il les orchestre.

---

## Les trois modes

| Mode | Déclencheur typique | Skill activé |
|------|---------------------|-------------|
| **A — Formulation** | Nouvelle prédiction à formuler | `superforecasting-protocol` |
| **B — Mise à jour** | Nouvelle information sur un dossier | `bayesian-updater` |
| **C — Calibration** | Bilan, scoring, rapport, biais | `calibration-engine` |

---

## Activation

> "prédiction", "forecasting", "formuler une hypothèse", "nouvelle information sur le dossier", "que change ce signal", "Bayes", "mise à jour", "bilan de calibration", "scorer", "rapport prédictif", "Brier", "superforecasting"

---

## Dépendances

Requiert les trois skills frères installés dans l'environnement :
- `superforecasting-protocol`
- `bayesian-updater`
- `calibration-engine`

En leur absence, le skill bascule en mode dégradé (versions compactes intégrées dans le SKILL.md).

---

## Licence

GPL-3.0 — écosystème IRIS∞.
