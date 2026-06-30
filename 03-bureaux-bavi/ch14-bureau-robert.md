# Bureau Robert : le conseil stratégique

Le Bureau Robert est le **consultant IT** de l'équipe. Il produit des analyses stratégiques, des recommandations d'architecture et des études comparatives. Quand un sujet dépasse le cadre technique pour toucher à la stratégie, Robert prend la main.

## Son rôle

Robert n'installe pas de serveurs et ne rédige pas de roadbooks — il **réfléchit**, **compare**, **recommande**.

```
Bureau Robert = votre consultant IT personnel
├── 🏛️ Architecture et choix techniques
├── 📊 Analyses comparatives (DeepSeek vs Gemini vs Ollama)
├── 💡 Recommandations stratégiques
├── 🔮 Roadmaps et évolutions
└── 📋 Rapports et documentations
```

## Les experts du bureau

| Expert | Compétence |
|:-------|:-----------|
| 🏛️ **Architecte** | Conception de systèmes, choix techniques |
| 🔒 **Sécurité** | Audit, conformité, risques |
| 📊 **Data** | Analyse de données, métriques, KPIs |
| 📋 **Gouvernance** | Processus, documentation, standards |
| 🔮 **Vision Stratégique** | Roadmap, évolutions, tendances |
| 📐 **Projet & Programme** | Planification, suivi, livrables |
| 🛡️ **Assurance Obligatoire** | INAMI, BCSS, eHealth, MyCareNet |

## Types d'analyses

Robert peut produire plusieurs formats selon le besoin :

| Type | Description | Exemple concret |
|:-----|:------------|:----------------|
| **Analyse stratégique** | Étude complète avec recommandations | "Quel LLM pour remplacer DeepSeek Flash ?" |
| **Benchmark** | Comparaison multi-critères | "Gemma 4 vs Qwen 3 vs Llama 4" |
| **Étude de faisabilité** | Options techniques + coûts | "Installer un GPU local pour l'IA" |
| **Audit** | Évaluation d'un existant | "Audit sécurité du serveur LEO" |

## Le processus de conseil

```
Demande ──→ Dispatch expert ──→ Production ──→ Croisement (si multi-experts)
                                                │
                                                ▼
                                          Synthèse ──→ Livrable ──→ Archivage
```

Pour les sujets complexes, plusieurs experts travaillent en parallèle :

```
"Quel LLM choisir pour mon serveur ?"
├── 📊 Data     : benchmark des modèles disponibles
├── 💰 Sophie   : analyse des coûts (TCO)
├── 🔒 Sécurité : données sensibles, vie privée
└── 🏛️ Synthèse : recommandation finale
```

## Exemple réel : remplacer DeepSeek Flash par du local

Le Bureau Robert a produit une **analyse comparative de 5 modèles open-source** pour remplacer DeepSeek Flash par un LLM local :

| Modèle | VRAM Q8 | Qualité estimée | Vitesse |
|:-------|:-------:|:---------------:|:-------:|
| **Gemma 4 26B MoE** ⭐ | 30 GB | ~85% | 🚀 80+ tok/s |
| Gemma 4 31B | 34 GB ❌ | ~90% | ~35 tok/s |
| Llama 4 Scout | 22 GB | ~75% | ~50 tok/s |
| Qwen 3 32B | 34 GB ❌ | ~80% | ~40 tok/s |

**Recommandation** : Gemma 4 26B MoE en Q8 sur RTX 3050 + RTX 3090 (32 GB total).

## Collaboration avec Sophie

Robert travaille main dans la main avec le **Bureau Sophie** (pilotage financier) :

```yaml
Projet: "Remplacer DeepSeek API par un LLM local"
Robert (technique):
  - Gemma 4 26B MoE Q8 = meilleur rapport qualité/VRAM
  - ~85% de la qualité DeepSeek Flash

Sophie (financier):
  - Coût actuel DeepSeek: ~720 €/an
  - Investissement GPU: ~800 € (RTX 3090)
  - ROI: 14 mois
  - Économie: ~600 €/an après ROI
  - 3 scenarii : pessimiste, réaliste, optimiste
```

## Voir aussi

- **Ch.10** : Architecture des bureaux
- **Ch.11** : Bureau Michel (implémente les recommandations)
- **Ch.15** : Bureau Sophie (pilotage financier)
