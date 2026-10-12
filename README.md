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
| Location de matériel | ✅ Disponible |
| Pack cystite (éligibilité TROD, bon de prise en charge, fiche conseil) | ✅ Disponible |
| Pack angine (éligibilité McIsaac, bon de prise en charge, fiche conseil) | ✅ Disponible |
| Dépistage cancer colorectal (auto-questionnaire INCa) | ✅ Disponible |
| Comptage de caisse (`/caisse/`) | ✅ Disponible |
| Vaccination grippe 2026-2027 (`/grippe/`) | ✅ Disponible |

## Module vaccination grippe (`grippe/`)
Aide à la décision au comptoir (campagne 2026-2027, Flucelvax / Fluad) : questions dans
l'ordre de la procédure, conclusion mise à jour en direct (vaccin, prise en charge, **bon à
sortir**, points de vigilance). Les bons officiels CNAM (610d, 610d-ALD, 610e, 610e-ALD, 610f,
611 — version 07/2026, valables jusqu'au 31/01/2027) sont dans `grippe/bons/`. Le bouton
« Ouvrir le bon pré-rempli » remplit les champs d'en-tête du PDF (NIR, bénéficiaire, date de
naissance, code organisme) **dans le navigateur** avec pdf-lib (CDN cdnjs) puis l'ouvre dans un
onglet : rien n'est envoyé ni stocké. Règles « à confirmer » signalées en orange dans la page.
Mise à jour des bons : remplacer les PDF dans `grippe/bons/` (mêmes noms).
Adulte non éligible (vaccin et injection payés par le patient) : le module renvoie vers le
document canvas `ordonnance-grippe` (prescription pharmaceutique, tampon pré-rempli).
L'accueil accepte `index.html#id-du-document` pour ouvrir directement un document canvas.
