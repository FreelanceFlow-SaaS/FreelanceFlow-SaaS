# FreelanceFlow — Schéma de base de données (V1)

Modèle **PostgreSQL**, conventions **snake_case** en base, aligné PRD (clients, prestations, factures HT/TVA/TTC, statuts, profil vendeur, isolation par compte).

## Diagramme entités-relations

```mermaid
erDiagram
  users ||--o| freelancer_profiles : "1 profil vendeur"
  users ||--o{ clients : possede
  users ||--o{ services : possede
  users ||--o{ invoices : possede
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
    string company_name
    string address_line1
    string address_line2
    string postal_code
    string city
    string country
    string vat_number "nullable"
    string siret "nullable"
    timestamptz updated_at
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
| **Tenant** | Toutes les tables métier portent `user_id` (sauf `users`). Requêtes toujours filtrées par utilisateur authentifié. |
| **Profil vendeur** | `freelancer_profiles` : informations affichées sur facture/PDF (FR5/FR6). Champs légaux (SIRET, n° TVA) **optionnels** en V1 si hors scope produit. |
| **Lignes de facture** | **Snapshot** : `description`, `unit_price_ht`, `vat_rate`, montants ligne — cohérent même si la prestation est modifiée ou supprimée (`service_id` nullable, `ON DELETE SET NULL`). |
| **Totaux facture** | `total_ht`, `total_vat`, `total_ttc` **dénormalisés** sur `invoices` : source d’affichage/PDF alignée avec les lignes (FR23). Recalcul côté service à chaque mutation autorisée. |
| **Numéro de facture** | Unique **par utilisateur** : `UNIQUE (user_id, invoice_number)`. |
| **Statuts** | `draft`, `sent`, `paid`, `cancelled` — transitions à verrouiller dans l’API (PRD Mehdi). |
| **Suppression client** | Tant qu’au moins une facture référence le client : **`ON DELETE RESTRICT`** (ou logique équivalente). |
| **Monnaie** | `currency` défaut `EUR` ; montants en `NUMERIC` (jamais `float`). |

## Schéma Prisma (référence d’implémentation)

À placer dans le repo backend, par ex. `prisma/schema.prisma`. Adapter `provider` / `url` selon l’environnement.

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

enum InvoiceStatus {
  draft
  sent
  paid
  cancelled
}

model User {
  id           String   @id @default(uuid()) @db.Uuid
  email        String   @unique
  passwordHash String   @map("password_hash")
  createdAt    DateTime @default(now()) @map("created_at")
  updatedAt    DateTime @updatedAt @map("updated_at")

  profile  FreelancerProfile?
  clients  Client[]
  services Service[]
  invoices Invoice[]

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

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

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

  user     User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  invoices Invoice[]

  @@index([userId])
  @@map("clients")
}

model Service {
  id          String   @id @default(uuid()) @db.Uuid
  userId      String   @map("user_id") @db.Uuid
  title       String
  hourlyRateHt Decimal @map("hourly_rate_ht") @db.Decimal(12, 2)
  createdAt   DateTime @default(now()) @map("created_at")
  updatedAt   DateTime @updatedAt @map("updated_at")

  user  User          @relation(fields: [userId], references: [id], onDelete: Cascade)
  lines InvoiceLine[]

  @@index([userId])
  @@map("services")
}

model Invoice {
  id            String        @id @default(uuid()) @db.Uuid
  userId        String        @map("user_id") @db.Uuid
  clientId      String        @map("client_id") @db.Uuid
  invoiceNumber String        @map("invoice_number")
  status        InvoiceStatus @default(draft)
  issueDate     DateTime      @map("issue_date") @db.Date
  dueDate       DateTime?     @map("due_date") @db.Date
  currency      String        @default("EUR") @db.Char(3)
  totalHt       Decimal       @map("total_ht") @db.Decimal(12, 2)
  totalVat      Decimal       @map("total_vat") @db.Decimal(12, 2)
  totalTtc      Decimal       @map("total_ttc") @db.Decimal(12, 2)
  createdAt     DateTime      @default(now()) @map("created_at")
  updatedAt     DateTime      @updatedAt @map("updated_at")

  user    User                  @relation(fields: [userId], references: [id], onDelete: Cascade)
  client  Client                @relation(fields: [clientId], references: [id], onDelete: Restrict)
  lines   InvoiceLine[]
  events  InvoiceStatusEvent[]

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

  invoice Invoice  @relation(fields: [invoiceId], references: [id], onDelete: Cascade)
  service Service? @relation(fields: [serviceId], references: [id], onDelete: SetNull)

  @@index([invoiceId])
  @@map("invoice_lines")
}

/// Optionnel V1 : traçabilité des changements de statut (audit PRD).
model InvoiceStatusEvent {
  id        String   @id @default(uuid()) @db.Uuid
  invoiceId String   @map("invoice_id") @db.Uuid
  fromStatus InvoiceStatus? @map("from_status")
  toStatus  InvoiceStatus  @map("to_status")
  changedAt DateTime @default(now()) @map("changed_at")

  invoice Invoice @relation(fields: [invoiceId], references: [id], onDelete: Cascade)

  @@index([invoiceId])
  @@map("invoice_status_events")
}
```

