Tu es un expert Python et développement web.

Génère un script Python découpé en 4 blocs commentés (CELLULE 1 à 4),
chaque bloc étant destiné à être copié dans une cellule Jupyter Notebook séparée.

Le script crée un questionnaire de bien-être académique bilingue français/arabe
(échelle WEMWBS), le déploie sur GitHub Pages, et collecte les réponses
dans Google Sheets.

════════════════════════════════════════════════
CELLULE 1 — INSTALLATION
════════════════════════════════════════════════
Installer silencieusement avec subprocess.check_call + stdout=DEVNULL :
  requests, qrcode[pil], Pillow
Afficher "✓ nom_paquet" pour chacun.
Terminer par "✅ Prêt — passez à la cellule 2".

════════════════════════════════════════════════
CELLULE 2 — GÉNÉRATION DU FICHIER index.html
════════════════════════════════════════════════
Écrire un fichier index.html autonome (HTML + CSS + JS inline).
L'écrire avec : Path("index.html").write_text(contenu_html, encoding="utf-8")

Le fichier HTML doit respecter ces règles STRICTES :

A) JAVASCRIPT — SYNTAXE OBLIGATOIRE
   - Toutes les variables globales déclarées avec "var" (jamais const/let)
   - Toutes les fonctions déclarées avec "function nom()" (jamais de =>)
   - Les boutons de navigation utilisent onclick="nom()" en attribut HTML
   - La fonction de navigation allerA(n) est déclarée EN PREMIER dans le script
   - Les boucles sur les 14 items utilisent une IIFE : (function(i){...})(idx)
   - Pas d'addEventListener pour les boutons principaux
   - Compatible Safari iOS 12+

B) NAVIGATION — 4 PAGES
   - IDs : p0, p1, p2, p3
   - CSS : .page{display:none} .page.actif{display:block}
   - allerA(n) : retire "actif" de la page courante, ajoute "actif" à p+n
   - Variable globale : var pageN = 0;
   - Appel buildItems() uniquement quand allerA(2) est appelé

C) PAGE 0 — ACCUEIL
   Titre FR : "Bien-être au Maroc" / AR : "الرفاهية في المغرب"
   Institution : Ali Bezzaa — FSJES Marrakech
   Objectif de l'étude WEMWBS 2007, durée 5 min, clause RGPD anonymat
   Bouton FR : "Commencer le questionnaire →" onclick="allerA(1)"
   Bouton AR : "ابدأ الاستبيان ←"

D) PAGE 1 — SOCIO-DÉMOGRAPHIQUE
   5 champs obligatoires (select + input texte) :
     age : 18-24 / 25-34 / 35-44 / 45-54 / 55-64 / 65+
     genre : Homme / Femme / Préfère ne pas répondre
     education : Primaire / Collège / Lycée / Licence / Master / Doctorat / Autre
     situation : Employé(e) / Indépendant(e) / Étudiant(e) / Sans emploi / Retraité(e) / Autre
     pays : champ texte libre
   Bouton onclick="valDemo()" — valDemo() vérifie tous les champs,
   affiche erreur si vide, appelle allerA(2) si tout est rempli
   Stocker dans var demo = {} avant d'appeler allerA(2)

