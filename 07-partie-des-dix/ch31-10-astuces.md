# 10 astuces pour ne pas galérer

Basées sur les vrais pièges rencontrés par LEO pendant sa construction. Évitez-les, vous gagnerez des heures.

## 1. `hermes` n'est pas dans le PATH

```bash
# ❌ Ça ne marche pas
hermes chat

# ✅ Ça marche
/opt/hermes/.venv/bin/hermes chat

# ✅ Solution : créer un alias
alias hermes='/opt/hermes/.venv/bin/hermes'
```

## 2. Les tokens .env sont fragiles

Le moindre caractère spécial (`$`, `!`, `&`) dans un fichier `.env` peut casser le gateway. Toujours utiliser des guillemets :

```bash
# ❌ Problème si le token contient $ ou !
TELEGRAM_BOT_TOKEN=abc$def

# ✅ Sécurisé
TELEGRAM_BOT_TOKEN="abc$def"
```

## 3. s6-svstat DOWN ne veut pas dire mort

```bash
# ❌ s6 dit DOWN mais le processus tourne
s6-svstat /run/service/gateway-default
# → down (faux négatif)

# ✅ Vérifier les processus
ps aux | grep hermes.*gateway
```

`s6-svstat` peut retourner `down` même quand le processus est actif. Toujours vérifier avec `ps`.

## 4. Le contexte DeepSeek est limité à 128K tokens

Si votre conversation ou document dépasse 128K tokens, DeepSeek plante.

```yaml
# Solution : fallback Gemini (1M tokens, gratuit)
fallback_providers: '[{"provider": "gemini", "model": "gemini-2.5-flash"}]'
```

Gemini a un contexte 8 fois plus grand — parfait pour les longs documents.

## 5. Ne jamais réappliquer les labels Gmail

```python
# ❌ DANGER : réappliquer les labels en masse
# → Des milliers d'emails re-classifiés, pagaille assurée

# ✅ Règle d'or
if email.already_labeled:
    pass  # Ne pas toucher
```

Les labels Gmail sont appliqués une seule fois. Passé ce cap, on n'y touche plus.

## 6. Budget DeepSeek : mesurer le delta, pas les logs

Les logs DeepSeek ne reflètent pas toujours le coût réel. Utilisez le **delta de balance** :

```python
# ✅ Fiable
vrai_coût = solde_avant - solde_après

# ❌ Pas fiable
cout_logs = somme(prix_token * tokens)
```

## 7. Les symlinks dans scripts/ sont refusés

Hermes v0.17.0 refuse les liens symboliques dans le dossier `scripts/`.

```bash
# ❌ Symlink refusé
ln -s /opt/data/scripts/backup.py /opt/data/profiles/leo-copilot/scripts/backup.py

# ✅ Copie réelle
cp /opt/data/scripts/backup.py /opt/data/profiles/leo-copilot/scripts/backup.py
```

## 8. Toujours vérifier avant de livrer

```bash
# Règle absolue : vérifier AVANT de dire "c'est fait"
curl -s -o /dev/null -w "%{http_code}" https://mon-site.com
# → 200 ✅

grep "version: 2.0" /opt/data/config.yaml
# → trouvé ✅
```

Ne jamais faire confiance à "ça devrait marcher". Vérifiez.

## 9. Un backup qui n'est pas testé n'existe pas

```bash
# Vérifier le contenu du backup
tar -tzf /opt/data/backups/leo-backup-*.tar.gz | head -20

# Simuler une restauration
tar -xzf /opt/data/backups/leo-backup-*.tar.gz -C /tmp/test-restore
```

Testez vos backups. Le jour où vous en avez besoin, il est trop tard pour découvrir qu'ils sont vides.

## 10. Documentez vos pièges

Quand vous passez 2 heures à résoudre un problème, **notez-le dans un skill**. La prochaine fois, ce sera 5 minutes.

```markdown
## Pièges
- Le port 5678 de n8n n'est accessible que via Tailscale
- Après un kill du gateway, attendre 30s avant de relancer
- La clé API DeepSeek est dans le .env du profil, pas le global
```

La documentation, c'est comme l'humour : c'est mieux avec un peu d'avance.
