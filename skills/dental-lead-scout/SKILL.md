---
name: dental-lead-scout
description: Daily scout for new Indian dental clinic leads for the MVP Daddy Rs 5,000 website offer. Finds a handful of clinics, drafts outreach messages, and files everything into the repo. Drafts only, never contacts a clinic.
metadata:
  title: Dental Lead Scout
  category: productivity
  var: ""
  tags:
    - research
    - leads
    - mvp-daddy
  requires:
    - GH_READ_PAT?
---

> **${var}** — Optional. Comma-separated city names to scout this run (e.g. `Pune,Nagpur`). Empty = rotate through the built-in city list automatically.

Today is ${today}. This skill supports the operator's MVP Daddy offer: a Rs 5,000 website for Indian dental clinics. It finds clinics that need one and drafts the outreach message for each, so the operator can send them from his own outreach flow.

## Hard rule: DRAFTS ONLY

This skill NEVER contacts a clinic. No email, no WhatsApp, no SMS, no calls, no Resend, no send-email, no comments on clinic pages. The only outputs are files committed to this repo. Sending stays with the operator's separate outreach flow. Do not enable or invoke any sending pathway even if credentials appear to exist.

## Token budget (hard caps)

The operator funds runs from a small prepaid balance. Stay cheap:

- At most **5 leads** per run. Fewer is fine. Zero is a valid outcome.
- At most **4 WebSearch/WebFetch calls** per run.
- Drafts are short (see style rules). No research rabbit holes: one search result page per query is enough, never deep-crawl a directory.

## Steps

### 0. Bootstrap

```bash
mkdir -p output/dental-leads memory/topics
[ -f memory/topics/dental-lead-scout-seen.json ] || echo '{"seen":[]}' > memory/topics/dental-lead-scout-seen.json
```

Read `memory/topics/dental-lead-scout-seen.json` (the dedup LRU, cap 500 entries of `clinic-name|city` keys) and skim the most recent 2 files in `output/dental-leads/` for format continuity.

Optionally, if reachable in one cheap call, read the lead format in the operator's private repo `rohitctrl/india-dentist-leads` via `gh api repos/rohitctrl/india-dentist-leads/contents/` (or through `{GH_READ_PAT}` with `./secretcurl` when injected). READ ONLY: never write to, fork, or modify that repo. If it is not accessible, skip silently and continue.

### 1. Pick cities

If `${var}` names cities, use those. Otherwise rotate: take the day-of-year modulo the list below and pick 2 consecutive cities. Prefer Tier 2 and Tier 3 cities where clinics are least likely to have good websites.

Pune, Nagpur, Nashik, Indore, Bhopal, Lucknow, Jaipur, Patna, Ranchi, Coimbatore, Madurai, Vijayawada, Visakhapatnam, Surat, Vadodara, Ludhiana, Agra, Varanasi, Guwahati, Bhubaneswar

### 2. Scout (respect the search cap)

Good lead signals, strongest first:

1. Clinic with **no website at all** (directory listing only: Justdial, Practo, Sulekha, Google Business Profile with no site link).
2. Clinic whose site is **broken, parked, or clearly abandoned** (domain expired, placeholder page, last update years old).
3. **Newly opened** clinic (mentions of "newly opened", "now open", recent first reviews) with no site.
4. Clinic with only a Facebook or Instagram page.

Queries that work (adapt per city, at most 2 cities x 2 queries):

- `"dental clinic" <city> site:justdial.com`
- `"dental clinic" <city> "no website" OR "newly opened" OR "now open"`
- Practo/Justdial listing pages for the city, looking for listings with no website link

### 3. Qualify

Keep a lead only when ALL hold:

- It is a dental clinic (or a dentist's practice) in India.
- It matches at least one signal from step 2.
- There is a **contact path**: phone number, WhatsApp number, or email from the listing. No contact path, no lead.
- It is not already in the seen LRU or in recent `output/dental-leads/` files.

Assign each lead a priority: **P1** = no website at all with phone number, **P2** = broken/abandoned site, **P3** = social-only or newly opened.

### 4. Draft one outreach message per lead

WhatsApp-ready, under 80 words, from the operator (Rohit), offering a Rs 5,000 website for their clinic. Personalize the first line to the clinic name and city. One concrete hook per message (for example: patients searching on Google find nothing, or competitors nearby have booking sites).

**Style rules for every draft (mandatory):**

- No dashes of any kind. No em dashes, no en dashes, no hyphen connectors. Rewrite with a period, comma, or plain rephrase. Words like "follow up" stay two words.
- Short plain sentences, lowercase-friendly register, no hype words, no exclamation marks.
- Sound like a real person texting, not a marketing blast.
- End with one simple question (for example: want me to show you a sample?).

### 5. Write the output file

Write `output/dental-leads/${today}.md` (overwrite if it exists):

```markdown
# Dental leads - ${today}

Cities scouted: <cities>. Searches used: N/4. Leads: M (P1 x, P2 y, P3 z).

## 1. <Clinic name> - <City> [P1]
- Source: <listing URL>
- Contact: <phone / WhatsApp / email>
- Why a fit: <one line, the signal>
- Draft:
> <the outreach message verbatim>

(repeat per lead)
```

If zero qualified leads, still write the file with the header, `Leads: 0`, and one line on what was searched. A quiet day must be visible, not silent.

### 6. Update dedup state and log

- Append each new lead's `clinic-name|city` key to `memory/topics/dental-lead-scout-seen.json`, keeping the array capped at 500 (drop oldest).
- Append to `memory/logs/${today}.md` under a `### dental-lead-scout` heading: cities scouted, searches used, lead count by priority, output file path.

## Constraints

- Never send anything to a clinic. Repo files are the only output.
- Never modify `rohitctrl/india-dentist-leads` or any repo outside this one.
- Do not enable, edit, or dispatch other skills.
- Skip `./notify` entirely on a normal run (the operator reviews the output file himself). Notify only if the skill is broken (for example searches failing two runs in a row).
- Do not exceed the search and lead caps even if results are rich.
- Respect robots and rate limits; one polite fetch per directory page, no scraping loops.

## Exit taxonomy

- `DENTAL_SCOUT_OK leads=N` - normal run, N qualified leads (N may be 0).
- `DENTAL_SCOUT_SOURCE_MISS` - searches failed (network or rate limit); logged, no leads filed, no retry storm.
