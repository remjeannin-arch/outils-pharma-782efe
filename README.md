# Outils Pharmacie

Page web pour **remplir, imprimer et coller dans Winpharma** des documents d'officine
(attestations, TROD, etc.). Conçue pour la sécurité des données patients : **rien n'est
jamais enregistré** — tout est saisi dans le navigateur et s'efface à la fermeture.

## 🔗 En ligne
- **Site** : https://remjeannin-arch.github.io/outils-pharma-782efe/
- **Repo** : https://github.com/remjeannin-arch/outils-pharma-782efe (public)

À ouvrir dans **Chrome ou Edge** (le copier-coller d'image exige un contexte HTTPS).

## Principe
- Page d'accueil avec **tuiles** (un document = une tuile).
- À l'ouverture d'un document : formulaire à gauche, **aperçu fidèle** à droite (rendu canvas).
- Boutons : **📋 Copier l'image** (→ `Ctrl/Cmd+V` dans la partie ordo Winpharma),
  🖨️ Imprimer, 🖼️ Télécharger PNG, 🗑️ Effacer.
- Image exportée en **PNG noir/blanc léger (< 100 Ko)**, lisible pour la Sécu.

## Confidentialité
- Aucun `localStorage`, aucun réseau : les saisies ne quittent pas le navigateur.
- Le code est public mais **ne contient aucune donnée ni secret** → ne jamais y mettre
  d'information sensible.

## Architecture (1 seul fichier : `index.html`)
- `DOCUMENTS` : objet de définitions. Chaque doc = `{ title, icon, desc, fields, blocks(d) }`.
  - `fields` → génère le formulaire (sections, lignes, cases à cocher).
  - `blocks(d)` → décrit le rendu (liste de blocs déclaratifs).
- `COMING_SOON` : tuiles grisées « À venir ».
- **Moteur de rendu canvas** par blocs : `logo`, `heading`, `paragraph`, `field`,
  `fieldrow`, `checkbox`, `note`, `hr`, `spacer`. Word-wrap + 2 passes (mesure puis dessin).
- Aperçu net (DPR=2) ; export à `EXPORT_W = 1000` px.
- « Copier l'image » : `ClipboardItem` avec **Promise de blob** passée directement
  (indispensable pour Safari).

## Ajouter un document
1. Dans `index.html`, ajouter une entrée dans `DOCUMENTS` avec ses `fields` et son `blocks(d)`.
2. (Optionnel) retirer la tuile correspondante de `COMING_SOON`.
3. Tester localement, puis déployer (voir ci-dessous).

## Déploiement
GitHub Pages via **GitHub Actions** (`.github/workflows/deploy.yml`).
```bash
# depuis /Users/remijeannin/docs-pharma
git add -A && git commit -m "..." && git push origin main
# → le site se met à jour en ~1 min
```
⚠️ **Ne pas** utiliser le build Pages « legacy » : il se coince et provoque des 404
persistants. Rester en `build_type=workflow`.

## Documents
| Document | Statut |
|---|---|
| Attestation séjour à l'étranger (traitement > 1 mois, régime général AURA) | ✅ Disponible |
| TROD angine | 🔜 À venir |
| Cystite | 🔜 À venir |
| Location de matériel | 🔜 À venir |
| Module de caisse | 🔜 À définir |
