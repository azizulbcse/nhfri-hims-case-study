<div align="center">

# 🏥 NHFRI HIMS
### Hospital Information Management System — Legacy-to-Web Migration

A full-stack case study from my work building and migrating a hospital's
operations software — from a 34-module desktop system I've co-developed since 2014,
to a modern web application.

[![Next.js](https://img.shields.io/badge/Next.js_14-000000?style=for-the-badge&logo=next.js&logoColor=white)](#)
[![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)](#)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](#)
[![ASP.NET Core](https://img.shields.io/badge/ASP.NET_Core-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](#)
[![C#](https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=csharp&logoColor=white)](#)
[![Oracle](https://img.shields.io/badge/Oracle_11g-F80000?style=for-the-badge&logo=oracle&logoColor=white)](#)

</div>

---

## 📋 Table of Contents

- [Background](#-background)
- [Web Migration — Overview](#-web-migration--overview)
- [My Role](#-my-role)
- [Tech Stack](#-tech-stack)
- [Impact at a Glance](#-impact-at-a-glance)
- [Modules Delivered](#-modules-delivered)
- [Security & Reliability Highlights](#-security--reliability-highlights)
- [Note on Scope](#-note-on-scope)

---

## 🏢 Background

Since **2014**, I've been one of the core developers on this hospital's in-house
**desktop Hospital Management System** (C# WinForms, Visual Studio, SQL Server) — a
**34-module** solution still running in production across **two hospital branches**
today. I've personally built and maintained the majority of these modules,
including:

| Area | Modules |
|---|---|
| **Accounts & Billing** | Cash Counter, General Accounts, Office Accounts, Billing, Hospital Statement, Financial Assistance Fund |
| **Patient Management** | In-Patient, Out-Patient, Record Room, Consultant Chamber |
| **Diagnostics** | Cath Lab, Echocardiography, ETT, Pathology, Radiology, Cardiac Wellness Center |
| **Inventory & Pharmacy** | General Store, Medical Store, Pharmacy |
| **HR & Admin** | HRM, Payroll, Security, Security Officer, IT, Audit, Library |
| **Other** | Surgery, HTN (Hypertension Program), Freedom Fighters' Welfare, Service & Maintenance, Utility |

Alongside this, I independently built and maintain a separate **Pharmacy POS**
system and a **Canteen Management** system for the hospital (also VS/SQL Server
based).

This repository documents the **next chapter**: migrating that decade of
hospital-domain knowledge into a modern web stack — while the legacy systems above
stay live and keep writing to the same production database throughout the
migration.

## 🧭 Web Migration — Overview

**National Heart Foundation Hospital & Research Institute (NHFH&RI)** is migrating
its day-to-day hospital operations software to a modern web application — Next.js
on the frontend, ASP.NET Core on the backend — rebuilding each module with real
production data, real concurrent users, and zero tolerance for downtime.

## 👨‍💻 My Role

**Full-stack developer**, owning features end-to-end:

- Additive schema changes on a **live Oracle database**, never breaking the legacy
  systems (desktop HMS, classic ASPX apps) still running against it
- REST APIs following Clean Architecture (Domain / Application / Infrastructure)
- The corresponding Next.js / React interface, built for non-technical hospital
  staff and the public
- Independent security reviews of features before they go live

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | Next.js 14 (App Router), React, Tailwind CSS |
| **Backend** | ASP.NET Core Web API, Clean Architecture, Dapper |
| **Database** | Oracle 11g |
| **Legacy stack (since 2014)** | C# WinForms, Classic ASP.NET, SQL Server |
| **Patterns** | Repository + Unit of Work, lock-and-retry for tables shared with legacy systems |

## 📊 Impact at a Glance

| | |
|---|---|
| 🏥 **10+ years** | building this hospital's software, 2014–present |
| 🧩 **34-module system** | the majority personally built, in the legacy desktop HMS across 2 branches |
| 🎫 **13,000+** | live complaint records handled by the rebuilt helpdesk |
| 📢 **2,200+** | live notices migrated to the new public-facing system |
| 🔒 **1 critical PII leak** | found and closed during an independent security review |

## 🚀 Modules Delivered

### 📢 Notice & Communication System

<details>
<summary><b>Public notice board, view-tracking, full audit history</b></summary>

- Public, no-login notice board (`/notices`) replacing a login-only legacy screen —
  search, date filters, "new" badges tracked per visitor, attachment preview.
- **View-tracking** ("who's seen this") for both staff and public visitors, joined
  against existing device/employee records rather than adding new tracking
  infrastructure.
- **Edit / Close / Reopen with full audit trail** — a new audit table records every
  change (who, when, what the text used to say), restricted to the original author
  or an admin.
- Hardened in a dedicated security pass: added missing author-or-admin checks on
  four endpoints that had none, restricted creation to active staff only, fixed a
  race condition in concurrent view-counting.

</details>

### 🎫 Complaint / Helpdesk System

<details>
<summary><b>Public ticketing, QR verification, print slips, full security review</b></summary>

- Public ticket submission (`/complaints`) with branch + department routing,
  priority levels, and status lookup by ticket number or QR code.
- **Printable complaint slips** reusing the app's existing letterhead print
  pipeline, with an embedded QR code linking straight to the ticket's live status.
- **Critical PII leak found and fixed**: the public page was downloading the
  hospital's entire complaint table — 13,000+ records, 12,800+ of them containing
  the reporter's name and phone number — to every visitor's browser, unauthenticated,
  and filtering it client-side. Replaced with three purpose-built, server-filtered
  endpoints that never return personal data.
- Diagnosed and fixed three backend bugs causing every complaint's status to read
  as blank; fixed a duplicate-ticket-number bug caused by two different systems
  writing to the same table with incompatible numbering schemes.

</details>

### 📅 Appointment & Consultant Suite

<details>
<summary><b>Booking, a hospital-wide date switch, and a slot-scheduling tool</b></summary>

- **Book Appointment** — walk-in booking reverse-engineered from a legacy ASPX
  screen, built to stay write-compatible with it (handles concurrent-insert
  collisions on a table both apps write to).
- **Appointment Date Switch** — a single-row "current booking date" control used
  hospital-wide, redesigned with a complete, append-only audit trail, without
  altering the existing production table.
- **Consultant Time Setup** — mirrors an existing legacy desktop screen: generates
  per-doctor daily appointment slots from a start time, interval, and patient count.
- **Consultant Management** — admin CRUD mapping hospital staff to the "active
  consultants" used across every booking module.

</details>

### 🏠 Staff Portal & Dashboard

<details>
<summary><b>Public staff portal, performance-first dashboard</b></summary>

- Rebuilt the public homepage from a generic marketing template into a real staff
  portal — live OPD/IPD/CAG/CABG counts, module shortcuts, an announcements board —
  while fixing a security issue where it had been showing real patient/billing data
  without login.
- Dashboard redesigned for performance: independently-loading cards instead of one
  blocking request, server-side pagination + infinite scroll replacing a widget that
  was pulling an entire 13,000+ row table just to show the latest 10 entries.

</details>

### 🧩 IT Asset Tracking *(early stage)*

<details>
<summary><b>Phase 1 of a planned, department-agnostic asset registry</b></summary>

- Asset registry UI (Add/Edit/Delete, duplicate-ID validation, searchable
  dropdowns) designed so Department/Category/Branch stay dynamic master data —
  built to be reusable by other departments (Biomedical, Facilities), not
  IT-specific.

</details>

### ⚙️ Platform Engineering

<details>
<summary><b>Menu/permissions system, Oracle driver bugs, branch master data</b></summary>

- Diagnosed and fixed an Oracle parameter-binding bug (missing `BindByName`)
  causing silent data corruption across multiple admin screens — shipped a reusable
  wrapper to prevent it recurring.
- Designed a dynamic, database-driven menu + role-permission system so new pages
  can be wired up without a deployment.
- Introduced a proper master-data table for hospital branches, replacing an
  ad-hoc, schema-per-branch convention.

</details>

## 🔒 Security & Reliability Highlights

- 🔐 **Found and closed a live PII leak** — a public page was exposing 13,000+
  patients' and staff's names and phone numbers before login; replaced with
  properly scoped, server-filtered endpoints.
- 🕵️ **Independent security reviews** — ran dedicated review passes (treating my
  own feature work adversarially) that caught authorization gaps, spoofable
  identity checks, and PII exposure before each feature's public launch.
- ⚔️ **Concurrency-safe against legacy systems** — tables written to by both the
  new app and still-running legacy systems use table-level locking plus
  retry-on-collision, instead of assuming exclusive ownership.
- 🔒 **Additive-only database changes** — every schema change on the live
  production database was designed to never alter or drop an existing column or
  table.
- 🔍 **Root-caused, not patched** — silent save failures, misreported insert
  failures, duplicate IDs, and a timezone off-by-one were all traced to their
  actual root cause rather than worked around.

## 📝 Note on Scope

> This repository is a **written case study** of work delivered across these
> projects — it does not contain the hospital's source code, data, or internal
> infrastructure details.

