# PostgreSQL Ecosystem: Critical Extensions & Drivers

## Overview

This document catalogs the critical extensions and drivers in the PostgreSQL
ecosystem, their maintainer situations, downstream dependency counts, and
associated bus-factor risks.

**Key finding:** 16 out of ~25 critical projects are effectively maintained by
a single person, despite collectively serving millions of downstream projects
and billions of package downloads.

---

## 1. Drivers

### 1.1 Dependency & Usage Summary

| Driver | Language | Maintainers | Single-Person? | Primary Maintainer | Downstream Dependents | Downloads |
|---|---|---|---|---|---|---|

> **Note on download counts:** Download numbers are not comparable across package
> managers. Maven caches artifacts locally (`~/.m2/repository`) so each dependency
> is downloaded once per machine. PyPI and npm re-download on every `pip install`
> or `npm install` without explicit caching — CI/CD pipelines, Docker builds, and
> virtual environments each trigger fresh downloads. PgJDBC at ~20M Maven
> downloads/month likely represents similar real-world usage to psycopg2 at
> ~250M PyPI downloads/month. Download counts are only meaningful for comparing
> projects within the same ecosystem.

| libpq | C | ~31 committers | No | PostgreSQL core team | 20+ drivers, all PG tools | Ships with PostgreSQL |
| psqlODBC | C | 1 | Yes | Dave Cramer (AWS) | Power BI, Excel, Tableau, SSIS, etc. | N/A (system driver) |
| libpqxx | C++ | 1 | Yes | Jeroen T. Vermeulen | ~16 tagged repos | N/A |
| PgJDBC | Java | 2-3 | No | Dave Cramer | ~6,400 Maven artifacts | N/A |
| R2DBC PostgreSQL | Java | 1 | Yes | Mark Paluch | ~134 Maven artifacts | N/A |
| psycopg2 | Python | 1 | Yes | Daniele Varrazzo | ~6,850 packages | 250M/month (5.7B total) |
| psycopg3 | Python | 1 | Yes | Daniele Varrazzo | 672 packages | 54M/month |
| asyncpg | Python | 1 | Yes | Elvis Pranskevichus | 1,410 packages | 61M/month |
| Npgsql | .NET | 4 | No | Shay Rojansky (Microsoft) | ~3,050 packages | 775.8M total NuGet |
| node-postgres (pg) | Node.js | 1 | Yes | Brian Carlson | 13,747 npm packages | 22.3M/week |
| postgres.js | Node.js | 1 | Yes | Rasmus Porsager | 870 npm packages | 6.4M/week |
| pgx | Go | 1 | Yes | Jack Christensen | 8,606 Go modules (v5) | N/A |
| lib/pq | Go | 1 | Yes | Martin Tournoij | 54,638 Go modules | N/A |
| pg gem | Ruby | 1 | Yes | Lars Kanis | 2,074 gems | 434M total |
| rust-postgres | Rust | 1 | Yes | Paolo Barbolini | 1,073 crates (tokio-postgres) | 28.5M total |
| PDO_PGSQL | PHP | 0 dedicated | No dedicated owner | PHP core team | Laravel, Symfony, Drupal, etc. | Ships with PHP |
| DBD::Pg | Perl | 1 | Yes | Greg Sabino Mullane | ~50-100 CPAN dists | N/A |

### 1.2 Driver Details

#### libpq (C) — The Foundation
- **Maintainers:** PostgreSQL Global Development Group (~31 committers, 200+ contributors/year)
- **Bus factor:** Very high — well-governed community project
- **Role:** The canonical C client library. Nearly every other driver either wraps libpq or reimplements the PostgreSQL wire protocol. All standard tools (psql, pg_dump, pg_restore) use it.

#### psqlODBC (C)
- **Maintainers:** Dave Cramer (sole release manager, all 35 GitHub releases, AWS). Hiroshi Inoue (historical lead). Christian Ullrich (active contributor).
- **Bus factor:** Low
- **Dependents:** Critical for enterprise/BI tools — Power BI, Excel, Tableau, SSIS, SSRS, Crystal Reports, MicroStrategy, LibreOffice Base, and any application using unixODBC/iODBC.
- **Note:** The global ODBC market is projected at USD 12.07B by 2035.

