---
title: Projet 1 — Site vitrine pour un artisan local
tags: [HTML-NO CODE]

---

# Projet 1 — Site vitrine pour un artisan local
---

## 1. Préparer l'environnement

1. Ouvrir **Visual Studio Code**.
2. Créer un dossier `site-artisan` sur le bureau ou dans les documents.
3. Dans VS Code : **Fichier → Ouvrir le dossier** → choisir `site-artisan`.
4. Installer l'extension **Live Server** (icône Extensions à gauche, rechercher « Live Server », cliquer sur *Install*). Elle permet de voir le site se mettre à jour en direct.

## 2. Créer les fichiers

Dans l'explorateur de VS Code, créer :

```
site-artisan/
├── index.html
├── style.css
└── images/        (facultatif : logo, photos)
```

## 3. Structurer le contenu (plan du site)

Choisir le métier (ex. électricien) et lister le contenu :

| Section | Contenu |
|---|---|
| **Accueil** | Nom de l'entreprise, slogan, courte présentation |
| **Services** | 3 à 4 prestations (ex. dépannage, installation, mise aux normes) |
| **Contact** | Téléphone, email, adresse, horaires |

## 4. Écrire le HTML sémantique

Dans `index.html`, taper `!` puis `Tab` pour générer la base, puis compléter :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dupont Électricité — Électricien à Genève</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>

  <header>
    <h1>Dupont Électricité</h1>
    <nav>
      <a href="#accueil">Accueil</a>
      <a href="#services">Services</a>
      <a href="#contact">Contact</a>
    </nav>
  </header>

  <main>
    <section id="accueil">
      <h2>Votre électricien de confiance</h2>
      <p>Dépannage, installation et rénovation électrique depuis 15 ans.</p>
    </section>

    <section id="services">
      <h2>Nos services</h2>
      <article>
        <h3>Dépannage</h3>
        <p>Intervention rapide en cas de panne.</p>
      </article>
      <article>
        <h3>Installation</h3>
        <p>Tableaux, prises, éclairage.</p>
      </article>
      <article>
        <h3>Mise aux normes</h3>
        <p>Mise en conformité de votre logement.</p>
      </article>
    </section>

    <section id="contact">
      <h2>Contact</h2>
      <p>Téléphone : 022 000 00 00</p>
      <p>Email : contact@dupont-elec.ch</p>
      <p>Horaires : lundi–vendredi, 8h–18h</p>
    </section>
  </main>

  <footer>
    <p>&copy; 2026 Dupont Électricité</p>
  </footer>

</body>
</html>
```

> 💡 Balises sémantiques à utiliser : `header`, `nav`, `main`, `section`, `article`, `footer`. Éviter les `div` quand une balise sémantique existe.

![Capture d'écran 2026-11-02 091735](https://hackmd.io/_uploads/B15HPaHTGx.png)
![Capture d'écran 2026-11-02 091748](https://hackmd.io/_uploads/HyRSD6STMx.png)


## 5. Tester le rendu

- Clic droit dans `index.html` → **Open with Live Server**.
- Le site s'ouvre dans le navigateur et se rafraîchit à chaque sauvegarde (`Ctrl + S`).

![Capture d'écran 2026-11-02 091919](https://hackmd.io/_uploads/rkL5vTH6fg.png)

## 6. Styliser avec CSS

Dans `style.css` :

```css
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: Arial, sans-serif;
  line-height: 1.6;
  color: #333;
}

header {
  background-color: #1e3a5f;
  color: white;
  padding: 20px;
  text-align: center;
}

nav a {
  color: white;
  text-decoration: none;
  margin: 0 10px;
}

nav a:hover {
  text-decoration: underline;
}

section {
  padding: 40px 20px;
  max-width: 900px;
  margin: auto;
}

h2 {
  color: #1e3a5f;
  margin-bottom: 15px;
}

#services article {
  background-color: #f2f5f9;
  padding: 15px;
  margin-bottom: 15px;
  border-radius: 8px;
}

footer {
  background-color: #1e3a5f;
  color: white;
  text-align: center;
  padding: 15px;
}
```
![Capture d'écran 2026-11-02 091955](https://hackmd.io/_uploads/SkuTvaS6Ge.png)
![Capture d'écran 2026-11-02 092004](https://hackmd.io/_uploads/rys6Ppr6ze.png)


## 7. Rendre le site responsive (mobile)

Ajouter à la fin de `style.css` :

```css
/* Affichage des services en colonnes sur grand écran */
@media (min-width: 768px) {
  #services {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 15px;
  }
  #services h2 {
    grid-column: 1 / -1;
  }
}

/* Petits écrans */
@media (max-width: 767px) {
  nav a {
    display: block;
    margin: 8px 0;
  }
}
```

**Tester le mobile :** dans le navigateur, `F12` → icône « mobile / tablette » (Ctrl + Maj + M) → choisir un téléphone.

![Capture d'écran 2026-11-02 092121](https://hackmd.io/_uploads/ByvGOprpzx.png)


## 8. Tester dans le navigateur

Ouvrir le site dans le navigateur (ex. Chrome et Firefox ou Edge) et vérifier :

-  Les 3 sections s'affichent correctement
-  Les liens du menu fonctionnent
-  Les couleurs et polices sont identiques
-  Le site est lisible sur mobile

## 9. Captures d'écran

- **Desktop :** page complète dans le navigateur (`Win + Maj + S` sous Windows).
![Capture d'écran 2026-11-02 092039](https://hackmd.io/_uploads/HkJluaH6fg.png)

- **Mobile :** mode appareil de `F12`, puis capture.
![Capture d'écran 2026-11-02 092157](https://hackmd.io/_uploads/HysNdarTMg.png)