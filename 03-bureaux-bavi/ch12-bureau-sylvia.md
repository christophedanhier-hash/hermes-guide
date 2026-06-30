# Bureau Sylvia : les voyages

Le Bureau Sylvia est le spécialiste des **roadbooks camping-car**. Accessible via le bot Telegram [@bavi_leo_voyages_bot](https://t.me/bavi_leo_voyages_bot), il produit des itinéraires complets avec cartes interactives, budgets et conseils pratiques.

## Son rôle

Sylvia ne planifie pas seulement des itinéraires — elle construit des **carnets de voyage complets** qui servent de guide pendant le trajet.

```
Bureau Sylvia = votre agence de voyage IA
├── 🗺️ Roadbooks détaillés (étapes, distances, campings)
├── 📏 Calcul Haversine (distances à vol d'oiseau)
├── 🗺️ Cartes OSM interactives (folium + leaflet)
├── 💰 Budget prévisionnel (péages, carburant, campings)
├── 🏕️ Campings et hébergements
└── 💡 Astuces camping-car (ZTL, hauteur, aires)
```

## Les experts du bureau

| Expert | Rôle |
|:-------|:-----|
| 🧭 **Curateur d'Expériences** | Conception de l'itinéraire, choix des étapes |
| 🚐 **Navigateur Camping-Car** | Distances, routes, aires, contraintes CC |
| 📝 **Journaliste de Bord** | Rédaction du carnet de voyage |

## Structure d'un roadbook

Chaque roadbook suit un format strict, testé sur des dizaines de voyages :

### 1. Header + Contexte

```markdown
# 🇮🇹 Voyage Italie — Septembre/Octobre 2026

> 🗓️ 15/09/2026 → 05/10/2026 | 🚐 21 jours | 📏 ~3 500 km

## 👥 Contexte du voyage
|               |                                |
|:--------------|:-------------------------------|
| **Voyageurs** | Christophe, Sylvie + 🐕 Nala  |
| **Véhicule**  | Camping-car 8m × 2.3m (h: 3m) |
| **Équipement**| 2 vélos électriques           |
```

### 2. Coût du service

| Métrique | Valeur |
|:---------|------:|
| Sessions IA | 12 |
| Tokens consommés | 240K IN / 78K OUT |
| Coût DeepSeek réel | ~0,06 € |
| Frais de service | 2,50 € |
| **Total facturé** | **2,56 €** |

### 3. Itinéraire détaillé

| Jour | Date | Étape | Distance | KM cumulé | Nuit | Camping | Coût |
|:----:|:----:|:------|:--------:|:---------:|:----:|:--------|:----:|
| 1 | 15/09 | Sombreffe → Reims | 180 km | 180 km | 1 | Camping Reims | 30 € |

### 4. Distances Haversine

| Trajet | Distance vol d'oiseau |
|:-------|:--------------------:|
| Sombreffe → Reims | 165 km |
| Reims → Dijon | 210 km |
| ... | ... |
| **Total** | **~2 800 km** |

### 5. Carte interactive

Chaque roadbook inclut une carte OSM tracée avec folium :

```python
import folium

stops = [(50.5, 4.5, "Sombreffe"), (49.25, 4.03, "Reims"), ...]
m = folium.Map(location=[47.0, 6.0], zoom_start=6, tiles="OpenStreetMap")

# Tracé du parcours
route = [(s[0], s[1]) for s in stops]
folium.PolyLine(route, color="#e63946", weight=4, opacity=0.8).add_to(m)

# Marqueurs
for lat, lon, label in stops:
    folium.Marker([lat, lon], popup=label, 
                  icon=folium.Icon(color="red", icon="info-sign")).add_to(m)

m.save("docs/italie/carte-italie.html")
```

### 6-9. Campings, Budget, Notes, Résumé

Les sections suivantes détaillent les hébergements, le budget par poste, les astuces pratiques et un tableau récapitulatif.

## Publication

Les roadbooks sont publiés sur le **wiki Voyages** :

```
📦 github.com/christophedanhier-hash/voyages-wiki
🌐 https://christophedanhier-hash.github.io/voyages-wiki/
📁 /opt/data/voyages-wiki/docs/
```

Le workflow de publication est automatisé :

```bash
cd /opt/data/voyages-wiki
git add docs/NOM-DU-VOYAGE/
git commit -m "Ajout roadbook [destination] — [dates]"
git push origin main
# ~1 min → GitHub Pages déploie automatiquement
```

## Roadbooks existants

| Destination | Période | Statut |
|:------------|:-------:|:------:|
| 🇮🇹 **Italie** | Septembre/Octobre 2026 | 📝 En préparation |
| 🇻🇳🇱🇦🇰🇭 **Vietnam-Laos-Cambodge** | Janvier/Février 2027 | 📝 En préparation |
| 🇳🇴 **Scandinavie** | Août-Octobre 2026 | 📝 En préparation |
| 🇪🇸 **Andalousie** | Septembre-Octobre 2026 | 📝 En préparation |
| 🇫🇷 **Canet** | Juin 2026 | 📝 En préparation |

## Règles strictes

1. **Pas de Google Maps** — uniquement OpenStreetMap
2. **Pas de photos** dans le wiki — restent sur Polarsteps
3. **Distances Haversine** obligatoires entre chaque étape
4. **Dates belges** — format JJ/MM/AAAA
5. **Chaque modif** = mise à jour de la section coûts
6. **ZTL et hauteurs** — vérification obligatoire pour camping-car

## Voir aussi

- **Ch.11** : Bureau Michel (hébergement et publication)
- **Ch.26** : Crons de synchronisation
- **Annexe B** : Guide démarrage rapide