#### libpqxx (C++)
- **Maintainers:** Jeroen T. Vermeulen — sole maintainer since 2000. Has disclosed health problems affecting capacity.
- **Bus factor:** Very low
- **Dependents:** Niche but important as the official C++ PostgreSQL API. Used in backend services, game servers, embedded systems.

#### PgJDBC (Java)
- **Maintainers:**  Dave Cramer (AWS), Vladimir Sitnikov, Jorge Solórzano, Sehrope Sarkuni, Brett Okken
- **Bus factor:** Moderate — small but real team
- **Dependents:** ~6,400 Maven artifacts. The standard Java PostgreSQL driver used by Spring, Hibernate, every Java enterprise stack.

#### R2DBC PostgreSQL (Java)
- **Maintainers:** Mark Paluch (sole committer, also Spring Data lead at VMware/Broadcom)
- **Bus factor:** Low
- **Dependents:** ~134 Maven artifacts. Niche reactive/non-blocking use case.

#### psycopg2 (Python)
- **Maintainers:** Daniele Varrazzo — sole maintainer for 10+ years. Now in maintenance mode.
- **Bus factor:** Very low
- **Dependents:** ~6,850 packages. 250M downloads/month. Used by pandas, SQLAlchemy, Django, Apache Airflow, Apache Beam, Great Expectations, dbt-postgres. The default PostgreSQL driver for the entire Python ecosystem.

#### psycopg3 (Python)
- **Maintainers:** Daniele Varrazzo — same person as psycopg2. Has explicitly asked for co-maintainers.
- **Bus factor:** Very low (same person maintains both the legacy and modern Python drivers)
- **Dependents:** 672 packages. 54M downloads/month. Adopted by SQLAlchemy 2.0 and Django 4.2+ as the recommended successor.

#### asyncpg (Python)
- **Maintainers:** Elvis Pranskevichus (sole active maintainer). Yury Selivanov (original creator) stepped back.
- **Bus factor:** Low. 243 open issues and 26 open PRs suggest limited bandwidth.
- **Dependents:** 1,410 packages. 61M downloads/month. Dominates the async/AI/LLM space — LlamaIndex, Pydantic AI, sentry-sdk, Tortoise ORM.

#### Npgsql (.NET)
- **Maintainers:** Shay Rojansky (Microsoft employee), Nino Floris, Nikita Kazmin, Brar Piening
- **Bus factor:** Moderate-high — institutional backing from Microsoft
- **Dependents:** ~3,050 NuGet packages. 775.8M total downloads. Npgsql.EntityFrameworkCore.PostgreSQL alone has 384.7M downloads.

#### node-postgres / pg (Node.js)
- **Maintainers:** Brian Carlson — sole maintainer for 14+ years
- **Bus factor:** Very low
- **Dependents:** 13,747 npm packages. 22.3M weekly downloads. One of the most depended-upon npm packages.

#### postgres.js (Node.js)
- **Maintainers:** Rasmus Porsager — sole creator and maintainer
- **Bus factor:** Low
- **Dependents:** 870 npm packages. 6.4M weekly downloads. Growing alternative to node-postgres.

#### pgx (Go)
- **Maintainers:** Jack Christensen — sole maintainer
- **Bus factor:** Low
- **Dependents:** 8,606 Go modules (v5 alone, ~15,400 across all versions). The recommended Go PostgreSQL driver.

#### lib/pq (Go)
- **Maintainers:** Martin Tournoij (revived project from near-dormancy in 2024-2025)
- **Bus factor:** Low
- **Dependents:** 54,638 Go modules — one of the most imported Go packages. Widely considered legacy but massive installed base.

#### pg gem (Ruby)
- **Maintainers:** Lars Kanis (de facto primary). Michael Granger (repo owner, minimal recent activity).
- **Bus factor:** Low
- **Dependents:** 2,074 gems. 434M total downloads. Required by every Rails application using PostgreSQL.

