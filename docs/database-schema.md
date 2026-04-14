# FreelanceFlow — Schéma de base de données

Modèle **PostgreSQL**, défini par **Prisma** (`backend/prisma/schema.prisma`) et migrations dans `backend/prisma/migrations/`. Conventions **snake_case** en base, alignement PRD (clients, prestations, factures HT/TVA/TTC, statuts, profil vendeur, isolation par compte).

## Diagramme entités-relations

```mermaid
erDiagram
  users ||--o| freelancer_profiles : "1 profil vendeur"
  users ||--o| invoice_counters : "compteur factures"
  users ||--o{ clients : possede
  users ||--o{ services : possede
  users ||--o{ invoices : possede
  users ||--o{ refresh_tokens : sessions
  clients ||--o{ invoices : facture
  invoices ||--o{ invoice_lines : contient
  services ||--o{ invoice_lines : "référence optionnelle"
  invoices ||--o{ invoice_status_events : "historique optionnel"

  users {
    uuid id PK
    string email UK
    string password_hash
    timestamptz created_at
    timestamptz updated_at
  }

  freelancer_profiles {
    uuid id PK
    uuid user_id FK_UK
    string display_name
    string legal_name
    string company_name "nullable"
    string address_line1
    string address_line2 "nullable"
    string postal_code
    string city
    string country
    string vat_number "nullable"
    string siret "nullable"
    timestamptz updated_at
  }

  invoice_counters {
    uuid user_id PK_FK
    int year
    int seq
  }

  refresh_tokens {
    uuid id PK
    uuid user_id FK
    string token_hash
    timestamptz expires_at
    timestamptz created_at
  }

  clients {
    uuid id PK
    uuid user_id FK
    string name
    string email
    string company
    text address
    timestamptz created_at
    timestamptz updated_at
  }

  services {
    uuid id PK
    uuid user_id FK
    string title
    decimal hourly_rate_ht
    timestamptz created_at
    timestamptz updated_at
  }

  invoices {
    uuid id PK
    uuid user_id FK
    uuid client_id FK
    string invoice_number
    string status
    date issue_date
    date due_date "nullable"
    string currency
    decimal total_ht
    decimal total_vat
    decimal total_ttc
    timestamptz created_at
    timestamptz updated_at
  }

  invoice_lines {
    uuid id PK
    uuid invoice_id FK
    uuid service_id FK "nullable"
    int line_order
    string description
    decimal quantity
    decimal unit_price_ht
    decimal vat_rate
    decimal line_ht
    decimal line_vat
    decimal line_ttc
    timestamptz created_at
  }

  invoice_status_events {
    uuid id PK
    uuid invoice_id FK
    string from_status "nullable"
    string to_status
    timestamptz changed_at
  }
```

## Règles métier reflétées dans le modèle

| Sujet | Choix |
|--------|--------|
| **Tenant** | Tables métier portent `user_id` (sauf `users`). Requêtes filtrées par utilisateur authentifié. |
| **Profil vendeur** | `freelancer_profiles` : infos facture/PDF. Champs légaux (SIRET, n° TVA) optionnels. |
| **Lignes de facture** | **Snapshot** : `description`, `unit_price_ht`, `vat_rate`, montants — cohérent si la prestation change (`service_id` nullable, **`ON DELETE SET NULL`** côté base). |
| **Totaux facture** | `total_ht`, `total_vat`, `total_ttc` dénormalisés sur `invoices` ; recalcul côté service aux mutations. |
| **Numéro de facture** | Unique **par utilisateur** : `UNIQUE (user_id, invoice_number)`. Séquence annuelle optionnelle via `invoice_counters` (une ligne par utilisateur, clé primaire `user_id`). |
| **Statuts** | Enum Prisma / PG `InvoiceStatus` : `draft`, `sent`, `paid`, `cancelled`. |
| **Suppression client** | **`ON DELETE RESTRICT`** sur `invoices.client_id` si des factures référencent le client. |
| **Monnaie** | `currency` défaut `EUR` (`CHAR(3)` en base) ; montants en `DECIMAL` / `NUMERIC`. |
| **JWT / session** | `refresh_tokens` : hash du token, expiration, lien `user_id` ; suppression en cascade si l’utilisateur est supprimé. |

## Schéma Prisma (référence d’implémentation)

