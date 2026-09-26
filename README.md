# Plan Vélo — La Baule-Escoublac

Suivi interactif du plan vélo de la Ville de La Baule-Escoublac : carte des itinéraires, tableau de bord, planning et points bloquants. Site statique (une seule page HTML) branché sur une base de données Supabase pour un partage en direct, ouvert au public.

## Architecture

- **Frontend** : `index.html`, une page HTML/CSS/JS autonome, aucune étape de build. Déployée telle quelle sur Vercel.
- **Données** : Supabase (Postgres + API + temps réel). Deux tables, `itineraries` et `segments`, chacune avec une colonne `id` et une colonne `data` (JSON flexible reprenant la structure exacte utilisée par l'application). Voir `supabase_schema.sql`.
- **Édition** : ouverte à tout visiteur ayant le lien (pas d'authentification). Toute modification (statut, budget, tracé, etc.) est écrite dans Supabase et repoussée en temps réel à tous les autres visiteurs connectés.

## Mise en route (déjà faite lors du déploiement initial)

1. Créer un projet Supabase, puis exécuter `supabase_schema.sql` dans l'éditeur SQL du projet (SQL Editor → New query → coller → Run).
2. Récupérer, dans Project Settings → Data API, la « Project URL » et la clé « anon public ».
3. Renseigner ces deux valeurs dans `index.html`, aux lignes :
   ```html
   const SUPABASE_URL = "...";
   const SUPABASE_ANON_KEY = "...";
   ```
   (La clé « anon public » est faite pour être visible côté client — ce n'est pas un secret. La sécurité réelle, définie dans `supabase_schema.sql`, repose sur les policies Row Level Security.)
4. Déployer ce dépôt sur Vercel (aucune configuration nécessaire : c'est un site statique).

## Reprise par une autre structure

Ce dépôt est autonome et ne dépend d'aucun service propre à Claude — il peut être cloné et redéployé tel quel sur n'importe quel hébergement statique (Vercel, Netlify, un serveur web classique…) et sur n'importe quel projet Supabase :

1. Créer un nouveau projet Supabase et y exécuter `supabase_schema.sql`.
2. Remplacer les deux constantes `SUPABASE_URL` / `SUPABASE_ANON_KEY` dans `index.html` par celles du nouveau projet.
3. Déployer `index.html` (+ `map_itineraires.jpg` et `map_amenagements.jpg`) sur l'hébergement choisi.

Pour restreindre l'édition (par exemple si une structure souhaite protéger la saisie derrière une authentification), il suffit de modifier les policies RLS dans Supabase — rien à changer côté application.

## Fichiers

- `index.html` — l'application.
- `map_itineraires.jpg`, `map_amenagements.jpg` — les deux fonds de carte officiels utilisés par l'onglet Carte.
- `supabase_schema.sql` — création des tables, sécurité (RLS) et activation du temps réel.