E) PAGE 2 — QUESTIONNAIRE WEMWBS 14 ITEMS
   Items FR (dans l'ordre) :
     1. Je me suis senti(e) optimiste quant à l'avenir
     2. Je me suis senti(e) utile
     3. Je me suis senti(e) détendu(e)
     4. J'ai eu des sentiments chaleureux envers d'autres personnes
     5. J'ai eu de l'énergie à revendre
     6. J'ai abordé les problèmes avec lucidité
     7. Je me suis senti(e) bien dans ma peau
     8. Je me suis senti(e) proche des autres
     9. J'ai été en mesure de prendre mes propres décisions
    10. Je me suis senti(e) aimé(e) et apprécié(e)
    11. Je me suis intéressé(e) à de nouvelles choses
    12. Je me suis senti(e) joyeux/joyeuse
    13. Je me suis senti(e) inspiré(e) dans ce que je faisais
    14. J'ai eu l'impression que je contribuais à quelque chose d'important

   Items AR (dans l'ordre) :
     1. شعرت بالتفاؤل تجاه المستقبل
     2. شعرت أنني مفيد/ة
     3. شعرت بالاسترخاء
     4. شعرت بالدفء تجاه الآخرين
     5. كان لديّ طاقة كافية
     6. تعاملت مع المشكلات بوضوح
     7. شعرت بالرضا عن نفسي
     8. شعرت بالقرب من الآخرين
     9. كنت قادر/ة على اتخاذ قراراتي بنفسي
    10. شعرت بأنني محبوب/ة ومُقدَّر/ة
    11. اهتممت بأشياء جديدة
    12. شعرت بالبهجة والسعادة
    13. شعرت بالإلهام فيما أقوم به
    14. شعرت أن ما أفعله يساهم في شيء مهم

   Échelle Likert 5 niveaux :
     FR : Jamais(1) Rarement(2) Quelquefois(3) Souvent(4) Tout le temps(5)
     AR : أبدًا نادرًا أحيانًا كثيرًا دائمًا

   Bouton onclick="soumettre()" — soumettre() vérifie les 14 items,
   affiche erreur si incomplet, calcule score et appelle allerA(3)

F) PAGE 3 — RÉSULTATS
   Score total affiché (14–70)
   Barre de progression animée (setTimeout 150ms)
   Interprétation :
     14–42 → faible  : "Bien-être faible — cherchez un soutien professionnel"
     43–52 → modéré  : "Bien-être modéré — prenez soin de vous"
     53–70 → élevé   : "Bien-être élevé — continuez ainsi"
   Message de remerciement
   Bouton recommencer onclick="resetQ()"
   Bouton télécharger onclick="window.print()"

G) COLLECTE GOOGLE SHEETS — MÉTHODE FORMULAIRE CACHÉ
   Dans le body, AVANT le script :
     
   Variable : var SHEETS_URL = "PLACEHOLDER_URL";
   Fonction envoyerDonnees(entree) :
     1. Créer un  method="POST" action=SHEETS_URL target="_gs_frame"
     2. Pour chaque clé de l'objet entree : créer un 
     3. Appendre le form au body, appeler form.submit()
     4. Supprimer le form après 2000ms avec setTimeout
     5. Sauvegarder aussi dans localStorage key "wemwbs" comme backup
     6. Afficher un toast "✓ Réponse enregistrée" pendant 3 secondes
   NE PAS utiliser fetch() — uniquement le formulaire HTML

H) OBJET DE TRADUCTIONS
   var T = { fr: {...}, ar: {...} }
   Contient tous les textes de l'interface dans les deux langues
   Fonction appliquer() met à jour tous les éléments selon var lng = "fr"
   Fonction basculer() alterne lng, met à jour document.dir,
   appelle appliquer(), reconstruit les items si pageN === 2

I) DESIGN CSS
   Variables CSS : --bleu:#1B4F8A --vert:#2E8B6A --fond:#FAFAFA
   Polices Google Fonts : Inter (FR) + Tajawal (AR)
   Header fixe bleu avec bouton langue en haut à droite
   Barre progression fixed top 4px gradient bleu→vert
   Cartes blanches border-radius:12px box-shadow
   Boutons radio : sélectionné = fond bleu texte blanc
   Toast fixed bottom-right (vert si OK, rouge si erreur)
   Responsive mobile-first max-width:720px centré

J) STRUCTURE DU SCRIPT JS (dans cet ordre exact) :
   1. var SHEETS_URL = "PLACEHOLDER_URL";
   2. var lng="fr"; var pageN=0; var tDebut=Date.now(); var demo={};
   3. var T = { fr:{...}, ar:{...} };
   4. function allerA(n){...}
   5. function majPts(){...}
   6. function appliquer(){...}
   7. function basculer(){...}
   8. function valDemo(){...}
   9. function buildItems(){...}
   10. function soumettre(){...}
   11. function envoyerDonnees(entree){...}
   12. function sauvegarderLocal(entree){...}
   13. function afficherToast(msg,cls){...}
   14. function resetQ(){...}
   15. appliquer(); (initialisation)