#### rust-postgres / tokio-postgres (Rust)
- **Maintainers:** Paolo Barbolini (took over from Steven Fackler who stepped back)
- **Bus factor:** Low
- **Dependents:** 1,073 crates (tokio-postgres), 451 crates (sync postgres). Pure Rust — does not use libpq.

#### PDO_PGSQL / pgsql (PHP)
- **Maintainers:** No dedicated owner. Maintained collectively by PHP core team.
- **Bus factor:** Medium (no single point of failure, but no champion either)
- **Dependents:** Used by Laravel, Symfony, CakePHP, Yii2, Drupal, and essentially every PHP application that connects to PostgreSQL. PHP powers ~77% of websites with known server-side language.

#### DBD::Pg (Perl)
- **Maintainers:** Greg Sabino Mullane (Crunchy Data). esabol (active secondary contributor).
- **Bus factor:** Low
- **Dependents:** ~50-100 CPAN distributions. The standard PostgreSQL driver for Perl's DBI.

---

## 2. Extensions

### 2.1 Core/Contrib Extensions

These ship with PostgreSQL and are maintained by the PostgreSQL Global Development Group.
Bus factor is generally high (community-maintained), though many were originally authored
by specific individuals.

| Extension | Category | Key Original Authors | Managed Service Support |
|---|---|---|---|
| pg_stat_statements | Performance | Takahiro Itagaki | ✅ All major providers |
| pg_trgm | Text Search | Oleg Bartunov, Teodor Sigaev | ✅ All major providers |
| btree_gist | Indexing | Janko Richter | ✅ All major providers |
| btree_gin | Indexing | Oleg Bartunov, Teodor Sigaev | ✅ All major providers |
| pg_prewarm | Performance | Robert Haas | ✅ All major providers |
| hstore | Data Types | Oleg Bartunov, Teodor Sigaev | ✅ All major providers |
| uuid-ossp | Data Types | Andrew Gierth | ✅ All major providers |
| pgcrypto | Security | Marko Kreen | ✅ All major providers |
| citext | Data Types | David Wheeler | ✅ All major providers |
| intarray | Data Types | Oleg Bartunov, Teodor Sigaev | ✅ All major providers |
| ltree | Data Types | Oleg Bartunov, Teodor Sigaev | ✅ All major providers |
| unaccent | Text Search | Community | ✅ All major providers |
| postgres_fdw | Federation | Community | ✅ All major providers |
| file_fdw | Federation | Community | ✅ All major providers |
| pg_buffercache | Admin | Mark Kirkwood | ✅ All major providers |
| pg_visibility | Admin | Robert Haas | ✅ All major providers |
| pageinspect | Admin | Community | ✅ All major providers |
| auto_explain | Admin | Takahiro Itagaki | ✅ All major providers |

**Notable:** Oleg Bartunov and Teodor Sigaev (Moscow State University) authored 6 of these
extensions (hstore, ltree, intarray, pg_trgm, btree_gin, and contributions to btree_gist).

**Adoption highlight:** pg_stat_statements is installed in 1M+ databases on Neon alone and
is near-universally enabled on production deployments. pgcrypto is #5 most popular on Neon
(20k+ installs).

### 2.2 Third-Party Extensions

| Extension | Category | Maintainers | Single-Person? | Primary Maintainer | GitHub Stars | Managed Service Support |
|---|---|---|---|---|---|---|
| pg_cron | Scheduling | 1 | Yes | Marco Slot (Citus/Microsoft) | 3.7k | ✅ 15+ providers |
| pg_partman | Partitioning | 1 | Yes | Keith Fiske | 2k | ✅ All major providers |
| pgAudit | Security | 1 | Yes | David Steele | 1.6k | ✅ All major providers |
| pglogical | Replication | Small team | No | 2ndQuadrant/EDB | 1.2k | ✅ Most providers (⚠️ limited on serverless) |

---

## 3. Risk Assessment

### 3.1 Highest Risk: Single-Person Projects with Massive Downstream Impact

