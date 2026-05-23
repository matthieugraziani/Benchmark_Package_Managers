# 📦 Benchmark : uv vs pip vs poetry

Comparaison de performance entre les trois principaux gestionnaires de paquets Python : **uv**, **pip** et **poetry** — sous la forme d'un Jupyter Notebook interactif.

---

## 🎯 Objectif

Mesurer et visualiser les différences de vitesse entre :

| Outil | Description |
|---|---|
| **pip** | Gestionnaire officiel Python, universel |
| **uv** | Gestionnaire ultra-rapide écrit en Rust (Astral) |
| **poetry** | Gestionnaire orienté projets, lock file et publication PyPI |

---

## 🔬 Benchmarks réalisés

| # | Benchmark | Paquets testés | Méthode |
|---|---|---|---|
| 1 | Installation sans cache (cold) | `requests` | venv isolé, `--no-cache` |
| 2 | Installation avec cache chaud | `requests` | 2ème install, cache peuplé |
| 3 | Installation de paquets lourds | `numpy` + `pandas` + `scipy` | venv isolé, `--no-cache` |
| 4 | Résolution de dépendances seule | `django` | `--dry-run` / `poetry lock` |

Chaque benchmark s'exécute dans un **répertoire temporaire isolé** pour garantir la reproductibilité et éviter toute interférence entre les outils.

---

## 📊 Résultats typiques

```
======================================================================
 RÉSULTATS COMPLETS
======================================================================
Benchmark                             pip           uv       poetry   uv speedup
----------------------------------------------------------------------
Install simple (requests)           1.76 s        54 ms     1.10 s      32.6x
Install avec cache                   556 ms         8 ms      1.06 s      69.5x
Install lourd (np+pd+sc)            14.16 s        56 ms     1.75 s     252.9x
Résolution dépendances               332 ms        50 ms     3.40 s       6.6x
======================================================================

🏆 Speedup moyen uv vs pip    : 90.4x plus rapide
🏅 Speedup moyen poetry vs pip: 0.7x
```

> Les résultats varient selon la machine, la connexion réseau et l'état du cache.

---

## 🗂️ Structure du projet

```
benchmark-package-managers/
├── benchmark_package_managers.ipynb   # Notebook principal (vierge)
├── benchmark_results.png              # Graphique de sortie
├── requirements.txt                   # Dépendances pip
├── pyproject.toml                     # Config poetry / outils
└── README.md
```

---

## 🚀 Installation & lancement

### Prérequis

- Python 3.9+
- [uv](https://docs.astral.sh/uv/) installé
- [poetry](https://python-poetry.org/) installé

#### Installer uv

```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

#### Installer poetry

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

---

### Option A — avec pip

```bash
git clone https://github.com/votre-user/benchmark-package-managers.git
cd benchmark-package-managers

python -m venv .venv
source .venv/bin/activate          # Windows : .venv\Scripts\activate

pip install -r requirements.txt
jupyter notebook benchmark_package_managers.ipynb
```

### Option B — avec uv (recommandé)

```bash
git clone https://github.com/votre-user/benchmark-package-managers.git
cd benchmark-package-managers

uv venv
source .venv/bin/activate

uv pip install -r requirements.txt
jupyter notebook benchmark_package_managers.ipynb
```

### Option C — avec poetry

```bash
git clone https://github.com/votre-user/benchmark-package-managers.git
cd benchmark-package-managers

poetry install
poetry run jupyter notebook benchmark_package_managers.ipynb
```

---

## 📈 Visualisations générées

Le notebook produit automatiquement deux graphiques côte à côte :

- **Barres groupées** — durée absolue par benchmark et par outil (moins = mieux)
- **Speedup relatif** — combien de fois uv et poetry sont plus rapides que pip

Le fichier `benchmark_results.png` est sauvegardé dans le répertoire courant.

---

## 🔍 Détails techniques

### Isolation des benchmarks

Chaque mesure crée un **répertoire temporaire** (`tempfile.TemporaryDirectory`) contenant :
- un venv dédié pour pip
- un cache séparé (`UV_CACHE_DIR`) pour uv
- un projet poetry complet avec `pyproject.toml` pour poetry

Cela garantit qu'aucun cache ou état résiduel ne biaise les mesures.

### Détection des outils dans Jupyter

Le PATH du kernel Jupyter peut différer du shell. Le notebook enrichit automatiquement `os.environ["PATH"]` avec les emplacements courants (`~/.local/bin`, `~/.cargo/bin`, `/usr/local/bin`, etc.) et utilise `python -m pip` pour pip afin d'assurer la compatibilité sur tous les environnements.

---

## 🗒️ Guide de choix

| Cas d'usage | Recommandation |
|---|---|
| CI/CD, Docker, scripts | ⚡ **uv** |
| Projets avec lock file strict | 🎯 **poetry** |
| Compatibilité maximale / legacy | 🔧 **pip** |
| Développement itératif rapide | ⚡ **uv** |
| Publication sur PyPI | 🎯 **poetry** |

---

## 📚 Ressources

- [uv — documentation officielle](https://docs.astral.sh/uv/)
- [pip — documentation officielle](https://pip.pypa.io/)
- [poetry — documentation officielle](https://python-poetry.org/)
- [PEP 517 — Build system interface](https://peps.python.org/pep-0517/)

---

## 📄 Licence

MIT — libre d'utilisation et de modification.