════════════════════════════════════════════════
CELLULE 3 — AFFICHAGE DU GOOGLE APPS SCRIPT
════════════════════════════════════════════════
Afficher dans Jupyter avec display(HTML(...)) le script Google Apps Script
que l'utilisateur doit coller dans Extensions → Apps Script de sa Google Sheet.

Le Apps Script doit :
- Recevoir les données via e.parameter (form-data, PAS e.postData)
- Créer les en-têtes automatiquement si première ligne vide :
  timestamp, langue, age, genre, education, situation, pays,
  item_1 à item_14, score_total, interpretation, duree_secondes
- Mettre les en-têtes en fond #1B4F8A, texte blanc, gras, ligne figée
- Construire la ligne avec COLS.map(function(c){ return p[c]||''; })
- Retourner JSON {statut:'ok', ligne: sheet.getLastRow()}
- Avoir une fonction doGet() qui retourne 'endpoint actif'

Afficher également les instructions étape par étape :
  1. Google Sheets → Extensions → Apps Script → effacer → coller
  2. Déployer → Nouveau déploiement → Web App
  3. Exécuter en tant que : Moi / Accès : Tout le monde
  4. Copier l'URL exec → la coller dans la cellule 4

════════════════════════════════════════════════
CELLULE 4 — DÉPLOIEMENT GITHUB PAGES + QR CODE
════════════════════════════════════════════════
Demander 4 informations à l'utilisateur :
  url_sheets  = input("URL Google Apps Script (exec) : ")
  gh_username = input("GitHub username : ")
  gh_repo     = input("Nom du repo : ")
  gh_token    = getpass("GitHub token : ")

Étape 1 — Vérifier le token :
  GET https://api.github.com/user
  Si status != 200 → print erreur et arrêter

Étape 2 — Injecter l'URL dans index.html :
  Utiliser re.sub pour remplacer var SHEETS_URL = ".*?";
  par var SHEETS_URL = "{url_sheets}";

Étape 3 — Générer README.md avec l'URL finale et .nojekyll vide

Étape 4 — Créer le repo GitHub si inexistant :
  GET /repos/{user}/{repo} → si 404 : POST /user/repos
  public:True, description:"WEMWBS FR/AR — Ali Bezzaa / FSJES Marrakech"
  Attendre 2 secondes après création

Étape 5 — Pousser les 3 fichiers (index.html, README.md, .nojekyll) :
  Pour chaque fichier :
    GET /repos/{user}/{repo}/contents/{fichier} → récupérer sha si existe
    PUT /repos/{user}/{repo}/contents/{fichier}
    body : message, content (base64), branch:"main", sha si existe
  Attendre 0.5s entre chaque fichier

Étape 6 — Activer GitHub Pages :
  POST /repos/{user}/{repo}/pages → source branch:main path:/
  Si 409 (déjà activé) → ignorer
  Si autre erreur → essayer PUT à la place

Étape 7 — Générer un QR code :
  Utiliser qrcode avec fill_color="#1B4F8A", back_color="white"
  Sauvegarder en "qr.png"
  Afficher avec IPython.display.Image(filename="qr.png", width=180)

Étape 8 — Afficher le résultat final avec display(HTML(...)) :
  Encadré vert avec :
  - URL cliquable : https://{gh_username}.github.io/{gh_repo}/
  - Lien vers le repo GitHub
  - Message : "Patientez 1–3 min que GitHub Pages s'active"
  - Message : "Soumettez une réponse test → vérifiez Google Sheets"

════════════════════════════════════════════════
CONTRAINTES GÉNÉRALES
════════════════════════════════════════════════
- Tous les commentaires Python en français
- Imports en haut de chaque cellule (pas de imports globaux entre cellules)
- Chaque cellule est autonome et peut être copiée indépendamment
- Le contenu HTML est une chaîne Python entre guillemets triples
- Aucune f-string dans la partie HTML (utiliser .format() ou concaténation
  pour injecter des valeurs Python dans le HTML si nécessaire)
- Taille cible du fichier index.html : entre 25 Ko et 60 Ko
- Le script Python complet doit tenir en un seul bloc de code