| Project | Maintainer | Downstream Impact | Risk Level |
|---|---|---|---|
| psycopg2 + psycopg3 | Daniele Varrazzo | 250M+ downloads/month, 7,500+ dependent packages, pandas/Django/Airflow | 🔴 Critical |
| node-postgres (pg) | Brian Carlson | 22.3M weekly npm downloads, 13,747 dependent packages | 🔴 Critical |
| lib/pq | Martin Tournoij | 54,638 Go modules (one of most imported Go packages) | 🔴 Critical |
| pgx | Jack Christensen | 8,606+ Go modules, recommended Go PG driver | 🟠 High |
| asyncpg | Elvis Pranskevichus | 61M downloads/month, 1,410 packages, LLM/AI ecosystem | 🟠 High |
| pg gem | Lars Kanis | 434M total downloads, 2,074 gems, all Rails+PG apps | 🟠 High |
| libpqxx | Jeroen T. Vermeulen | Official C++ PG API, maintainer has health issues | 🟠 High |
| psqlODBC | Dave Cramer | All ODBC-based BI/enterprise tools | 🟠 High |

### 3.2 Moderate Risk: Small Teams or Institutional Backing

| Project | Team Size | Mitigating Factor |
|---|---|---|
| PgJDBC | 2-3 active | Small but real team |
| Npgsql | 4 active | Microsoft institutional backing |
| pg_cron | 1 | Microsoft/Citus backing |
| pglogical | Small team | EDB backing |

### 3.3 Low Risk: Community/Organization Maintained

| Project | Maintainer Situation |
|---|---|
| libpq | PostgreSQL core team (~31 committers) |
| Core contrib extensions | PostgreSQL Global Development Group |
| PDO_PGSQL | PHP core team (no dedicated owner, but no single point of failure) |

### 3.4 The libpq Dependency Tree

libpq is the foundation of the PostgreSQL client ecosystem. The following
drivers wrap it directly:

```
libpq
├── psqlODBC → Power BI, Excel, Tableau, SSIS, ...
├── libpqxx → C++ applications
├── psycopg2 → 250M downloads/month → pandas, Django, Airflow, ...
├── psycopg3 → 54M downloads/month → SQLAlchemy 2.0, Django 4.2+, ...
├── pg gem → 434M total downloads → Ruby on Rails ecosystem
├── DBD::Pg → Perl DBI ecosystem
├── PDO_PGSQL → Laravel, Symfony, Drupal, ...
└── psql, pg_dump, pg_restore, ...
```

Drivers that reimplement the wire protocol (no libpq dependency):
- PgJDBC (Java) — 6,400 Maven artifacts
- R2DBC PostgreSQL (Java)
- asyncpg (Python)
- pgx (Go)
- lib/pq (Go)
- node-postgres (Node.js)
- postgres.js (Node.js)
- Npgsql (.NET)
- rust-postgres (Rust)

---

## 4. Fork Activity & Community Readiness

This section assesses whether each project has active forks that could serve as
a successor if the primary maintainer steps away. "Active forks" means forks
with commits ahead of the main repo (independent development, not just PRs).

### 4.1 Drivers

