# NHFRI HIMS — Hospital Information Management System (Web Migration)

## Overview

National Heart Foundation Hospital & Research Institute (NHFH&RI) is migrating its
hospital management software from a legacy desktop (WinForms) + classic ASP.NET stack
to a modern web application. I work on this migration as part of the hospital's IT
department, delivering full-stack features end-to-end across both the new frontend
and backend.

## My Role

Full-stack developer — design and build production modules end-to-end: additive
database migrations on a live Oracle database, backend APIs, and the corresponding
React/Next.js UI, while keeping full compatibility with legacy systems that are still
running in parallel.

## Tech Stack

- **Frontend:** Next.js 14 (App Router), React, Tailwind CSS
- **Backend:** ASP.NET Core Web API, Clean Architecture (Domain / Application /
  Infrastructure), Dapper
- **Database:** Oracle 11g
- **Patterns:** Repository + Unit of Work, optimistic lock-and-retry for tables shared
  with legacy systems

## Key Modules Delivered

### 1. Appointment Management Suite

- **Book Appointment** — walk-in doctor appointment booking, reverse-engineered from a
  legacy ASPX booking screen and its underlying table, built to stay fully
  write-compatible with that legacy system (safely handles concurrent-insert
  collisions on a shared table).
- **Appointment Date Switch + Full Audit History** — a single-row "current booking
  date" control used hospital-wide, redesigned with a complete, append-only audit
  trail (who changed it, when, and to what) without altering the existing production
  table's structure.
- **Consultant Time Setup** — a scheduling tool mirroring (and improving on) an
  existing legacy desktop screen: generates per-doctor, per-day appointment slots from
  a start time, interval, and patient count, with safe concurrent-write handling
  against the legacy desktop app still writing to the same table.

### 2. Consultant Management

- Admin CRUD for mapping hospital staff to the "active consultants" shown across the
  booking modules — duplicate-name detection, department autosuggest, and manual
  ordering.
- Diagnosed and fixed a subtle Oracle/Dapper bug where a successful `INSERT` was being
  misreported as a failure (`ExecuteScalar` on a DML statement with no `RETURNING`
  clause always returns no row).

### 3. Dashboard Performance & UX Overhaul

- Replaced a single blocking `Promise.all` with independently-loading cards, so the
  page renders progressively instead of waiting on the slowest query.
- Found and fixed a dashboard widget that was pulling an entire 13,000+ row table just
  to show the latest 10 entries — replaced with server-side pagination and infinite
  scroll.

## Notable Engineering Decisions

- **Additive-only database changes** — every schema change on the live production
  database was designed to never alter or drop an existing column or table, since
  several tables are shared with legacy systems that are still running.
- **Concurrency-safe writes against legacy systems** — tables written to by both the
  new and legacy applications use table-level locking plus retry-on-collision, rather
  than assuming exclusive ownership of the table.
- **Root-caused rather than patched** — several bugs (silent save failures, a
  misreported insert failure, a timezone off-by-one) were traced to their actual root
  cause instead of being worked around.

---

*This repository is a written case study of work delivered on this project — it does
not contain the hospital's source code, data, or internal infrastructure details.*