Fichier source : `backend/prisma/schema.prisma`.

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id             String             @id @default(uuid()) @db.Uuid
  email          String             @unique
  passwordHash   String             @map("password_hash")
  createdAt      DateTime           @default(now()) @map("created_at")
  updatedAt      DateTime           @updatedAt @map("updated_at")
  clients        Client[]
  profile        FreelancerProfile?
  invoices       Invoice[]
  services       Service[]
  refreshTokens  RefreshToken[]
  invoiceCounter InvoiceCounter?

  @@map("users")
}

model FreelancerProfile {
  id           String   @id @default(uuid()) @db.Uuid
  userId       String   @unique @map("user_id") @db.Uuid
  displayName  String   @map("display_name")
  legalName    String   @map("legal_name")
  companyName  String?  @map("company_name")
  addressLine1 String   @map("address_line1")
  addressLine2 String?  @map("address_line2")
  postalCode   String   @map("postal_code")
  city         String
  country      String   @default("FR")
  vatNumber    String?  @map("vat_number")
  siret        String?
  updatedAt    DateTime @updatedAt @map("updated_at")
  user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@map("freelancer_profiles")
}

model Client {
  id        String   @id @default(uuid()) @db.Uuid
  userId    String   @map("user_id") @db.Uuid
  name      String
  email     String
  company   String
  address   String
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")
  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  invoices  Invoice[]

  @@index([userId])
  @@map("clients")
}

model Service {
  id           String        @id @default(uuid()) @db.Uuid
  userId       String        @map("user_id") @db.Uuid
  title        String
  hourlyRateHt Decimal       @map("hourly_rate_ht") @db.Decimal(12, 2)
  createdAt    DateTime      @default(now()) @map("created_at")
  updatedAt    DateTime      @updatedAt @map("updated_at")
  lines        InvoiceLine[]
  user         User          @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId])
  @@map("services")
}

model Invoice {
  id            String               @id @default(uuid()) @db.Uuid
  userId        String               @map("user_id") @db.Uuid
  clientId      String               @map("client_id") @db.Uuid
  invoiceNumber String               @map("invoice_number")
  status        InvoiceStatus        @default(draft)
  issueDate     DateTime             @map("issue_date") @db.Date
  dueDate       DateTime?            @map("due_date") @db.Date
  currency      String               @default("EUR") @db.Char(3)
  totalHt       Decimal              @map("total_ht") @db.Decimal(12, 2)
  totalVat      Decimal              @map("total_vat") @db.Decimal(12, 2)
  totalTtc      Decimal              @map("total_ttc") @db.Decimal(12, 2)
  createdAt     DateTime             @default(now()) @map("created_at")
  updatedAt     DateTime             @updatedAt @map("updated_at")
  lines         InvoiceLine[]
  events        InvoiceStatusEvent[]
  client        Client               @relation(fields: [clientId], references: [id])
  user          User                 @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([userId, invoiceNumber])
  @@index([userId])
  @@index([clientId])
  @@map("invoices")
}

model InvoiceLine {
  id          String   @id @default(uuid()) @db.Uuid
  invoiceId   String   @map("invoice_id") @db.Uuid
  serviceId   String?  @map("service_id") @db.Uuid
  lineOrder   Int      @map("line_order")
  description String
  quantity    Decimal  @db.Decimal(10, 2)
  unitPriceHt Decimal  @map("unit_price_ht") @db.Decimal(12, 2)
  vatRate     Decimal  @map("vat_rate") @db.Decimal(5, 4)
  lineHt      Decimal  @map("line_ht") @db.Decimal(12, 2)
  lineVat     Decimal  @map("line_vat") @db.Decimal(12, 2)
  lineTtc     Decimal  @map("line_ttc") @db.Decimal(12, 2)
  createdAt   DateTime @default(now()) @map("created_at")
  invoice     Invoice  @relation(fields: [invoiceId], references: [id], onDelete: Cascade)
  service     Service? @relation(fields: [serviceId], references: [id])

  @@index([invoiceId])
  @@map("invoice_lines")
}

model InvoiceStatusEvent {
  id         String         @id @default(uuid()) @db.Uuid
  invoiceId  String         @map("invoice_id") @db.Uuid
  fromStatus InvoiceStatus? @map("from_status")
  toStatus   InvoiceStatus  @map("to_status")
  changedAt  DateTime       @default(now()) @map("changed_at")
  invoice    Invoice        @relation(fields: [invoiceId], references: [id], onDelete: Cascade)

  @@index([invoiceId])
  @@map("invoice_status_events")
}

