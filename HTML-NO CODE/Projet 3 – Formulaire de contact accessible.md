---
title: Projet 3 – Formulaire de contact accessible
tags: [HTML-NO CODE]

---

# Projet 3 – Formulaire de contact accessible
---

## 🎯 Objectif

Créer un formulaire de contact HTML pour une association, avec :
- une structure HTML correcte (sémantique)
- un style CSS lisible (bons contrastes)
- une validation native HTML5
- une navigation possible **uniquement au clavier**

---

## 1. Préparer le projet

1. Créer un dossier `formulaire-accessible` sur le bureau.
2. Ouvrir **Visual Studio Code** → `Fichier > Ouvrir le dossier` → choisir le dossier.
3. Créer 2 fichiers :
   - `index.html`
   - `style.css`
4. (Conseil) Installer l'extension **Live Server** pour voir le résultat en direct :
   clic droit sur `index.html` → **Open with Live Server**.

---

## 2. Structurer le formulaire (HTML)

Dans `index.html`, taper `!` puis `Tab` pour générer la base, puis compléter :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Contact – Association</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <main>
    <h1>Contactez-nous</h1>
    <p>Les champs marqués d'une * sont obligatoires.</p>

    <form action="#" method="post">

      <fieldset>
        <legend>Vos coordonnées</legend>

        <label for="nom">Nom *</label>
        <input type="text" id="nom" name="nom" required autocomplete="family-name">

        <label for="prenom">Prénom *</label>
        <input type="text" id="prenom" name="prenom" required autocomplete="given-name">

        <label for="email">Adresse e-mail *</label>
        <input type="email" id="email" name="email" required autocomplete="email">

        <label for="tel">Téléphone</label>
        <input type="tel" id="tel" name="tel"
               pattern="[0-9 +]{10,15}"
               title="10 à 15 chiffres (espaces et + autorisés)"
               autocomplete="tel">
      </fieldset>

      <fieldset>
        <legend>Votre message</legend>

        <label for="sujet">Sujet *</label>
        <select id="sujet" name="sujet" required>
          <option value="">-- Choisir un sujet --</option>
          <option value="info">Demande d'information</option>
          <option value="benevole">Devenir bénévole</option>
          <option value="don">Faire un don</option>
        </select>

        <label for="message">Message *</label>
        <textarea id="message" name="message" rows="6" required minlength="10"></textarea>
      </fieldset>

      <fieldset>
        <legend>Préférence de réponse</legend>
        <input type="radio" id="rep-mail" name="reponse" value="mail" checked>
        <label for="rep-mail">Par e-mail</label>

        <input type="radio" id="rep-tel" name="reponse" value="tel">
        <label for="rep-tel">Par téléphone</label>
      </fieldset>

      <input type="checkbox" id="rgpd" name="rgpd" required>
      <label for="rgpd">J'accepte d'être recontacté(e) *</label>

      <button type="submit">Envoyer</button>
    </form>
  </main>
</body>
</html>
```

### ✅ Points d'accessibilité à retenir
- Chaque champ a un `<label for="...">` lié à l'`id` du champ
- `<fieldset>` + `<legend>` regroupent les champs liés
- `lang="fr"` sur la balise `<html>`
- `autocomplete` aide à remplir automatiquement
- Un seul `<h1>`, et le contenu est dans `<main>`
![Capture d'écran 2026-11-06 090900](https://hackmd.io/_uploads/BkQ4i-spzx.png)
![Capture d'écran 2026-11-06 090909](https://hackmd.io/_uploads/ByvVjWs6Gx.png)

---

## 3. Valider avec HTML5 (natif)

Déjà intégré dans le code ci-dessus :

| Attribut | Rôle |
|---|---|
| `required` | champ obligatoire |
| `type="email"` | vérifie le format d'une adresse e-mail |
| `type="tel"` + `pattern` | vérifie le format du numéro |
| `minlength="10"` | message d'au moins 10 caractères |

**Test :** cliquer sur « Envoyer » avec des champs vides ou faux → le navigateur affiche un message d'erreur.
![Capture d'écran 2026-11-06 085842](https://hackmd.io/_uploads/ryOp_Wspfl.png)

---

## 4. Appliquer un style accessible (CSS)

Dans `style.css` :

```css
body {
  font-family: Arial, sans-serif;
  font-size: 1.125rem;       /* texte assez grand */
  line-height: 1.6;
  color: #1a1a1a;            /* texte foncé */
  background-color: #ffffff; /* fond clair → fort contraste */
  margin: 0;
  padding: 1rem;
}

main {
  max-width: 600px;
  margin: 0 auto;
}

fieldset {
  border: 2px solid #333;
  border-radius: 6px;
  margin-bottom: 1.5rem;
  padding: 1rem;
}

legend {
  font-weight: bold;
  padding: 0 0.5rem;
}

label {
  display: block;
  margin-top: 1rem;
  font-weight: bold;
}

input[type="radio"] + label,
input[type="checkbox"] + label {
  display: inline;
  font-weight: normal;
}

input[type="text"],
input[type="email"],
input[type="tel"],
select,
textarea {
  width: 100%;
  padding: 0.6rem;
  font-size: 1rem;
  border: 2px solid #333;
  border-radius: 4px;
  box-sizing: border-box;
}

/* Focus bien visible pour la navigation clavier */
input:focus,
select:focus,
textarea:focus,
button:focus {
  outline: 3px solid #0050d0;
  outline-offset: 2px;
}

button {
  margin-top: 1.5rem;
  padding: 0.8rem 1.5rem;
  font-size: 1.1rem;
  background-color: #0050d0;
  color: #ffffff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #003a99;
}
```
---
![Capture d'écran 2026-11-06 090933](https://hackmd.io/_uploads/SJNIoZi6Mx.png)
![Capture d'écran 2026-11-06 090942](https://hackmd.io/_uploads/H1PUsboTfl.png)


## 5. Tester au clavier uniquement

⚠️ **Ne pas utiliser la souris !**

| Touche | Action |
|---|---|
| `Tab` | champ suivant |
| `Shift + Tab` | champ précédent |
| `Espace` | cocher une case / ouvrir une liste |
| `Flèches ↑ ↓` | changer l'option (boutons radio, liste) |
| `Entrée` | envoyer le formulaire |

### Check-list du test
-  Je peux atteindre **tous** les champs avec `Tab`
-  L'ordre de navigation est logique (haut → bas)
-  Le champ actif est **bien visible** (contour bleu)
-  Je peux cocher la case et choisir dans la liste sans souris
-  Je peux envoyer le formulaire avec `Entrée`
-  Les messages d'erreur s'affichent si le formulaire est incomplet

### 📸 Preuve du test
![Capture d'écran 2026-11-06 090838](https://hackmd.io/_uploads/HyGMs-jaMg.png)
---

## 6. Documenter les choix d'accessibilité

Rédiger une courte note (dans HackMD) avec ce tableau :

| Critère | Ce que j'ai fait | Pourquoi |
|---|---|---|
| Labels | `<label for>` sur chaque champ | Les lecteurs d'écran annoncent le champ |
| Regroupement | `<fieldset>` et `<legend>` | Structure claire des sections |
| Langue | `lang="fr"` | Prononciation correcte par le lecteur d'écran |
| Contraste | Texte `#1a1a1a` sur fond blanc | Lisible pour les malvoyants (WCAG AA) |
| Focus visible | `outline` bleu de 3 px | Navigation clavier possible |
| Validation | `required`, `type`, `pattern` | Erreurs détectées sans JavaScript |
| Taille du texte | 18 px minimum | Confort de lecture |

---