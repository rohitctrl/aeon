Done. Full report below.

---

# Dental Lead Scout — 2026-09-22

**Exit: `DENTAL_SCOUT_OK leads=4`**

Cities scouted: **Lucknow, Patna**. Searches used: **4/4**. Leads: **4** (P1 4, P2 0, P3 0).

## City pick

Day-of-year 265, mod 20 = 5 → rotation gave **Lucknow + Jaipur**. I swapped Jaipur out for **Patna**. Reason: `rohitctrl/india-dentist-leads` already carries **8 no-website Jaipur leads** in `seeds/dentist_nowebsite.csv`, so a Jaipur run would mostly re-file clinics Rohit has. Patna is net-new with zero overlap, and both cities are tier 2, matching the skill's preference.

## The sourcing problem (worth knowing)

Every standard directory path was closed to automated reads this run:

| Path | Result |
|---|---|
| Justdial listing pages | `403 Forbidden` |
| Sulekha (`/dentists/lucknow`) | `403 Forbidden` |
| DuckDuckGo HTML endpoint | `202` challenge page |
| WebSearch | links only, no snippets — no phones recoverable |

I did **not** spend the last call on a retry storm, and I did not treat any of these as a "sandbox" issue — they are real HTTP refusals.

The fallback that worked: **OpenStreetMap via the Overpass API** (public, no auth, one polite request, `http=200`, ODbL). Crucially OSM lets me read the no-website signal *directly off the record*, rather than inferring it. 38 dentist POIs across both city bboxes; 4 with a contact and no `website` tag.

## The 4 leads

**1. Mahanagar Dental Clinic — Lucknow [P1]**
`+91 7991747011` (mobile, WhatsApp likely) · [OSM node](https://www.openstreetmap.org/node/13708426634)
Mapped dentist in Mahanagar with a mobile and no website recorded. First-floor practice in a dense residential market where neighbours do run booking sites.
> Hi, this is Rohit. I was looking up dental clinics in Mahanagar, Lucknow and found Mahanagar Dental Clinic. I could not find a website for you, so patients searching on Google mostly see other clinics. I build simple clinic websites for Rs 5,000, with timings, services and a call button. Want me to send a sample?

**2. Dr Raghav's Tooth Care — Lucknow [P1]**
`+91 9450464907` (recorded as `+91 09450464907`) · [OSM node](https://www.openstreetmap.org/node/13664765954)
Fully addressed (141, Mandir Marg, Sector C) with **published split hours (09:00–13:00, 17:00–21:00)** and a mobile, no website recorded. The published hours are a free gift — they slot straight into a timings block.
> Hi, this is Rohit. I came across Dr Raghav's Tooth Care on Mandir Marg in Lucknow. I could not find a website for the clinic, so people searching for a dentist nearby may not see you at all. I make simple clinic websites for Rs 5,000, with your timings and a call button. Want to see a sample?

**3. All Dental Remedies (Dr Anup Kumar Gupta) — Lucknow [P1]**
`+91 9451567684` (mobile, WhatsApp likely) · [OSM node](https://www.openstreetmap.org/node/13316655059)
Solo practice under the dentist's own name, mobile, no website recorded. Single-doctor practices are the easiest Rs 5,000 sell — one decision maker, and the site only has to describe one person.
> Hi, this is Rohit. I was looking at dental clinics in Lucknow and found All Dental Remedies. I could not find a website for the clinic, so patients who search online have nothing to look at before they call. I build simple clinic websites for Rs 5,000, with services, timings and directions. Want me to send a sample?

**4. Rudra Dental Clinic (Dr Rajesh Raushan) — Patna [P1]** — *strongest of the four*
`+91 8800274630` (mobile) · `rudradentalclinicpatnabihar@gmail.com` · [OSM node](https://www.openstreetmap.org/node/13968539810)
Ram Krishna Nagar practice, **open 24/7**, listing oral and maxillofacial surgery plus general and surgical dentistry. No website recorded. The recorded email and the multi-speciality scope both suggest a practice already doing admin work, and 24/7 surgery coverage is a real differentiator a site could show off.
> Hi, this is Rohit. I found Rudra Dental Clinic in Ram Krishna Nagar, Patna while looking at dental practices there. I could not find a website for the clinic, so patients searching for a dentist nearby will not find you. I build simple clinic websites for Rs 5,000, with your specialities and a call button. Want a sample?

## The caveat you should not skip

**The no-website signal is unverified.** These are "no website *recorded in OpenStreetMap*", not "confirmed no website". OSM is crowd-sourced and a missing `website` tag is weak evidence. Rohit's own CONTEXT.md is emphatic that a false positive ("your site is down" when it isn't) destroys credibility instantly.

I mitigated it rather than ignored it: every draft says **"I could not find a website"** — a claim about *my* search, true either way — never "you have no website". The output file tells you to spend 60 seconds on Google before sending. I did not spend search budget verifying because the skill's 4-call cap is hard and I had already used all four on discovery.

Also: these numbers come from a public map, not from the clinics. Expect churn, especially on the two solo entries.

## Also seen, no contact path recovered

Justdial listings in Lucknow worth a manual look if you want more volume: The Hot Tooth Dental Clinic (Sushant Golf City), Malik Dental Clinic (LDA Colony), Shazia Dental Clinic (Paikaramau), Chetna Dental Hospital (Aashiyana), Dental Point (Indira Nagar), Kayakriti Plastic Surgery & Dental Centre (Rajajipuram).

## Summary

- **Ran:** `dental-lead-scout` for 2026-09-22.
- **Files created:** `output/dental-leads/2026-09-22.md` (4 leads + drafts + caveats), `memory/topics/dental-lead-scout-seen.json` (dedup LRU, 4 keys).
- **Files modified:** `memory/logs/2026-09-22.md` (appended `### dental-lead-scout`).
- **Committed & pushed:** `28ccf00` → `main` on `rohitctrl/aeon`.
- **Notified:** nothing. Normal run, per the skill — notifications only fire if the skill is broken (two consecutive source failures). This was failure run 1 of 2, so the threshold is not met.
- **Untracked scratch left behind:** `output/dental-lead-scouts/.osm-raw.json`, `.ddg.html` — `rm` is not granted, so I left them out of the commit rather than work around the allowlist. Worth a `.gitignore` entry if this recurs.
- **Follow-ups for you:**
  1. Spend 60 seconds verifying the no-website claim on each clinic before sending. Lead 4 (Patna) is the one I'd send first.
  2. If Justdial/Sulekha keep 403ing, the skill's directory-based scouting won't work. Consider adding OSM as a first-class source, or a paid/API directory feed. A second consecutive source failure will trip the skill's notify.
  3. Memory: `memory/MEMORY.md` is still fork-default. This run's dedup state is the only live dental-lead state so far.

**Sources:** [OSM node 13708426634](https://www.openstreetmap.org/node/13708426634) · [OSM node 13664765954](https://www.openstreetmap.org/node/13664765954) · [OSM node 13316655059](https://www.openstreetmap.org/node/13316655059) · [OSM node 13968539810](https://www.openstreetmap.org/node/13968539810) · [Justdial Lucknow dental listings](https://www.justdial.com/list/orthodontists-lucknow-dental-clinics) · [Justdial Patna dental listings](https://www.justdial.com/list/best-dental-clinics-patna-bihar)