| Project | Forks | Active/Notable Forks | Well-Resourced Forks | Successor Readiness |
|---|---|---|---|---|
| libpq | N/A | PostgreSQL core | N/A | ✅ Not at risk |
| psqlODBC | 40 | IvorySQL/isqlodbc (product fork) | None | 🔴 No viable successor |
| libpqxx | 283 | ClickHouse/libpqxx (internal use) | ClickHouse (purpose-built) | 🔴 No general successor |
| PgJDBC | 923 | arenadata/pgjdbc, ali-security forks | Arenadata | ✅ Not at risk (team-maintained) |
| R2DBC PostgreSQL | 187 | appsmithorg fork | None | 🟠 Low fork activity |
| psycopg2 | 533 | Netezza adaptations, ansible fork | None | N/A (psycopg3 is official successor) |
| psycopg3 | 239 | Materialize, ankane (pgvector), Dalibo | Materialize | 🟠 Some corporate interest |
| asyncpg | 438 | **athenianco/asyncpg-rkt** (46★, own PyPI package) | Athenianco | 🟡 One viable divergent fork |
| Npgsql | 876 | HuaweiCloud/gaussdb-dotnet, Yugabyte | Huawei, Yugabyte | ✅ Not at risk (team + corporate forks) |
| node-postgres | ~1,300 | benjie (PostGraphile), jawj (Neon), Yugabyte, Databricks | Multiple corporate | 🟡 benjie fork most likely successor |
| postgres.js | 345 | jawj, ds300 | None | 🔴 No viable successor |
| pgx | ~1,000 | CockroachDB, Yugabyte, HuaweiCloud, SmartContract, LaunchDarkly, Segment | CockroachDB, Yugabyte | 🟢 Strong corporate fork base |
| lib/pq | 956 | CockroachDB, Greenplum, Confluent | CockroachDB | ✅ pgx is official recommended replacement |
| pg gem | 189 | Shopify, byroot (Rails core), tenderlove (Rails core), SamSaffron (Discourse) | Shopify, Rails core team | 🟢 Strong Rails community backing |
| rust-postgres | 542 | **Neon, Materialize, Prisma, Discord, Timescale, Embark, Huawei** | Multiple well-funded | 🟢 Strongest fork insurance of any project |
| PDO_PGSQL | N/A | Part of php-src | N/A | ✅ PHP core project |
| DBD::Pg | 42 | timbunce (DBI author), fluca1978 | None | 🔴 Minimal fork activity |

### 4.2 Extensions

| Project | Forks | Notable Active Forks | Successor Readiness |
|---|---|---|---|
| pg_partman | ~312 | Cloud provider adaptations | 🟠 No standout successor fork |
| pgAudit | ~189 | Cloud provider adaptations | 🟠 No standout successor fork |
| pg_cron | ~244 | Cloud provider adaptations | 🟡 Microsoft/Citus backing mitigates risk |
| pglogical | ~174 | EDB internal forks | 🟡 EDB backing mitigates risk |

### 4.3 Key Findings

**Best "insurance" via well-resourced forks:**
- **rust-postgres** — Neon, Materialize, Prisma, Discord, Timescale all maintain active forks
- **pgx (Go)** — CockroachDB, Yugabyte, Huawei maintain active forks
- **pg gem (Ruby)** — Shopify and multiple Rails core team members have forks

**Most vulnerable (single maintainer + no viable successor fork):**
- **psqlODBC** — 40 forks, none with independent development
- **postgres.js** — 345 forks, no well-resourced organizational fork
- **libpqxx** — 283 forks, only ClickHouse has a purpose-built fork (not a general successor)
- **DBD::Pg** — 42 forks, minimal activity

**Notable:** Huawei (GaussDB) and Yugabyte maintain forks across nearly every
driver in this list, suggesting they could step in as maintainers if needed for
their own product lines, but not necessarily for the community at large.

---

## 5. Conclusions

1. **The PostgreSQL driver ecosystem has a severe bus-factor problem.** 16 of ~25
   critical projects are effectively single-person maintained, collectively serving
   billions of downloads and tens of thousands of dependent packages.

2. **Python is the highest-risk language.** One person (Daniele Varrazzo) maintains
   both the legacy and modern PostgreSQL drivers, serving 250M+ PyPI downloads/month
   combined. He has publicly asked for co-maintainers.

3. **Only 3 driver projects have genuine multi-person teams:** PgJDBC (Java),
   Npgsql (.NET, Microsoft-backed), and libpq itself (PostgreSQL core).

4. **Core contrib extensions are well-maintained** by the PostgreSQL community, but
   critical third-party extensions (pg_partman, pgAudit, pg_cron) are each
   single-person projects, albeit with some institutional backing.

5. **The ODBC driver (psqlODBC) is a critical enterprise dependency** bridging
   PostgreSQL to the entire BI/reporting tool ecosystem, maintained by one person.

6. **Fork activity does not uniformly mitigate risk.** While rust-postgres, pgx,
   and the pg gem have strong corporate fork bases that could step in, the most
   critical single-maintainer projects (psycopg2/3, node-postgres, psqlODBC) have
   no viable successor forks despite their massive downstream impact.
