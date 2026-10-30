# Sous-titres Prêches — publication en ligne & programme de production

Directive de Bah (2026-10-30) : **toutes les langues de prêche doivent avoir
des sous-titres, disponibles EN LIGNE SEULEMENT** (rien d'embarqué dans l'APK).

## Architecture livrée (v3.17.15)

- L'app interroge un **index hébergé** :
  `https://raw.githubusercontent.com/Mouc878/an-nur-sermons/main/subs-index.json`
  (format : `{version, updated, tracks: { <sermonId>: { <lang>: <url VTT> } }}`).
- **Badge « CC » affiché uniquement si l'index confirme** une piste publiée
  pour ce prêche (cartes du catalogue + bouton CC du lecteur).
- À l'activation, la piste VTT est **téléchargée à la demande** puis
  **mise en cache sur l'appareil** (localStorage `s1sub:<id>:<lang>`) :
  un prêche déjà suivi garde sa piste hors ligne. Sans connexion ni
  cache, le lecteur affiche simplement « Sous-titres indisponibles
  sans connexion » (FR/AR/EN).
- Dernier index connu conservé en local (`s1_subs_index`) : les badges
  restent stables hors ligne une fois l'index vu.
- Les sous-titres restent une fonction **Premium** (règle du lot S1,
  inchangée). Langue affichée = langue de l'app, surcharge manuelle
  dans le lecteur.
- **Aucune piste dans l'APK** : `assets/sermons/` ne contient que le
  catalogue ; le champ `subs` a été retiré des 587 fiches du catalogue.

## État au jour de la livraison

- **12 pistes prêtes à publier, couvrant 5 prêches** (dossier
  `sermon-subs-online/` : `subs-index.json` + `tracks/*.vtt`) :
  Abdel Kafi « أصحاب الأخدود » (AR/FR/EN), Abdel Kafi « أدم عليه السلام »
  (AR/EN), Kishk « لا تحزن » (AR/EN), Kishk « سنة الرسول في السفر »
  (AR/EN), Mufti Menk « Your Friend Is Allah » (EN/AR/FR).
- **Reste à faire pour la mise en ligne effective** : créer le dépôt
  GitHub public `Mouc878/an-nur-sermons` et y pousser ce dossier
  (`subs-index.json` à la racine, `tracks/` à côté). Aucun accès
  GitHub n'était authentifié dans l'environnement de build ; l'app
  dégrade proprement tant que l'index est absent (aucun badge).
- Qualité des 12 pistes : ASR faster-whisper (modèle small) relues par
  échantillon ; la piste FR d'« أصحاب الأخدود » est une traduction
  **humaine** ; les pivots machine ar→en→fr de mauvaise qualité ont
  été **écartés** au lot S1 (Adam, 2 Kishk : AR/EN seulement).

## Programme de production restant (marathon)

Volumes mesurés sur le catalogue réel : **587 prêches, 551,9 h d'audio**.

| Étape | Volume | Rythme mesuré (lot S1) | Charge estimée |
|---|---|---|---|
| ASR langue source (faster-whisper small, CPU int8) | 551,9 h d'audio | ≈ 0,84 × temps réel | ≈ **464 h CPU** |
| Traductions (Argos hors ligne) | jusqu'à 2 pistes/prêche ≈ 1 174 pistes | ≈ 2 min/piste | ≈ **40 h CPU** |
| Relecture par échantillon + publication dans l'index | au fil de l'eau | porte qualité ci-dessous | inclus |

À raison de ~8 h de calcul/jour sur une machine : **≈ 2 mois** pour la
couverture complète ; chaque lot publié apparaît dans l'app **sans mise
à jour** (l'index est interrogé à l'ouverture de la section).

### Ordre de traitement
1. **Les 9 prêches gratuits** (prêche du jour + échantillon par imam) —
   5 faits, 4 restants : ils touchent tous les utilisateurs.
2. **Kishk d'abord** (257 prêches, 310,1 h) — thèmes de la vie
   quotidienne en premier (Couple & famille, Travail & argent,
   Épreuves & patience, Jeunesse, Éducation, Repentir), puis le reste.
3. **Mufti Menk** (144, 110,2 h, EN → paires AR/FR directes faciles),
   **Omar Suleiman** (60, 45,1 h), **Yasir Qadhi** (27, 26,2 h),
   **Al-Hayiti** (40, 21,2 h), **Muhammad Hassan** (17, 19,1 h),
   **Mahmoud al-Masri** (10, 14,4 h), **Omar Abdel Kafi** (32, 5,6 h).

### Porte qualité (inchangée, non négociable)
- Une piste n'est publiée que si sa **relecture par échantillon**
  passe ; sinon le prêche reste **sans badge** — jamais de
  sous-titres médiocres affichés comme disponibles.
- Les paires sans chemin de traduction acceptable (ex. pivot
  ar→en→fr jugé insuffisant au lot S1) restent non publiées jusqu'à
  une piste conforme (modèle direct ou traduction humaine).
- L'ASR est un brouillon fidèle mais imparfait : les pistes sont
  présentées comme des transcriptions automatiques relues par
  échantillon, jamais comme des textes officiels.

### Publication d'un lot (mécanique)
1. Déposer les `.vtt` dans `tracks/` du dépôt.
2. Ajouter les entrées dans `subs-index.json` (`tracks.<sermonId>.<lang>`)
   et mettre à jour `updated`.
3. Pousser : l'app voit les nouvelles pistes à la prochaine ouverture
   de la section Prêches. Aucune republication d'APK.
