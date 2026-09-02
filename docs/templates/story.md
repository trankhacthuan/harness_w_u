# US-XXX Story Title

## Status

planned

## Lane

tiny | normal | high-risk

## Product Contract

Describe the behavior this story must make true.

## Relevant Product Docs

- `docs/product/...`

## Acceptance Criteria

- Criterion 1.
- Criterion 2.
- Criterion 3.

## Design Notes

- Routes / rendering mode (static / ISR / SSR / client):
- Data sources and boundary inputs (parse-first):
- Server vs Client Components:
- Server actions / route handlers:
- Domain types and rules:
- SEO metadata needs:

## Web Page Checklist

Fill this when the story adds or changes a route. Delete it for non-UI stories.

- [ ] Loading state (`loading.tsx` / skeleton)
- [ ] Empty state
- [ ] Error state (`error.tsx` / inline)
- [ ] Keyboard navigable, visible focus, correct landmarks/headings
- [ ] Labels / alt text / ARIA where needed (`jsx-a11y` clean, axe clean)
- [ ] Responsive 320px → wide desktop, no horizontal scroll
- [ ] Uses the UI/UX repo's token / class convention, no bespoke styling
- [ ] `metadata` exported if the route is indexable
- [ ] Forms: client + server validation, error summary, success path

## Validation

When updating durable proof status, use numeric booleans:
`scripts/bin/harness-cli story update --id <id> --unit 1 --integration 1 --e2e 0 --platform 0`.

| Layer | Expected proof |
| --- | --- |
| Unit | |
| Integration | |
| E2E | |
| Platform | |
| Release | |

## Harness Delta

Document any harness updates made or proposed because of this story.

## Evidence

Add commands, reports, screenshots, or links after validation exists.
