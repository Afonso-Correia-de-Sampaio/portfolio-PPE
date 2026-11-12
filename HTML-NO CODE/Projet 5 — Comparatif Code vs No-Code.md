---
title: Projet 5 — Comparatif Code vs No-Code
tags: [HTML-NO CODE]

---

# Projet 5 — Comparatif Code vs No-Code
---

## 1. Objectif

Un client hésite entre un site codé sur-mesure et un outil No-Code.
Je construis **la même page** avec les deux méthodes, je les compare, puis je rédige une **recommandation argumentée**.

---

## 2. Préparation

1. Récupérer le **cas client** donné par le formateur (activité, besoins, budget, délai).
2. Définir le **contenu de la page** (identique pour les deux versions) :
   - Un titre / logo
   - Une section « À propos » (court texte)
   - Une section « Services » (3 blocs)
   - Une image
   - Un bouton ou un lien de contact
   - Un pied de page
3. Préparer les textes et images dans un dossier `projet5/`.
4. Créer un **tableau de relevé** pour noter le temps passé

---

## 3. Version 1 — HTML/CSS avec Visual Studio Code

### 3.1 Créer le projet
1. Créer le dossier `projet5-code/`.
2. Ouvrir VS Code → **Fichier > Ouvrir le dossier** → choisir `projet5-code`.
3. Créer les fichiers :
   - `index.html`
   - `style.css`

### 3.2 Chronométrer
⏱️ Noter l'heure de début.

### 3.3 Écrire le HTML (`index.html`)
Dans VS Code, taper `!` puis **Tab** pour générer la structure de base. Compléter :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nom du client</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header>
    <h1>Nom du client</h1>
  </header>

  <main>
    <section id="apropos">
      <h2>À propos</h2>
      <p>Texte de présentation.</p>
      <img src="images/photo.jpg" alt="Description de l'image">
    </section>

    <section id="services">
      <h2>Services</h2>
      <div class="cartes">
        <article><h3>Service 1</h3><p>Description</p></article>
        <article><h3>Service 2</h3><p>Description</p></article>
        <article><h3>Service 3</h3><p>Description</p></article>
      </div>
    </section>

    <section id="contact">
      <h2>Contact</h2>
      <a class="bouton" href="mailto:contact@exemple.ch">Nous écrire</a>
    </section>
  </main>

  <footer>
    <p>© 2026 Nom du client</p>
  </footer>
</body>
</html>
```

### 3.4 Écrire le CSS (`style.css`)

```css
* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: Arial, sans-serif;
  line-height: 1.6;
  color: #222;
}

header, footer {
  background: #1f3a5f;
  color: white;
  text-align: center;
  padding: 1.5rem;
}

section { padding: 2rem 1rem; max-width: 900px; margin: auto; }

img { max-width: 100%; height: auto; }

.cartes {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 1rem;
}

article { border: 1px solid #ddd; padding: 1rem; border-radius: 8px; }

.bouton {
  display: inline-block;
  background: #e07a1f;
  color: white;
  padding: 0.7rem 1.4rem;
  border-radius: 6px;
  text-decoration: none;
}
```

### 3.5 Tester
1. Installer l'extension **Live Server** (si pas déjà fait).
2. Clic droit sur `index.html` → **Open with Live Server**.
3. Vérifier l'affichage sur ordinateur et en mode mobile (F12 > icône téléphone).

### 3.6 Finir
⏱️ Noter l'heure de fin → **temps total Version 1**.

![Capture d'écran 2026-11-11 091157](https://hackmd.io/_uploads/HJGvQiZRze.png)

---

## 4. Version 2 — No-Code avec Carrd

### 4.1 Créer le projet
1. Aller sur [carrd.co](https://carrd.co) et créer un compte (gratuit).
2. Cliquer sur **+ New Site** → choisir **Start from scratch** (ou un modèle simple).

### 4.2 Chronométrer
⏱️ Noter l'heure de début.

### 4.3 Construire la page
Avec le bouton **+** (Add Element), ajouter dans l'ordre :
1. **Heading** → nom du client
2. **Text** → « À propos » + texte
3. **Image** → la même image que la version 1
4. **Container** ou 3 blocs **Text** → les 3 services
5. **Button** → lien `mailto:contact@exemple.ch`
6. **Text** → pied de page

### 4.3 Personnaliser
- Régler les **couleurs** et la **police** pour se rapprocher de la version 1.
- Vérifier l'affichage **mobile** avec l'icône téléphone/ordinateur en haut.

### 4.4 Publier (optionnel)
- Le plan gratuit permet une adresse en `.carrd.co`.
- Noter ce qui est **limité ou payant** (domaine perso, formulaire, etc.).

### 4.5 Finir
⏱️ Noter l'heure de fin → **temps total Version 2**.
![Capture d'écran 2026-11-11 091836](https://hackmd.io/_uploads/By17BjWCzx.png)

---

## 5. Observer et mesurer

### Temps de réalisation

| | HTML/CSS (VS Code) | Carrd |
|---|---|---|
| Début |8h45|9h12 |
| Fin |9h11|9h18 |
| **Temps total** |26mins |6mins |

---

## 6. Tableau comparatif

| Critère | HTML/CSS (VS Code) | No-Code (Carrd) |
|---|---|---|
| **Temps de réalisation** |26mins|6mins |
| **Coût de départ** | Gratuit (outils) mais temps de dev | Gratuit / environ 19 $ par an (plan Pro) |
| **Flexibilité / personnalisation** |plus flexible et personnalisable |moins flexible et personnalisable |
| **Compétences nécessaires** |html et css |aucune |

---