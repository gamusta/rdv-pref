# RDV Prefecture - Plateforme de Gestion

Plateforme web Next.js servant de centre de contrôle pour une extension Chrome automatisant les réservations de RDV en préfecture.

## 🎯 Fonctionnalités

### Pour Utilisateurs
- Soumettre demandes de réservation RDV
- Suivre statut en temps réel (PENDING → PROCESSING → COMPLETED/FAILED)
- Consulter logs et historique

### Pour Administrateurs
- Supervision toutes réservations (tous users)
- Gestion catalogue préfectures (extraction formulaires)
- KPI : taux succès, coûts captcha, métriques performance
- Analyse logs et causes d'échecs

## 🔄 Fonctionnement

**Extension Chrome (existante)** → traite réservations automatiquement :
- Mode 1 (Booking) : résolution captcha + attente créneaux + remplissage formulaire
- Mode 2 (Extraction) : analyse structure formulaires préfectures

**Plateforme Web** → fournit/reçoit données :
- **GET** : configs réservations, liste préfectures à analyser
- **POST** : statuts, métriques captcha, logs, champs extraits

## 🛠️ Stack Technique

- **Framework** : Next.js 14+ (App Router)
- **Language** : TypeScript
- **Database** : PostgreSQL + Prisma ORM
- **Auth** : NextAuth.js (session-based, roles USER/ADMIN)
- **UI** : Shadcn/ui + Tailwind CSS
- **Validation** : Zod + React Hook Form
- **Hosting** : Vercel

## 📁 Architecture

```
/src
  /app
    /(public)              # Landing page + pages publiques
    /dashboard             # Dashboard utilisateur
      /page.tsx            # Liste réservations
      /new/page.tsx        # Créer réservation
      /[id]/page.tsx       # Détail + logs
    /admin                 # Dashboard admin
      /page.tsx            # Vue générale (KPI)
      /bookings/page.tsx   # Toutes réservations
      /procedures/page.tsx # Gestion préfectures
      /mappings/page.tsx   # Mappings ChatGPT
      /metrics/page.tsx    # Métriques captcha
      /logs/page.tsx       # Logs système
    /api
      /auth/[...nextauth]  # NextAuth endpoints
      /bookings            # CRUD réservations
      /procedures          # CRUD préfectures
      /mappings            # Mappings GPT
      /metrics             # Métriques captcha
      /logs                # Logs système
      /admin               # Routes admin (KPI)
  /components
    /ui                    # Shadcn components
    /dashboard             # Composants dashboard user
    /admin                 # Composants admin
  /lib
    /db.ts                 # Prisma client singleton
    /auth.ts               # NextAuth config
    /validations.ts        # Schémas Zod
  /types
    /index.ts              # Types TypeScript globaux
  /middleware.ts           # Protection routes /admin
prisma
  /schema.prisma           # Modèles BDD
```

## 🗄️ Modèles Données

- **User** : email, nom, role (USER/ADMIN)
- **Booking** : demande réservation, statut, formFields, userData
- **Procedure** : préfecture, URL, champs extraits (Mode 2)
- **FormMapping** : mappings calculés par ChatGPT
- **CaptchaMetric** : stats résolutions captcha (temps, coût)
- **Log** : traces exécution/erreurs extension

## 🔐 Authentification

**Dashboard** : NextAuth sessions
- Credentials provider (email/password)
- Middleware : protection `/admin/*` (role ADMIN)

**API Extension** : Header `X-API-Key`
- Endpoints publics pour extension Chrome

## 🚀 Installation

```bash
# Clone & install
git clone <repo>
npm install

# Setup database
npx prisma generate
npx prisma db push

# Variables d'environnement
cp .env.example .env
# Compléter : DATABASE_URL, NEXTAUTH_SECRET, EXTENSION_API_KEY

# Dev
npm run dev
```

## 📊 API Extension (exemples)

```bash
# Récupérer réservations en attente
GET /api/bookings?userId={id}
Headers: X-API-Key: xxx

# Mettre à jour statut
PATCH /api/bookings/{id}/status
Body: { status: "COMPLETED" }

# Enregistrer log
POST /api/logs
Body: { type: "ERROR", message: "...", startUrl: "..." }

# Enregistrer métrique captcha
POST /api/metrics/captcha
Body: { startUrl: "...", solveTime: 3500, cost: 0.003 }
```

## 📝 Roadmap MVP

- [x] Spécifications fonctionnelles
- [ ] Setup projet + Prisma schema
- [ ] NextAuth + middleware
- [ ] API routes extension
- [ ] Dashboard admin (KPI + gestion)
- [ ] Dashboard utilisateur
- [ ] Tests + deploy Vercel

## 📄 License

Propriétaire - Tous droits réservés