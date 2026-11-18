<div align="center">

# 🏥 NHFRI HIMS
### Hospital Information Management System — Legacy-to-Web Migration

A full-stack case study from my work migrating a hospital's operations software
from a legacy desktop/classic-ASP.NET stack to a modern web application.

[![Next.js](https://img.shields.io/badge/Next.js_14-000000?style=for-the-badge&logo=next.js&logoColor=white)](#)
[![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)](#)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](#)
[![ASP.NET Core](https://img.shields.io/badge/ASP.NET_Core-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](#)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](#)
[![Oracle](https://img.shields.io/badge/Oracle_11g-F80000?style=for-the-badge&logo=oracle&logoColor=white)](#)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [My Role](#-my-role)
- [Tech Stack](#-tech-stack)
- [Modules Delivered](#-modules-delivered)
- [Engineering Highlights](#-engineering-highlights)
- [Note on Scope](#-note-on-scope)

---

## 🧭 Overview

**National Heart Foundation Hospital & Research Institute (NHFH&RI)** is migrating its
hospital management software from a legacy desktop (WinForms) + classic ASP.NET stack
to a modern web application — while the legacy systems stay live and keep writing to
the same production database throughout the migration.

I work on this migration as part of the hospital's IT department, delivering
full-stack features end-to-end: database design, backend APIs, and the React/Next.js
UI that hospital staff use every day.

## 👨‍💻 My Role

**Full-stack developer**, owning features from database to UI:

- Additive schema changes on a **live Oracle database**, never breaking the legacy
  applications still running against it
- REST APIs following Clean Architecture
- The corresponding Next.js / React interface, built for non-technical hospital staff

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | Next.js 14 (App Router), React, Tailwind CSS |
| **Backend** | ASP.NET Core Web API, Clean Architecture (Domain / Application / Infrastructure), Dapper |
| **Database** | Oracle 11g |
| **Patterns** | Repository + Unit of Work, lock-and-retry for tables shared with legacy systems |

## 🚀 Modules Delivered

### 📅 Appointment Management Suite

- **Book Appointment** — walk-in doctor appointment booking, reverse-engineered from
  a legacy ASPX booking screen and its underlying table, built to stay fully
  write-compatible with that legacy system (safely resolves concurrent-insert
  collisions on a table both apps write to).
- **Appointment Date Switch + Full Audit History** — a single-row "current booking
  date" control used hospital-wide, redesigned with a complete, append-only audit
  trail (who changed it, when, and to what) without altering the existing production
  table's structure.
- **Consultant Time Setup** — a scheduling tool mirroring (and improving on) an
  existing legacy desktop screen: generates per-doctor, per-day appointment slots
  from a start time, interval, and patient count — with the same safe
  concurrent-write handling against the legacy desktop app still writing to that
  table.

### 👨‍⚕️ Consultant Management

- Admin CRUD for mapping hospital staff to the "active consultants" shown across the
  booking modules — duplicate-name detection, department autosuggest, manual
  ordering.
- Diagnosed and fixed a subtle Oracle/Dapper bug where a successful `INSERT` was
  silently misreported as a failure (`ExecuteScalar` on a DML statement with no
  `RETURNING` clause always returns no row).

### 📊 Dashboard Performance & UX Overhaul

- Replaced a single blocking `Promise.all` with independently-loading cards, so the
  page renders progressively instead of waiting on the slowest query.
- Found and fixed a dashboard widget that was pulling an entire **13,000+ row**
  table just to show the latest 10 entries — replaced with server-side pagination
  and infinite scroll.

## 💡 Engineering Highlights

- 🔒 **Additive-only database changes** — every schema change on the live production
  database was designed to never alter or drop an existing column or table, since
  several tables are shared with legacy systems that are still running.
- ⚔️ **Concurrency-safe writes against legacy systems** — tables written to by both
  the new and legacy applications use table-level locking plus retry-on-collision,
  rather than assuming exclusive ownership.
- 🔍 **Root-caused rather than patched** — several bugs (silent save failures, a
  misreported insert failure, a timezone off-by-one) were traced to their actual
  root cause instead of being worked around.

## 📝 Note on Scope

> This repository is a **written case study** of work delivered on this project —
> it does not contain the hospital's source code, data, or internal infrastructure
> details.

