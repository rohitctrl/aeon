Scratch files are now ignored and the drafts pass the style rules (69 and 61 words, zero dashes, both end with a question).

## Dental lead scout — 2026-09-30

**Exit: `DENTAL_SCOUT_OK leads=2`**

Cities scouted: **Surat, Vadodara** (day-of-year rotation, 273 mod 20 → index 13/14). Both tier 2, both net-new against `rohitctrl/india-dentist-leads`, which has no rows for either. Searches used 4/4. Two P1 leads filed, both Surat. Vadodara filed zero — and that is the main finding of this run.

### Leads filed (`output/dental-leads/2026-09-30.md`)

**1. Geeta Dental Hospital And Implant Centre — Surat [P1]**
`0261 2858585` / `098258 65404` · [AskLaila listing](https://www.asklaila.com/listing/Surat/puna-kumbharia-road/geeta-dental-hospital-and-implant-centre/0OusXYxT/)
Implant centre under Dr Mayur Dudhat and Dr Heena Dudhat, hospital scale, no website of its own. An exact-name search returned only third-party listings (Lybrate, IndiaDentalClinic, InfoAboutCompanies, KiviHealth) — no own domain anywhere in the results.

**2. Shine N Smile Dental Clinic — Surat [P1]**
`0261 3211984` · [AskLaila listing](https://www.asklaila.com/listing/Surat/ghod-dod-road/shine-and-smile-dental-clinic/1ajGaTUu/)
Carried by AskLaila and OpenStreetMap independently, no website in either, and a name search surfaces no official site or social page at all.

Both drafts are 69 and 61 words, no dashes of any kind, one question each. Draft text is verbatim in the output file.

### Two findings worth keeping

**AskLaila's Vadodara page is serving Bangalore data.** The Surat page is sound — every landline starts `0261` (Surat's STD code) and the localities are Surat localities. The Vadodara page returned 20 listings whose localities are *all* Bangalore (Basavanagudi, Bellandur, Koramangala, HSR Layout…) and whose landlines are *all* `080` (Bangalore's code). So Vadodara cannot be sourced from it at all. Cheap guard against the trap: check the landline's STD code against the city before filing.

**OpenStreetMap is not usable for these two cities.** The Overpass extract returned 89 dentists, of which **zero carry a `phone` tag and zero carry a `website` tag** — 84 of 89 come from a single "Local Knowledge" import. The no-website signal that carried the 2026-09-22 Lucknow/Patna run is an artifact of that import here, not evidence about any clinic. I filed no OSM leads; doing so would have produced false ones.

Source availability: open were AskLaila and healthfrog; closed were Justdial, Sulekha, Cybo, Grotal, polomap, clickedindia, bharatibiz, yellowpages.in and r.jina.ai. DuckDuckGo HTML works but rate-limits after roughly a dozen queries, which is what capped verification at two clinics. Six further Surat candidates with phone numbers are listed in the file's operator notes as unverified.

### Follow-up actions for the operator

- **Verify lead 2 before sending.** AskLaila and OSM agree on the clinic but differ on the name ("Shine And Smile" vs "Shine N Smile") and locality (Ghod Dod Road vs Citylight, adjacent areas). Confirm they are the same practice.
- **Start on mobiles, not landlines.** Both leads are landlines; only lead 1 has a mobile (`098258 65404`), which is the better WhatsApp channel.
- **Watch the sourcing method.** Two runs running have each needed a different source (OSM+phones, then AskLaila+name search). Neither is durable across cities — a third rebuild means the scout needs a stable phone-bearing source rather than a per-city workaround.
- `./notify` deliberately skipped: the run is healthy, and the skill says notify only when broken.

### Files changed

| File | Change |
|---|---|
| `output/dental-leads/2026-09-30.md` | New — the two leads, sourcing notes, operator notes |
| `memory/topics/dental-lead-scout-seen.json` | Added 2 keys to the dedup LRU (6 total, cap 500) |
| `memory/logs/2026-09-30.md` | New — run log under `### dental-lead-scout` |
| `.gitignore` | Added `.tmp-*` and `output/dental-leads/.*` |

One thing I did not do: `output/dental-leads/.ddg.html` and `.osm-raw.json` are scratch from the 2026-09-22 run that got swept into a commit by the workflow's `git add -A`. I added ignore rules so it cannot recur and flagged them in the notes, but I left the files in place rather than deleting them, since I did not create them. `rm` and `mv` are both blocked in this sandbox, so the ignore rule was the only cleanup lever available to me.

## Summary

Scouted Surat and Vadodara (rotation cities), filed 2 P1 leads both in Surat, and verified the no-website signal for each by name search. Discovered that AskLaila's Vadodara endpoint serves Bangalore listings and that OpenStreetMap's Surat/Vadodara import carries no phone or website tags — invalidating the sourcing method the previous run used. Updated the dedup LRU, wrote the run log, and added gitignore rules to stop scraper scratch leaking into the auto-commit. Operator follow-ups: confirm lead 2's identity across its two sources, prefer the mobile number, and treat the scout's sourcing method as needing a durable fix.