enum InvoiceStatus {
  draft
  sent
  paid
  cancelled
}

/// Compteur de factures par utilisateur (incrément atomique côté appli pour numéros uniques).
model InvoiceCounter {
  userId String @id @map("user_id") @db.Uuid
  year   Int
  seq    Int    @default(0)
  user   User   @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@map("invoice_counters")
}

/// Stockage des refresh tokens (JWT).
model RefreshToken {
  id        String   @id @default(uuid()) @db.Uuid
  userId    String   @map("user_id") @db.Uuid
  tokenHash String   @map("token_hash")
  expiresAt DateTime @map("expires_at")
  createdAt DateTime @default(now()) @map("created_at")
  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId])
  @@index([tokenHash])
  @@map("refresh_tokens")
}
```

### Notes Prisma

- **`InvoiceStatusEvent`** : présent en base ; tu peux retirer le modèle et la relation `events` sur `Invoice` si tu n’utilises pas l’historique.
- **`InvoiceLine.service`** : en migration SQL, la FK `service_id` est en **`ON DELETE SET NULL`**. Pour que le schéma Prisma le reflète explicitement : `onDelete: SetNull` sur la relation `service`.
- Les **`@default(uuid())`** sont gérés par le client Prisma à l’insertion ; les fichiers SQL générés n’ajoutent pas toujours `DEFAULT gen_random_uuid()` sur les colonnes `id`.

## SQL DDL (aligné migrations Prisma)

L’enum PostgreSQL créée par Prisma s’appelle **`"InvoiceStatus"`** (casse sensible). Horodatages en **`TIMESTAMP(3)`** dans les migrations actuelles.

```sql
CREATE TYPE "InvoiceStatus" AS ENUM ('draft', 'sent', 'paid', 'cancelled');

CREATE TABLE "users" (
  "id" UUID NOT NULL,
  "email" TEXT NOT NULL,
  "password_hash" TEXT NOT NULL,
  "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
  "updated_at" TIMESTAMP(3) NOT NULL,
  CONSTRAINT "users_pkey" PRIMARY KEY ("id")
);
CREATE UNIQUE INDEX "users_email_key" ON "users"("email");

