# Content Rules

Site copy and data must be true. These rules apply to every page, placeholder,
and piece of structured data produced in this repository.

## 1. Never invent facts

Do not make up names, prices, reviews, awards, statistics, addresses, contact
details, opening hours, or founding stories. If a fact is not in the brief, it
does not appear as a fact.

## 2. Missing data becomes a visible placeholder

- Use the form `[CẦN BỔ SUNG: <what is needed>]` in copy and in the UI, so the
  gap is obvious to the owner and to reviewers.
- Do not silently hide a gap inside lorem ipsum or plausible filler.

## 3. Sections without real content

Reviews, news, awards, and similar sections are **hidden** when no real content
exists, or shown as a clearly marked placeholder. Never create fake content to
fill them.

## 4. Image placeholders

Reserve a frame at the final aspect ratio on a neutral background and describe
the photo to take, for example:
`[CẦN BỔ SUNG ẢNH: cận cảnh tô <món>, khói bốc, ánh sáng bên trái]`.
The layout must look intentional with placeholders in place.

## 5. Prices and structured data

- Show a price only when the brief provides it.
- JSON-LD and other structured data include only fields that have real values.

## 6. Voice

Concrete and sensory, built from real details supplied in the brief (ingredients,
method, origin, people, space).

- Good: "Nước dùng ninh 12 tiếng từ xương ống." (only if that fact was given)
- Bad: "Hương vị tinh hoa hội tụ", "trải nghiệm ẩm thực tuyệt vời".

If a detail is missing, leave a placeholder rather than writing something
pleasant but unsupported.

## 7. The missing-content ledger

Every placeholder is also listed in `docs/product/MISSING.md` with where it is
used and what is needed. At the end of any task that touches content, report the
full list of missing content and assets to the owner.