### Note Prisma sur `InvoiceStatusEvent`

Si tu n’implémentes pas l’historique en V1, supprime le modèle `InvoiceStatusEvent` et la relation `events` sur `Invoice`.

## SQL DDL équivalent (référence)

```sql
CREATE TYPE invoice_status AS ENUM ('draft', 'sent', 'paid', 'cancelled');

CREATE TABLE users (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email           TEXT NOT NULL UNIQUE,
  password_hash   TEXT NOT NULL,
  created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE freelancer_profiles (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id         UUID NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
  display_name    TEXT NOT NULL,
  legal_name      TEXT NOT NULL,
  company_name    TEXT,
  address_line1   TEXT NOT NULL,
  address_line2   TEXT,
  postal_code     TEXT NOT NULL,
  city            TEXT NOT NULL,
  country         CHAR(2) NOT NULL DEFAULT 'FR',
  vat_number      TEXT,
  siret           TEXT,
  updated_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE clients (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  name        TEXT NOT NULL,
  email       TEXT NOT NULL,
  company     TEXT NOT NULL,
  address     TEXT NOT NULL,
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_clients_user_id ON clients(user_id);

CREATE TABLE services (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id          UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title            TEXT NOT NULL,
  hourly_rate_ht   NUMERIC(12, 2) NOT NULL,
  created_at       TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at       TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_services_user_id ON services(user_id);

CREATE TABLE invoices (
  id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id          UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  client_id        UUID NOT NULL REFERENCES clients(id) ON DELETE RESTRICT,
  invoice_number   TEXT NOT NULL,
  status           invoice_status NOT NULL DEFAULT 'draft',
  issue_date       DATE NOT NULL,
  due_date         DATE,
  currency         CHAR(3) NOT NULL DEFAULT 'EUR',
  total_ht         NUMERIC(12, 2) NOT NULL,
  total_vat        NUMERIC(12, 2) NOT NULL,
  total_ttc        NUMERIC(12, 2) NOT NULL,
  created_at       TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at       TIMESTAMPTZ NOT NULL DEFAULT now(),
  UNIQUE (user_id, invoice_number)
);
CREATE INDEX idx_invoices_user_id ON invoices(user_id);
CREATE INDEX idx_invoices_client_id ON invoices(client_id);

CREATE TABLE invoice_lines (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  invoice_id    UUID NOT NULL REFERENCES invoices(id) ON DELETE CASCADE,
  service_id    UUID REFERENCES services(id) ON DELETE SET NULL,
  line_order    INT NOT NULL,
  description   TEXT NOT NULL,
  quantity      NUMERIC(10, 2) NOT NULL,
  unit_price_ht NUMERIC(12, 2) NOT NULL,
  vat_rate      NUMERIC(5, 4) NOT NULL,
  line_ht       NUMERIC(12, 2) NOT NULL,
  line_vat      NUMERIC(12, 2) NOT NULL,
  line_ttc      NUMERIC(12, 2) NOT NULL,
  created_at    TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_invoice_lines_invoice_id ON invoice_lines(invoice_id);

CREATE TABLE invoice_status_events (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  invoice_id  UUID NOT NULL REFERENCES invoices(id) ON DELETE CASCADE,
  from_status invoice_status,
  to_status   invoice_status NOT NULL,
  changed_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE INDEX idx_invoice_status_events_invoice_id ON invoice_status_events(invoice_id);
```

## Extensions possibles (hors V1 minimal)

- Table `refresh_tokens` (hash, `user_id`, expiration) si refresh JWT en base.
- Numérotation séquentielle dédiée : table `invoice_counters (user_id, year, last_value)` si tu veux des numéros type `2026-0042` sans collision.
- Soft delete (`deleted_at`) sur `clients` / `invoices` si la politique de rétention fiscale l’exige plutôt que la suppression dure.
