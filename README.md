# app-build-workflow

**English** · [Bahasa Indonesia](#bahasa-indonesia)

A Claude Code skill for building applications **from zero**, in stages, using two levers to their full potential:

- **Ultracode**: multi-agent workflows that design, build and verify in parallel at the stages where it pays off (architecture, independent modules, critical-module review, final audit, stuck bugs).
- **Effort levels**: the right reasoning depth for each stage, from High to Max, for both the main session and every subagent.

It also invokes companion skills automatically (security, minimal backend code, frontend design), so you don't have to name them.

## How it works

| # | Stage | Mode | Effort |
|---|---|---|---|
| 1 | Requirements: review the rough plan, ask clarifying questions | Normal | High |
| 2 | Full plan + architecture: 3 competing designs, judge panel, synthesis | **Ultracode** | Extra (Max for financial) |
| 3 | UI design: design system, mockups, self-critique, handoff | Normal (variations optional) | High |
| 4a | Foundation: scaffold, schema, core auth, tests | Normal | Extra |
| 4b | Independent modules: parallel build in git worktrees + verify | **Ultracode** (if conditions met) | Extra |
| 4c | Critical modules (payments, balances, auth): build, then adversarial review | Normal + **Ultracode** review | Max |
| 4d | Integration: wiring and end-to-end flows | Normal | Extra |
| 5 | Functional + security audit, adversarial verification, loop until dry | **Ultracode** | Max |
| 6 | Manual testing + bug fixing (escalates when stuck) | Normal → **Ultracode** | High → Max |
| 7 | Deploy + go-live checklist | Normal | High |

Every stage ends with a short report and waits for you to say "continue". The report includes the recommended effort for the next stage.

### Ultracode rules

- **Required** stages run the multi-agent workflow without asking.
- **Optional** stages show the scope and agent count and wait for your "ok".
- **Normal** stages never ask about ultracode.
- Small projects (fewer than ~5 modules, no money or sensitive data) downgrade required stages to optional. Financial or regulated projects never downgrade.

Invoking the skill yourself (`/app-build-workflow`) counts as permission for all required ultracode stages. If it triggers automatically, Claude asks once per project.

### Effort rules

- Every agent inside a workflow gets an explicit effort: the stage's level for thinking agents, `medium` for mechanical ones (listing files, running tests).
- The main session's effort can only be changed by you (`/effort`). The skill reminds you when it doesn't match, and continues automatically after 5 minutes if your environment supports scheduled messages.

## Companion skills

The skill uses these automatically when installed, and recommends them once when they are missing. Each job has exactly one owner, so reviews are not duplicated.

| Skill | Job | Stages |
|---|---|---|
| [security-guidance](https://github.com/anthropics/claude-plugins-official) | Passive security check on every edit and diff | All |
| [security-audit](https://github.com/cloudflare/security-audit-skill) | Threat model, focused and full security audits | 2, 4c, 5 |
| [ponytail](https://github.com/DietrichGebert/ponytail) | Minimal backend code; review of critical diffs; shortcut ledger | 4, 6, 7 |
| [impeccable](https://github.com/pbakaus/impeccable) | Frontend design, UI build polish, UI audit | 3, 4, 5 |
| [diagram-design](https://github.com/cathrynlavery/diagram-design) (optional) | Architecture, data-flow and ER diagrams | 2 |
| Anthropic `engineering` plugin (optional) | System design, debugging, deploy checklist, docs | 2, 6, 7 |

**Deliberately excluded to avoid overlap:** a third code reviewer in the audit (the ultracode audit plus `security-audit` already cover it), `ponytail-review` on every diff (`security-guidance` already reviews each diff), and generic design skills alongside `impeccable` (used only as a fallback).

Install the companions:

```bash
# Passive security review (official Anthropic marketplace)
/plugin install security-guidance@claude-plugins-official

# Security audits (Cloudflare)
npx skills add https://github.com/cloudflare/security-audit-skill --skill security-audit --global

# Minimal backend code
/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail

# Frontend design
/plugin marketplace add pbakaus/impeccable
/plugin install impeccable@impeccable

# Optional: diagrams
/plugin marketplace add cathrynlavery/diagram-design
/plugin install diagram-design@diagram-design
```

Restart Claude Code after installing plugins so their hooks load.

## Installation

**Option A: as a plugin**

```bash
/plugin marketplace add mochalvi/app-build-workflow
/plugin install app-build-workflow@app-build-workflow
```

**Option B: as a skill**

```bash
npx skills add https://github.com/mochalvi/app-build-workflow --skill app-build-workflow --global
```

**Option C: manually**

Copy `skills/app-build-workflow/` into `~/.claude/skills/` (all projects) or `.claude/skills/` inside a project.

## Usage

Just describe the app you want to build from scratch, for example:

> Build an invoicing web app for small agencies. Here's my rough plan: …

Or invoke it directly so all required ultracode stages run without the confirmation question:

> /app-build-workflow Build an invoicing web app for small agencies …

Then answer the clarifying questions and say "continue" at each stage gate.

## Requirements

- Claude Code with skills support. Ultracode stages need the Workflow tool; without it, the skill approximates them with parallel subagents.
- Effort levels `high`, `xhigh` and `max` must be available for your model.
- Token use is significantly higher in ultracode stages. That is the trade-off for broader coverage and verified results.

## License

MIT

---

## Bahasa Indonesia

Skill Claude Code untuk membangun aplikasi **dari nol** secara bertahap, dengan memaksimalkan dua hal:

- **Ultracode**: workflow multi-agent yang merancang, membangun, dan memverifikasi secara paralel di tahap yang paling membutuhkannya (arsitektur, modul independen, review modul kritis, audit akhir, bug yang buntu).
- **Effort**: kedalaman berpikir yang tepat untuk tiap tahap, dari High sampai Max, untuk sesi utama maupun setiap sub-agent.

Skill ini juga memanggil skill pendamping secara otomatis (security, kode backend ringkas, desain frontend), jadi Anda tidak perlu menyebutnya satu per satu.

## Cara kerja

| # | Tahap | Mode | Effort |
|---|---|---|---|
| 1 | Requirement: review plan kasar, ajukan pertanyaan klarifikasi | Normal | High |
| 2 | Plan lengkap + arsitektur: 3 desain bersaing, panel juri, sintesis | **Ultracode** | Extra (Max untuk finansial) |
| 3 | Desain UI: design system, mockup, kritik mandiri, handoff | Normal (variasi opsional) | High |
| 4a | Fondasi: scaffold, schema, auth inti, test | Normal | Extra |
| 4b | Modul independen: build paralel di git worktree + verifikasi | **Ultracode** (jika syarat terpenuhi) | Extra |
| 4c | Modul kritis (pembayaran, saldo, auth): build, lalu review adversarial | Normal + review **Ultracode** | Max |
| 4d | Integrasi: menyambungkan modul dan uji alur end-to-end | Normal | Extra |
| 5 | Audit fungsi + security, verifikasi adversarial, diulang sampai tidak ada temuan baru | **Ultracode** | Max |
| 6 | Testing manual + perbaikan bug (eskalasi kalau buntu) | Normal → **Ultracode** | High → Max |
| 7 | Checklist deploy + go-live | Normal | High |

Setiap tahap diakhiri laporan singkat dan menunggu Anda bilang "lanjut". Laporan itu menyertakan effort yang disarankan untuk tahap berikutnya.

### Aturan ultracode

- Tahap **wajib** menjalankan workflow multi-agent tanpa bertanya.
- Tahap **opsional** menampilkan cakupan dan jumlah agent, lalu menunggu "ok" dari Anda.
- Tahap **normal** tidak pernah bertanya soal ultracode.
- Proyek kecil (kurang dari ~5 modul, tanpa uang atau data sensitif) menurunkan tahap wajib menjadi opsional. Proyek finansial atau yang diatur regulasi tidak pernah turun.

Memanggil skill sendiri (`/app-build-workflow`) dianggap sebagai izin untuk semua tahap ultracode wajib. Kalau skill terpicu otomatis, Claude akan bertanya sekali per proyek.

### Aturan effort

- Setiap agent di dalam workflow diberi effort secara eksplisit: sesuai level tahap untuk agent yang berpikir, `medium` untuk agent mekanis (daftar file, menjalankan test).
- Effort sesi utama hanya bisa Anda ubah sendiri (`/effort`). Skill akan mengingatkan kalau tidak sesuai, lalu lanjut otomatis setelah 5 menit jika lingkungan Anda mendukung pesan terjadwal.

## Skill pendamping

Skill ini memakai skill berikut secara otomatis jika terpasang, dan merekomendasikannya sekali jika belum ada. Setiap tugas hanya punya satu pemilik, supaya review tidak dobel.

| Skill | Tugas | Tahap |
|---|---|---|
| [security-guidance](https://github.com/anthropics/claude-plugins-official) | Cek security pasif di setiap edit dan diff | Semua |
| [security-audit](https://github.com/cloudflare/security-audit-skill) | Threat model, audit security terarah dan menyeluruh | 2, 4c, 5 |
| [ponytail](https://github.com/DietrichGebert/ponytail) | Kode backend ringkas, review diff modul kritis, daftar shortcut | 4, 6, 7 |
| [impeccable](https://github.com/pbakaus/impeccable) | Desain frontend, polish UI saat build, audit UI | 3, 4, 5 |
| [diagram-design](https://github.com/cathrynlavery/diagram-design) (opsional) | Diagram arsitektur, data flow, dan ER | 2 |
| Plugin `engineering` dari Anthropic (opsional) | System design, debugging, checklist deploy, dokumentasi | 2, 6, 7 |

**Sengaja tidak dipakai bersamaan supaya tidak tumpang tindih:** reviewer kode ketiga di tahap audit (sudah dicakup audit ultracode dan `security-audit`), `ponytail-review` di setiap diff (`security-guidance` sudah mereview tiap diff), dan skill desain generik bersama `impeccable` (hanya dipakai sebagai cadangan).

Perintah instalasinya sama dengan bagian English di atas. Restart Claude Code setelah memasang plugin supaya hook-nya termuat.

## Instalasi

**Opsi A: sebagai plugin**

```bash
/plugin marketplace add mochalvi/app-build-workflow
/plugin install app-build-workflow@app-build-workflow
```

**Opsi B: sebagai skill**

```bash
npx skills add https://github.com/mochalvi/app-build-workflow --skill app-build-workflow --global
```

**Opsi C: manual**

Salin folder `skills/app-build-workflow/` ke `~/.claude/skills/` (semua project) atau `.claude/skills/` di dalam project.

## Cara pakai

Cukup jelaskan aplikasi yang ingin dibangun dari nol, misalnya:

> Buatkan web app invoice untuk agensi kecil. Ini plan kasarnya: …

Atau panggil langsung supaya semua tahap ultracode wajib berjalan tanpa pertanyaan konfirmasi:

> /app-build-workflow Buatkan web app invoice untuk agensi kecil …

Setelah itu jawab pertanyaan klarifikasi, lalu bilang "lanjut" di setiap akhir tahap.

## Kebutuhan

- Claude Code yang mendukung skill. Tahap ultracode butuh Workflow tool; tanpa itu, skill akan mendekatinya dengan sub-agent paralel.
- Level effort `high`, `xhigh`, dan `max` harus tersedia untuk model yang Anda pakai.
- Pemakaian token jauh lebih besar di tahap ultracode. Itu konsekuensi dari cakupan yang lebih luas dan hasil yang terverifikasi.

## Lisensi

MIT
