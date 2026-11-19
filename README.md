# Amanat Digital Studio

Website for Amanat Digital Studio (Quetta, Balochistan) — mobile software repair, QR digital menus, websites & landing pages, and SEO.

Single-file static site (`index.html`). No build step needed.

## Order button
Each service page has an order box. "Send order on WhatsApp" opens WhatsApp with the customer's order pre-written, sent to `923308363433`.

To change the number, edit `WA_NUMBER` in `index.html` (digits only, country code first).

## Deploy on GitHub Pages
1. Push this folder to a GitHub repository.
2. Repo **Settings → Pages**.
3. Source: **Deploy from a branch**, branch `main`, folder `/ (root)`.
4. Your site goes live at `https://<username>.github.io/<repo-name>/`.

## Customer reviews
Customers post a review on the home page and it shows publicly right away. Reviews are stored free in Firebase Firestore.

### One-time setup
1. Go to https://console.firebase.google.com and create a project (Google Analytics can be off).
2. **Build -> Firestore Database -> Create database** (production mode, any region).
3. In Firestore **Rules**, paste the rules below and **Publish**.
4. **Project settings -> Your apps -> Web (`</>`)** and register an app. Copy the `firebaseConfig` values.
5. Paste `apiKey`, `authDomain`, `projectId`, `appId` into `FIREBASE_CONFIG` at the bottom of `index.html`.
6. Upload `index.html` to GitHub again.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{db}/documents {
    match /reviews/{id} {
      allow read: if true;
      allow create: if request.resource.data.keys().hasOnly(['name','rating','text','createdAt'])
        && request.resource.data.name is string
        && request.resource.data.name.size() > 0 && request.resource.data.name.size() <= 40
        && request.resource.data.text is string
        && request.resource.data.text.size() > 0 && request.resource.data.text.size() <= 600
        && request.resource.data.rating is int
        && request.resource.data.rating >= 1 && request.resource.data.rating <= 5
        && request.resource.data.createdAt == request.time;
      allow update, delete: if false;
    }
  }
}
```

### Removing a bad review
Firebase console -> Firestore Database -> `reviews` -> delete the document.
