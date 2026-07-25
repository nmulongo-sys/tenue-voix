# Tenue de voix — évaluateur d'harmonie

Application mono-fichier, hors-ligne, mobile-first. Colle une consigne (notes + durées),
chante — a cappella ou en jouant — et vois note par note où l'harmonie tient et où elle lâche.

**Démo :** https://nmulongo-sys.github.io/tenue-voix/

## Usage

1. **Consigne** — coller une séquence au format :

   ```
   # commentaire
   TEMPO 76
   Sol#2 4        ← note + octave, durée en temps
   REPOS 2        ← silence
   Do3 4
   ```

   Noms français (Do Ré Mi…) ou lettres (C D E…), dièses `#`, bémols `b`.
   Chaque note chargée affiche sa fréquence en Hz : vérifier le registre.

2. **Réglages** — tempo, tolérance en cents (50 par défaut), convention d'octave
   (internationale La4 = 440 Hz, ou franco-belge La3 = 440 Hz), « Ignorer l'octave »,
   « Je joue un instrument » (filtre passe-haut adaptatif sous la note cible la plus basse),
   guide sonore pendant l'enregistrement (au casque).

3. **Chanter** — décompte de 4 clics, puis la trace vocale se peint en direct sur les
   blocs cibles : vert = dans la tolérance, rouge = dehors.

4. **Résultats** — verdict par note (tenu / instable / faux / non chanté), % de justesse,
   écart moyen en cents avec direction (trop haut / trop bas), liste des moments à retravailler.

## Technique

- Détection de hauteur : YIN (CMNDF, interpolation parabolique), buffer 4096, seuil RMS anti-silence.
- Web Audio : AnalyserNode, BiquadFilter passe-haut optionnel, synthèse triangle pour cible et guide.
- Zéro dépendance, un seul fichier `index.html`. État persistant en localStorage.
- Micro : nécessite HTTPS (GitHub Pages) ; l'aperçu intégré d'applications tierces
  n'exécute pas le JavaScript — ouvrir dans un vrai navigateur.

## Limites connues

- Détection monophonique : en jeu simultané fort (basse/guitare dans le registre vocal),
  la détection peut décrocher malgré le filtre. Le mode a cappella reste le plus fiable.
- Pas d'enregistrement audio conservé : seule la trace de hauteur est analysée.

## Journal de développement

### 2026-07-25 — v1
- Création. Parseur de consignes (FR/EN, TEMPO, REPOS), conversion convention
  internationale / franco-belge, YIN validé sur sinus (±1 Hz à 220 Hz).
- Évaluation par trames : verdicts tenu ≥ 70 % / instable ≥ 40 % / faux / muet (< 30 % voisé).
- Timeline canvas : blocs cibles ocre, trace verte/rouge, curseur temps réel, repli d'octave à l'affichage.
- Validation headless : 25+ assertions (parseur, cents, évaluation, YIN) + jsdom (structure DOM,
  chargement de consigne, activation des contrôles). Garde-fou canvas sans contexte.
- Premier cas d'usage : ligne d'harmonie voix 2 du refrain de « Je l'aime à mourir »
  (sol# → la → si → do), variante avec Fa en mesure 21.