CREATE TABLE "freelancer_profiles" (
  "id" UUID NOT NULL,
  "user_id" UUID NOT NULL,
  "display_name" TEXT NOT NULL,
  "legal_name" TEXT NOT NULL,
  "company_name" TEXT,
  "address_line1" TEXT NOT NULL,
  "address_line2" TEXT,
  "postal_code" TEXT NOT NULL,
  "city" TEXT NOT NULL,
  "country" TEXT NOT NULL DEFAULT 'FR',
  "vat_number" TEXT,
  "siret" TEXT,
  "updated_at" TIMESTAMP(3) NOT NULL,
  CONSTRAINT "freelancer_profiles_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "freelancer_profiles_user_id_key" UNIQUE ("user_id"),
  CONSTRAINT "freelancer_profiles_user_id_fkey"
    FOREIGN KEY ("user_id") REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE "clients" (
  "id" UUID NOT NULL,
  "user_id" UUID NOT NULL,
  "name" TEXT NOT NULL,
  "email" TEXT NOT NULL,
  "company" TEXT NOT NULL,
  "address" TEXT NOT NULL,
  "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
  "updated_at" TIMESTAMP(3) NOT NULL,
  CONSTRAINT "clients_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "clients_user_id_fkey"
    FOREIGN KEY ("user_id") REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE
);
CREATE INDEX "clients_user_id_idx" ON "clients"("user_id");

CREATE TABLE "services" (
  "id" UUID NOT NULL,
  "user_id" UUID NOT NULL,
  "title" TEXT NOT NULL,
  "hourly_rate_ht" DECIMAL(12,2) NOT NULL,
  "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
  "updated_at" TIMESTAMP(3) NOT NULL,
  CONSTRAINT "services_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "services_user_id_fkey"
    FOREIGN KEY ("user_id") REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE
);
CREATE INDEX "services_user_id_idx" ON "services"("user_id");

CREATE TABLE "invoices" (
  "id" UUID NOT NULL,
  "user_id" UUID NOT NULL,
  "client_id" UUID NOT NULL,
  "invoice_number" TEXT NOT NULL,
  "status" "InvoiceStatus" NOT NULL DEFAULT 'draft',
  "issue_date" DATE NOT NULL,
  "due_date" DATE,
  "currency" CHAR(3) NOT NULL DEFAULT 'EUR',
  "total_ht" DECIMAL(12,2) NOT NULL,
  "total_vat" DECIMAL(12,2) NOT NULL,
  "total_ttc" DECIMAL(12,2) NOT NULL,
  "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
  "updated_at" TIMESTAMP(3) NOT NULL,
  CONSTRAINT "invoices_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "invoices_user_id_fkey"
    FOREIGN KEY ("user_id") REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT "invoices_client_id_fkey"
    FOREIGN KEY ("client_id") REFERENCES "clients"("id") ON DELETE RESTRICT ON UPDATE CASCADE,
  CONSTRAINT "invoices_user_id_invoice_number_key" UNIQUE ("user_id", "invoice_number")
);
CREATE INDEX "invoices_user_id_idx" ON "invoices"("user_id");
CREATE INDEX "invoices_client_id_idx" ON "invoices"("client_id");

CREATE TABLE "invoice_lines" (
  "id" UUID NOT NULL,
  "invoice_id" UUID NOT NULL,
  "service_id" UUID,
  "line_order" INTEGER NOT NULL,
  "description" TEXT NOT NULL,
  "quantity" DECIMAL(10,2) NOT NULL,
  "unit_price_ht" DECIMAL(12,2) NOT NULL,
  "vat_rate" DECIMAL(5,4) NOT NULL,
  "line_ht" DECIMAL(12,2) NOT NULL,
  "line_vat" DECIMAL(12,2) NOT NULL,
  "line_ttc" DECIMAL(12,2) NOT NULL,
  "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
  CONSTRAINT "invoice_lines_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "invoice_lines_invoice_id_fkey"
    FOREIGN KEY ("invoice_id") REFERENCES "invoices"("id") ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT "invoice_lines_service_id_fkey"
    FOREIGN KEY ("service_id") REFERENCES "services"("id") ON DELETE SET NULL ON UPDATE CASCADE
);
CREATE INDEX "invoice_lines_invoice_id_idx" ON "invoice_lines"("invoice_id");

CREATE TABLE "invoice_status_events" (
  "id" UUID NOT NULL,
  "invoice_id" UUID NOT NULL,
  "from_status" "InvoiceStatus",
  "to_status" "InvoiceStatus" NOT NULL,
  "changed_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
  CONSTRAINT "invoice_status_events_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "invoice_status_events_invoice_id_fkey"
    FOREIGN KEY ("invoice_id") REFERENCES "invoices"("id") ON DELETE CASCADE ON UPDATE CASCADE
);
CREATE INDEX "invoice_status_events_invoice_id_idx" ON "invoice_status_events"("invoice_id");

CREATE TABLE "invoice_counters" (
  "user_id" UUID NOT NULL,
  "year" INTEGER NOT NULL,
  "seq" INTEGER NOT NULL DEFAULT 0,
  CONSTRAINT "invoice_counters_pkey" PRIMARY KEY ("user_id"),
  CONSTRAINT "invoice_counters_user_id_fkey"
    FOREIGN KEY ("user_id") REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE "refresh_tokens" (
  "id" UUID NOT NULL,
  "user_id" UUID NOT NULL,
  "token_hash" TEXT NOT NULL,
  "expires_at" TIMESTAMP(3) NOT NULL,
  "created_at" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,
  CONSTRAINT "refresh_tokens_pkey" PRIMARY KEY ("id"),
  CONSTRAINT "refresh_tokens_user_id_fkey"
    FOREIGN KEY ("user_id") REFERENCES "users"("id") ON DELETE CASCADE ON UPDATE CASCADE
);
CREATE INDEX "refresh_tokens_user_id_idx" ON "refresh_tokens"("user_id");
CREATE INDEX "refresh_tokens_token_hash_idx" ON "refresh_tokens"("token_hash");
```

## Extensions possibles (non présentes aujourd’hui)

- **`onDelete: SetNull`** explicite sur la relation Prisma `InvoiceLine` → `Service` (déjà le comportement SQL).
- Soft delete (`deleted_at`) sur `clients` / `invoices` si la politique de rétention fiscale l’exige plutôt que la suppression dure.
- Contrainte ou table dédiée si tu passes à une numérotation **plusieurs lignes par utilisateur et par an** (aujourd’hui `invoice_counters` a une seule ligne par `user_id`, avec colonnes `year` et `seq`).
