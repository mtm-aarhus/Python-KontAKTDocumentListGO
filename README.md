# Python-KontAKTDocumentListGO

Fetches the document list for a single **GO** case and pushes it into **KontAKT** (the aktindsigt / FOI request system).

KontAKT triggers this when a caseworker adds a GO case to an aktindsigt, or refreshes it.

## What it does

For one GO case:

1. Reads the case metadata (title + URL).
2. Discovers the relevant document views.
3. Pages through every document row in the case.
4. For each document, resolves its *bilag* (attachment) relationships.
5. Posts the assembled document list to KontAKT's import endpoint, together with any warnings (e.g. documents missing a date, or act number 0).

Documents that look already-redacted (memo / tunnel-marking / fletteliste) are pre-marked "ingen aktindsigt" with a justification, so the caseworker doesn't have to.

## Input (one case)

| Field | Meaning |
|-------|---------|
| `kontakt_case_id` | KontAKT case id |
| `kontakt_reference_id` | KontAKT reference — the GO case within the aktindsigt |
| `source_case_id` | GO case number |
| `source_case_title` | Optional title hint |

## Output

A POST to KontAKT's `documents/import` containing the document list (act/document numbers, titles, dates, categories, bilag links) and any warnings. The reference is set to `fetching` while the job runs and `error` if it fails.

## Required configuration

- Constant `GOApiURL` — GO API base URL
- Credential `GOAktApiUser` — GO API user (NTLM)
- Credential `KontAKTAPI` — username = base URL, password = API key

## Dependencies

The shared [`oomtm`](https://github.com/mtm-aarhus/oomtm) library (`go`).
