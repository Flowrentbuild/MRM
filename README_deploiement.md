# Console MRM — Déploiement Cloudflare Pages

## Ce que c'est

Une page HTML statique qui sert de **console métier MRM** pour déposer les pièces à contrôler. Elle remplace l'interface n8n par défaut par une page brandée, avec un toggle "1 BDC" / "multi-BDC" qui route vers le bon workflow.

## Avant déploiement — 3 minutes de personnalisation

### 1. Couleurs MRM

Ouvre `index.html`. En tout début du `<style>`, tu trouveras le bloc `:root { ... }` qui contient toutes les couleurs en variables CSS :

```css
--mrm-primary:        #1F3864;   /* bleu profond — couleur principale */
--mrm-primary-dark:   #142544;   /* bleu nuit — hover */
--mrm-accent:         #B87333;   /* cuivre / bois chaud — accents */
```

Remplace ces 3 valeurs par les couleurs réelles de mrm31.fr (utilise [ColorPick](https://chrome.google.com/webstore/detail/colorpick-eyedropper/ohcpnigalekghcmgcdcenkpelffpdolg) ou l'inspecteur Chrome pour extraire les codes hex depuis le site).

### 2. Logo

Dans `index.html`, ligne 80 environ, tu vois :

```html
<div class="logo">M</div>
```

Pour mettre le vrai logo MRM :
1. Place ton logo dans le dossier (par exemple `logo-mrm.png` ou `.svg`)
2. Remplace la ligne par :
   ```html
   <img src="logo-mrm.png" alt="MRM Menuiserie" class="logo" style="object-fit:contain;">
   ```
3. (Optionnel) Ajuste la taille en modifiant `.logo { width: 48px; height: 48px; }` dans le CSS.

### 3. URLs des webhooks (déjà configurées)

Dans `index.html`, tout en bas du `<script>`, tu as :

```javascript
const WEBHOOK_URLS = {
  single: "https://flowrentbuild.app.n8n.cloud/form/mrm-verificateur-webhook",
  multi:  "https://flowrentbuild.app.n8n.cloud/form/mrm-multi-bdc-webhook"
};
```

Vérifie que ces URLs correspondent bien à tes 2 workflows n8n actifs. Si tu changes d'instance n8n ou de webhookId, modifie ici.

## Déploiement Cloudflare Pages — 5 minutes

1. Crée un compte gratuit sur [pages.cloudflare.com](https://pages.cloudflare.com) si pas déjà fait
2. Clique **« Create a project »** → **« Direct upload »**
3. Drag-and-drop le dossier `console_mrm/` (ou juste `index.html` + `logo-mrm.png` si tu as ajouté le logo)
4. Donne un nom au projet (ex: `mrm-verificateur`)
5. Click **Deploy**

Cloudflare te donne une URL `mrm-verificateur.pages.dev`. C'est ton URL de démo.

## Sous-domaine custom (optionnel — verif.mrm31.fr)

Si tu veux que la console réponde à `verif.mrm31.fr` plutôt qu'à `mrm-verificateur.pages.dev` :

1. Dans Cloudflare Pages → ton projet → **Custom domains** → **Set up a custom domain**
2. Tape `verif.mrm31.fr`
3. Cloudflare te donne un CNAME à ajouter dans le DNS du domaine `mrm31.fr` (chez l'éditeur DNS de MRM, probablement OVH ou Gandi)
4. Une fois le DNS propagé (15 min à 1h), `verif.mrm31.fr` pointe sur la console.

## Mises à jour ultérieures

À chaque fois que tu modifies `index.html` (couleurs, ajout d'un fournisseur dans la liste, logo, etc.) :
- Soit tu re-uploades manuellement la nouvelle version dans Cloudflare Pages
- Soit tu connectes un repo GitHub à Cloudflare Pages → chaque commit déploie automatiquement la nouvelle version

## Test rapide en local

Pas besoin de déploiement pour tester :

1. Ouvre `index.html` directement dans Chrome (double-clic)
2. Le formulaire s'affiche
3. Tu peux drag & drop les fichiers, mais le POST vers n8n peut être bloqué par CORS si tu testes en `file://`. Pour test local complet, lance un serveur :
   ```
   python3 -m http.server 8000
   ```
   Puis ouvre `http://localhost:8000`.

## Limites connues

- Pas de mode `dark` automatique (à ajouter si demandé)
- Le formulaire ne contient pas encore d'aperçu PDF (juste les noms de fichiers)
- Les uploads très lourds (>20 Mo) peuvent buter sur les limites n8n Cloud
