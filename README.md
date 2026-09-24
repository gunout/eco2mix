# ⚡ Dashboard Expert ÉCO2mix

**Dashboard citoyen d'analyse du système électrique français** — Données officielles RTE en temps réel.

[![France](https://img.shields.io/badge/🇫🇷-France-000091?style=for-the-badge)](https://www.rte-france.com)
[![RTE éCO2mix](https://img.shields.io/badge/Source-RTE%20éCO2mix-E1000F?style=for-the-badge)](https://odre.opendatasoft.com)
[![Licence Ouverte](https://img.shields.io/badge/Licence-Ouverte%202.0-blue?style=for-the-badge)](https://www.etalab.gouv.fr/licence-ouverte-open-licence)
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)](https://developer.mozilla.org/fr/docs/Web/HTML)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/fr/docs/Web/JavaScript)
[![Plotly](https://img.shields.io/badge/Plotly.js-3F4F75?style=for-the-badge&logo=plotly&logoColor=white)](https://plotly.com/javascript/)
[![Cloudflare Workers](https://img.shields.io/badge/Cloudflare-Workers-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://workers.cloudflare.com)

---

## 📋 Description

Ce dashboard permet de **suivre et analyser en temps réel** le système électrique français grâce aux données ouvertes publiées par RTE (Réseau de Transport d'Électricité) via la plateforme ODRE (Open Data Réseaux Énergies).

Il est conçu pour être **accessible à tout citoyen** souhaitant comprendre :

- la consommation nationale d'électricité
- le mix de production par filière (nucléaire, éolien, solaire, hydraulique, gaz, etc.)
- l'intensité carbone de l'électricité produite
- les échanges transfrontaliers
- les prévisions de consommation de RTE

**Aucune donnée n'est simulée.** Tous les indicateurs proviennent de l'API officielle éCO2mix, rafraîchie toutes les 15 minutes.

---

## 📊 Fonctionnalités

### Indicateurs temps réel

- Consommation nationale (MW)
- Production totale et par filière
- Taux de CO₂ (gCO₂/kWh)
- Part renouvelable et bas-carbone
- Solde des échanges transfrontaliers

### Analyses statistiques (24h - 14 jours)

- Moyennes, écarts-types, minimums, maximums
- Volatilité de la consommation et des filières
- Ratio pic/min (flexibilité nécessaire)
- Autonomie énergétique

### Indicateurs de performance

- Taux de charge par filière (rapport capacité installée / production)
- Émissions cumulées de CO₂
- Part bas-carbone vs fossile
- Corrélations entre variables (matrice de Pearson)

### Graphiques interactifs

- Évolution temporelle par filière
- Mix de production (camembert + aires empilées)
- Comparaison France / Europe (intensité carbone)
- Prévisions J-1 vs réalisé
- Échanges par pays voisin

### Fonctionnalités avancées

- ✅ Filtres par période (24h, 48h, 7j, 14j)
- ✅ Sélection de filière principale
- ✅ Lissage par moyenne mobile
- ✅ Mode clair / sombre
- ✅ Export CSV et PNG
- ✅ Tableau triable
- ✅ Rafraîchissement automatique (15 min)

---

## 🔌 Sources de données

Toutes les données sont **publiques, gratuites et officielles**.

| Source | Description | URL |
|--------|-------------|-----|
| **RTE éCO2mix** | Données temps réel du système électrique français (production, consommation, CO₂, échanges) | [odre.opendatasoft.com](https://odre.opendatasoft.com) |
| **ODRE** | Open Data Réseaux Énergies — plateforme de publication des données RTE | [odre.opendatasoft.com](https://odre.opendatasoft.com) |
| **RTE** | Réseau de Transport d'Électricité — gestionnaire du réseau public français | [rte-france.com](https://www.rte-france.com) |

### API utilisée

**Dataset :** `eco2mix-national-tr` (temps réel national)

**Endpoint :** `https://odre.opendatasoft.com/api/explore/v2.0/catalog/datasets/eco2mix-national-tr/records`

**Paramètres :**

- `limit` : 100 maximum (pagination requise)
- `order_by` : `date_heure desc`
- `offset` : pagination

### Licence

Les données éCO2mix sont publiées sous **Licence Ouverte 2.0** (Etalab), qui autorise la réutilisation libre à condition de mentionner la source.

---

## 🏗️ Architecture technique

```
┌─────────────────────────────────────────────────────────────┐
│                    NAVIGATEUR (Client)                      │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              index.html (Dashboard)                  │   │
│  │  - HTML5 + CSS3 + JavaScript vanilla                │   │
│  │  - Plotly.js pour les graphiques                    │   │
│  │  - Calculs statistiques côté client                 │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                 │
│                           │ fetch()                         │
│                           ▼                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │         Cloudflare Worker (Proxy CORS)              │   │
│  │  - Reçoit les requêtes du navigateur                │   │
│  │  - Appelle l'API ODRE côté serveur                  │   │
│  │  - Renvoie les données avec les bons headers CORS   │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                 │
└───────────────────────────┼─────────────────────────────────┘
                            │
                            │ HTTPS
                            ▼
              ┌─────────────────────────────┐
              │    API ODRE (RTE éCO2mix)   │
              │  Données temps réel 15 min  │
              └─────────────────────────────┘
```

### Pourquoi un proxy CORS ?

Les navigateurs bloquent les appels directs vers des domaines externes (Same-Origin Policy). Le Worker Cloudflare fait office d'intermédiaire : il appelle l'API côté serveur et renvoie les données avec les en-têtes `Access-Control-Allow-Origin` appropriés.

---

## 🚀 Installation et utilisation

### Prérequis

- Un navigateur moderne (Chrome, Firefox, Safari, Edge)
- Un compte Cloudflare gratuit (pour le Worker)
- Un serveur local ou un hébergement statique

### Étape 1 : Déployer le Worker Cloudflare

1. Connectez-vous à [dash.cloudflare.com](https://dash.cloudflare.com)
2. Allez dans **Workers & Pages** → **Create Application** → **Create Worker**
3. Remplacez le code par défaut par le proxy CORS suivant :

```javascript
export default {
  async fetch(request) {
    const url = new URL(request.url);
    const apiUrl = url.searchParams.get("apiurl");

    if (request.method === "OPTIONS") {
      return new Response(null, {
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Methods": "GET,HEAD,POST,OPTIONS",
          "Access-Control-Allow-Headers": "*",
        },
      });
    }

    if (!apiUrl) {
      return new Response("Paramètre apiurl manquant", { status: 400 });
    }

    const response = await fetch(apiUrl);
    const newResponse = new Response(response.body, response);
    newResponse.headers.set("Access-Control-Allow-Origin", "*");

    return newResponse;
  },
};
```

4. Déployez et notez l'URL de votre Worker (ex: `https://votre-nom.workers.dev`)

### Étape 2 : Configurer le dashboard

Dans `index.html`, modifiez la constante `WORKER_URL` :

```javascript
const WORKER_URL = 'https://votre-nom.workers.dev/';
```

### Étape 3 : Lancer le dashboard

**Option A — Serveur local Python :**

```bash
python3 -m http.server 8000
```

Puis ouvrez [http://localhost:8000](http://localhost:8000)

**Option B — Hébergement statique :**
Déployez `index.html` sur GitHub Pages, Netlify, Vercel, ou tout autre hébergeur statique.

---

## 📈 Structure du projet

```
edf-dashboard/
├── index.html          # Dashboard complet (HTML + CSS + JS)
└── README.md           # Ce fichier
```

---

## 🔧 Personnalisation

### Modifier les capacités installées

Dans le JavaScript, la constante `CAPACITES_INSTALLEES` définit les puissances installées par filière (MW) :

```javascript
const CAPACITES_INSTALLEES = {
  nucleaire: 61370,
  eolien: 24500,
  solaire: 22000,
  hydraulique: 25600,
  gaz: 12400,
  charbon: 1800,
  fioul: 2900,
  bioenergies: 2100
};
```

Ces valeurs sont utilisées pour calculer les taux de charge.

### Modifier les couleurs

Les couleurs respectent la **charte de l'État français** :

```javascript
const COLORS = {
  bleu: '#000091',      // Bleu France
  rouge: '#E1000F',     // Rouge Marianne
  vert: '#18753c',      // Vert validation
  // ...
};
```

---

## 📝 Licence

Ce projet est distribué sous **Licence Ouverte 2.0** (Etalab).

Les données sources sont publiées par RTE sous Licence Ouverte 2.0.

---

## 🤝 Contribution

Les contributions sont bienvenues ! N'hésitez pas à :

- Signaler des bugs
- Proposer des améliorations
- Ajouter de nouveaux indicateurs
- Traduire l'interface

---

## 🔗 Liens utiles

- [RTE — Réseau de Transport d'Électricité](https://www.rte-france.com)
- [ODRE — Open Data Réseaux Énergies](https://odre.opendatasoft.com)
- [éCO2mix — Données temps réel](https://www.rte-france.com/eco2mix)
- [data.gouv.fr](https://www.data.gouv.fr)
- [CRE — Commission de Régulation de l'Énergie](https://www.cre.fr)
- [Enedis Open Data](https://www.enedis.fr)

---

## ⚠️ Avertissement

Ce dashboard est fourni **à titre informatif uniquement**. Les données proviennent de l'API publique éCO2mix de RTE et sont mises à jour toutes les 15 minutes. Malgré tout le soin apporté, des erreurs ou retards de publication peuvent survenir.

Pour toute décision importante (investissement, régulation, etc.), consultez directement les sources officielles.

---

**République Française**
*Liberté · Égalité · Fraternité*

---

<div align="center">

### 🇫🇷 Gunout · 2026

![Made in France](https://img.shields.io/badge/Made_in-France-002395?style=flat-square&labelColor=FFFFFF&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCA5MDAgNjAwIj48cmVjdCB3aWR0aD0iOTAwIiBoZWlnaHQ9IjYwMCIgZmlsbD0iIzAwMjM5NSIvPjxyZWN0IHdpZHRoPSI5MDAiIGhlaWdodD0iNDAwIiB5PSIxMDAiIGZpbGw9IiNmZmYiLz48cmVjdCB3aWR0aD0iOTAwIiBoZWlnaHQ9IjIwMCIgeT0iNDAwIiBmaWxsPSIjZWQyOTM5Ii8+PC9zdmc+)
![GitHub](https://img.shields.io/badge/GitHub-gunout-181717?style=flat-square&logo=github&logoColor=white)
![Year](https://img.shields.io/badge/2026-ED2939?style=flat-square&labelColor=FFFFFF)

<sub>© 2026 <strong>Gunout</strong> — Tous droits réservés.</sub>

</div>
