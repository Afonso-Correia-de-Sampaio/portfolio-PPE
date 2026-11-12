---
title: Projet 4 — Refonte responsive d'un site existant
tags: [HTML-NO CODE]

---

# Projet 4 — Refonte responsive d'un site existant
---

## Contexte et objectif

Le formateur fournit un site qui **s'affiche mal sur mobile**. Mon travail : le rendre **responsive** (il doit s'adapter à toutes les tailles d'écran) sans casser la version ordinateur.

---

## Outils nécessaires

| Outil | Utilité |
|---|---|
| **Visual Studio Code** | Modifier le code |
| **Live Server** (extension VS Code) | Voir le site en direct, il se rafraîchit à chaque sauvegarde |
| **Chrome DevTools** (touche `F12`) | Simuler mobile / tablette / ordinateur |

---

## Étape 1 — Préparer le projet

1. Télécharger le site fourni par le formateur et **le dézipper** si besoin.
2. **Faire une copie de sauvegarde** du dossier (ex. `site-original`). Elle servira à comparer "avant / après".
3. Ouvrir VS Code → **Fichier → Ouvrir le dossier** → choisir le dossier du site.
4. Installer l'extension **Live Server** : icône Extensions (`Ctrl+Shift+X`) → chercher *Live Server* → **Install**.
5. Clic droit sur `index.html` → **Open with Live Server**.


---

## Étape 2 — Analyser le site sur différentes tailles d'écran

1. Dans le navigateur, ouvrir le site (Live Server) puis appuyer sur `F12`.
2. Cliquer sur l'icône **"Toggle device toolbar"** (petit téléphone/tablette) ou `Ctrl+Shift+M`.
3. Tester ces 3 tailles :

| Appareil | Largeur |
|---|---|
| 📱 Mobile | **375 px** |
| 📱 Tablette | **768 px** |
| 💻 Ordinateur | **1280 px** |

4. **Prendre les captures "AVANT"** pour chaque taille (`Ctrl+Shift+P` dans DevTools → taper *screenshot* → *Capture screenshot*).
5. Les ranger dans un dossier `captures/` avec des noms clairs : `avant-375.png`, `avant-768.png`, `avant-1280.png`.

---

## Étape 3 — Identifier les problèmes

Pendant l'analyse, noter dans un tableau ce qui ne va pas :

| # | Problème observé | Taille d'écran | Élément concerné |
|---|---|---|---|
| 1 | Ex. : l'image dépasse de l'écran | 375 px | `img` de la bannière |
| 2 | Ex. : texte trop petit | 375 px | paragraphes |
| 3 | Ex. : menu coupé / scroll horizontal | 375 px | `nav` |

**Problèmes courants à chercher**
- Scroll horizontal (la page dépasse sur la droite)
- Images trop grandes
- Texte illisible (trop petit) ou qui déborde
- Colonnes côte à côte qui s'écrasent
- Boutons ou liens trop petits pour un doigt
- Largeurs fixes en `px`
![Capture d'écran 2026-11-09 091856](https://hackmd.io/_uploads/SyH-G-J0zg.png)

---

## Étape 4 — Corriger avec le CSS responsive

### 4.1 Vérifier la balise viewport (dans `<head>` du HTML)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

> Sans elle, le mobile affiche le site en "version bureau réduite".

### 4.2 Règles de base dans le CSS

```css
* {
  box-sizing: border-box;
}

img {
  max-width: 100%;
  height: auto;
}
```

### 4.3 Remplacer les largeurs fixes

```css
/* ❌ Avant */
.container { width: 1000px; }

/* ✅ Après */
.container {
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
  padding: 0 1rem;
}
```

### 4.4 Flexbox — des éléments qui passent à la ligne

```css
.cartes {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.carte {
  flex: 1 1 250px; /* minimum 250px, puis s'adapte */
}
```

### 4.5 Grid — une grille qui s'adapte seule

```css
.galerie {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}
```

### 4.6 Media queries — changer le style selon l'écran

```css
/* Tablette et moins */
@media (max-width: 768px) {
  .menu {
    flex-direction: column;
  }
}

/* Mobile */
@media (max-width: 480px) {
  h1 { font-size: 1.6rem; }
  p  { font-size: 1rem; }
  .colonnes { flex-direction: column; }
}
```

### 4.7 Unités relatives

- Utiliser `rem`, `%`, `vw` à la place de `px` pour les textes et les espaces.
- Garder une taille de texte d'au moins **16 px** (`1rem`) sur mobile.

> ✅ **Astuce** : corriger **un problème à la fois**, sauvegarder (`Ctrl+S`), vérifier dans le navigateur, puis passer au suivant.
---

## Étape 5 — Documenter les corrections

Compléter ce tableau, à partir de la liste des problèmes de l'étape 3 :

| # | Problème | Correction apportée | Fichier / ligne |
|---|---|---|---|
| 1 | Image qui dépasse | `max-width: 100%; height: auto;` | `style.css` |
| 2 | Menu coupé sur mobile | `flex-wrap` + media query `max-width: 768px` | `style.css` |
| 3 | Texte trop petit | `font-size: 1rem` sur mobile | `style.css` |

### Captures avant / après

| Taille | Avant | Après |
|---|---|---|
| 375 px |![Capture d'écran 2026-11-09 090612](https://hackmd.io/_uploads/B1HW1-yAMg.png)|![Capture d'écran 2026-11-09 091346](https://hackmd.io/_uploads/r1cTx-y0fx.png)|
| 768 px | ![Capture d'écran 2026-11-09 090647](https://hackmd.io/_uploads/SkP8y-k0Gx.png)|![Capture d'écran 2026-11-09 091520](https://hackmd.io/_uploads/SJsQZWk0Gx.png)|
| 1280 px | ![Capture d'écran 2026-11-09 090757](https://hackmd.io/_uploads/HykuJWkRze.png)|![Capture d'écran 2026-11-09 091650](https://hackmd.io/_uploads/SJGt-byAMg.png)|

---