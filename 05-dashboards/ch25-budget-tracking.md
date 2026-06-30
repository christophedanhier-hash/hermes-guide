# Budget et tracking DeepSeek

LEO coûte environ 1,50 € par mois à faire fonctionner. Voici comment suivre et maîtriser ce budget.

## Le coût réel de LEO

```yaml
Budget mensuel LEO:
  DeepSeek V4 Flash (quotidien):  ~1,00 €
  DeepSeek V4 Pro (analyses):     ~0,50 €
  Gemini (fallback):                0 € (gratuit)
  Ollama (classification):          0 € (local)
  GitHub Pages (hébergement):       0 € (gratuit)
  n8n (workflows):                  0 € (self-hosted)
  Total:                           ~1,50 €
```

Le secret de ce coût ridicule : **Ollama pour le gratuit** (classification emails), **Flash pour le quotidien** (0,05 €/jour), **Pro seulement pour le complexe** (0,10 €/tâche).

## Triple ventilation

```python
# Ne pas se fier aux logs DeepSeek
# Mesurer le delta de balance
vrai_coût = solde_avant - solde_après

# Ventiler par usage
coûts = {
    "deepseek_flash":  0.05 * jours,    # Usage quotidien
    "deepseek_pro":    0.10 * analyses, # Analyses complexes
    "ollama":           0.00,           # Gratuit
    "gemini":           0.00,           # Gratuit (fallback)
}
```

## Dashboard budget

```markdown
| Métrique              | Valeur          |
|:---------------------|:----------------|
| Solde actuel         | $60.31          |
| Dépense totale       | $0.41           |
| Moyenne quotidienne  | $0.03           |
| Jours restants       | 2 315 (>6 ans) |
| Coût 14 jours        | $11.47          |
| Tendance             | Stable 📊       |
```

## Alertes

```yaml
Seuils d'alerte:
  Solde < 10€:  🔴 Notification immédiate
  Dépense > 1€/jour: 🟡 Vérifier si anomalie
  Erreur API:        🟡 Fallback Gemini automatique
```

## Comparaison des providers

| Provider | Coût IN (1M tokens) | Coût OUT (1M tokens) | Autonomie ($60) |
|:---------|:-------------------:|:--------------------:|:---------------:|
| DeepSeek Flash | $0.14 | $0.28 | >6 ans |
| DeepSeek Pro | $1.50 | $5.00 | ~2 mois |
| Gemini Flash | **0 €** | **0 €** | ∞ (quotas gratuits) |
| Ollama | **0 €** | **0 €** | ∞ (local) |
