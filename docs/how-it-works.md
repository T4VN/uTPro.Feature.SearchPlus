> **Rendered documentation:** [docs.utpro.dev](https://docs.utpro.dev/uTPro.Feature.SearchPlus/) is the canonical, always-current home of this page.

# How It Works

This page explains the two core mechanisms that Search Plus adds to Umbraco's search pipeline.

---

## 1. Diacritics-Insensitive Indexing

When content is published, Umbraco's Examine sends text to a Lucene index. Search Plus replaces
the default analyzer on the ExternalIndex with one that includes an **ASCII folding** step.

### Index time (when content is published/rebuilt)

```
Content: "Tham kháº£o cho Láº­p trÃ¬nh viÃªn"
         â”‚
         â–¼
    Tokenizer  â†’  ["Tham", "kháº£o", "cho", "Láº­p", "trÃ¬nh", "viÃªn"]
         â”‚
         â–¼
    Lowercase   â†’  ["tham", "kháº£o", "cho", "láº­p", "trÃ¬nh", "viÃªn"]
         â”‚
         â–¼
    ASCII Fold  â†’  ["tham", "khao", "cho", "lap", "trinh", "vien"]
         â”‚
         â–¼
    Stored in Lucene index (on disk)
```

### Query time (when user searches)

The **same analyzer** processes the search input:

```
User types: "tham khao"
         â”‚
         â–¼
    Tokenizer  â†’  ["tham", "khao"]
         â”‚
         â–¼
    Lowercase   â†’  ["tham", "khao"]
         â”‚
         â–¼
    ASCII Fold  â†’  ["tham", "khao"]  (already ASCII, no change)
         â”‚
         â–¼
    Match against index â†’ âœ“ finds "Tham kháº£o cho Láº­p trÃ¬nh viÃªn"
```

Because both sides (index and query) fold to ASCII, the matching is diacritics-insensitive.

### Supported languages

Any language that uses Latin script with accents/diacritics:

| Language | Example | Folded |
|---|---|---|
| Vietnamese | cÃ´ng ty, kháº£o | cong ty, khao |
| French | cafÃ©, naÃ¯ve | cafe, naive |
| Spanish | niÃ±o, seÃ±or | nino, senor |
| German | Ã¼ber, straÃŸe | uber, strasse |
| Portuguese | aÃ§Ã£o, coraÃ§Ã£o | acao, coracao |
| Turkish | ÅŸehir, gÃ¼neÅŸ | sehir, gunes |
| Polish | ÅºrÃ³dÅ‚o, Å‚Ã³dÅº | zrodlo, lodz |
| Czech | pÅ™Ã­liÅ¡, Å¾luÅ¥ouÄkÃ½ | prilis, zlutoucky |

---

## 2. Synonym Expansion

Synonyms are stored in the Umbraco database and loaded into an in-memory cache on startup.
They are **not part of the search index** â€” they are applied at query time.

### Query time flow

```
User searches: "laptop"
         â”‚
         â–¼
    â‘  Synonym lookup (from DB cache)
       "laptop" â†’ found in group: ["mÃ¡y tÃ­nh", "laptop", "pc", "computer", "notebook"]
         â”‚
         â–¼
    â‘¡ Build expanded query (OR logic)
       Search("mÃ¡y tÃ­nh") OR Search("laptop") OR Search("pc")
       OR Search("computer") OR Search("notebook")
         â”‚
         â–¼
    â‘¢ Each term goes through the same ASCII-folding analyzer
       "mÃ¡y tÃ­nh" â†’ tokens: ["may", "tinh"]
       "laptop"   â†’ tokens: ["laptop"]
       "pc"       â†’ tokens: ["pc"]
       ...
         â”‚
         â–¼
    â‘£ Lucene matches all tokens against the index
         â”‚
         â–¼
    â‘¤ Results merged, scored by relevance, deduplicated
```

### Diacritics-insensitive synonym lookup

The synonym lookup itself is also diacritics-insensitive:

```
User types: "cong ty" (no accents)
         â”‚
         â–¼
    Lookup "cong ty" â†’ not found (exact)
         â”‚
         â–¼
    Fold and retry â†’ "cong ty" matches cached key for "cÃ´ng ty"
         â”‚
         â–¼
    Returns: ["cÃ´ng ty", "company", "doanh nghiá»‡p", "enterprise", "business"]
```

---

## Key Implications

| Aspect | Behavior |
|---|---|
| **Add/edit/delete synonyms** | Takes effect immediately â€” no index rebuild needed |
| **First install (diacritics)** | Requires one index rebuild so existing content is re-processed with the new analyzer |
| **Uninstall** | Index reverts to default analyzer on next rebuild; synonym expansion stops |
| **Multi-instance** | Synonyms are in the shared database â€” all nodes see the same data |
| **Performance** | Synonym lookup is O(1) dictionary lookup from memory; no DB query per search |
