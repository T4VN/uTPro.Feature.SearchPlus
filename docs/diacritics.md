> **Rendered documentation:** [docs.utpro.dev](https://docs.utpro.dev/uTPro.Feature.SearchPlus/) is the canonical, always-current home of this page.

# Diacritics Support

## What is diacritics-insensitive search?

Diacritics are accent marks and other modifications on letters: Ã¡, áº£, Ã£, áº¡, Ã¢, Äƒ, Ã©, Ãª, Ã´, Æ¡, Æ°, Ã±, Ã¼, Ã§, etc.

**Diacritics-insensitive search** means users can type without accents and still find content that contains accented text.

| User types | Finds content containing |
|---|---|
| tham khao | tham kháº£o |
| cong ty | cÃ´ng ty |
| cafe | cafÃ© |
| nino | niÃ±o |
| uber | Ã¼ber |

## How it works

Search Plus replaces the default Examine ExternalIndex analyzer with one that includes an **ASCII folding** step. This step converts accented characters to their ASCII base equivalents.

The folding happens at **both index time and query time**, so the matching is always consistent.

### Index time

When content is published, text is processed:

```
"CÃ´ng ty TNHH ABC" â†’ tokens: ["cong", "ty", "tnhh", "abc"]
```

### Query time

When a user searches, the same processing applies:

```
User types: "cong ty" â†’ tokens: ["cong", "ty"] â†’ matches!
User types: "cÃ´ng ty" â†’ tokens: ["cong", "ty"] â†’ also matches!
```

## Supported languages

Any language using Latin script with diacritical marks:

| Language | Characters folded |
|---|---|
| Vietnamese | Ã , Ã¡, áº£, Ã£, áº¡, Ã¢, Äƒ, Ã¨, Ã©, Ãª, Ã¬, Ã², Ã´, Æ¡, Ã¹, Æ°, á»³, Ä‘ â†’ a, e, i, o, u, d |
| French | Ã©, Ã¨, Ãª, Ã«, Ã§, Ã , Ã¹, Ã®, Ã´, Ã» â†’ e, c, a, u, i, o, u |
| Spanish | Ã±, Ã¡, Ã©, Ã­, Ã³, Ãº, Ã¼ â†’ n, a, e, i, o, u, u |
| Portuguese | Ã£, Ãµ, Ã¡, Ã©, Ãª, Ã³, Ã´, Ãº, Ã§ â†’ a, o, a, e, e, o, o, u, c |
| German | Ã¤, Ã¶, Ã¼, ÃŸ â†’ a, o, u, ss |
| Turkish | ÅŸ, ÄŸ, Ä±, Ã¶, Ã¼, Ã§ â†’ s, g, i, o, u, c |
| Polish | Ä…, Ä‡, Ä™, Å‚, Å„, Ã³, Å›, Åº, Å¼ â†’ a, c, e, l, n, o, s, z, z |
| Czech/Slovak | Ã¡, Ä, Ä, Ã©, Ä›, Ã­, Åˆ, Ã³, Å™, Å¡, Å¥, Ãº, Å¯, Ã½, Å¾ â†’ a, c, d, e, e, i, n, o, r, s, t, u, u, y, z |
| Romanian | Äƒ, Ã¢, Ã®, È™, È› â†’ a, a, i, s, t |
| Hungarian | Ã¡, Ã©, Ã­, Ã³, Ã¶, Å‘, Ãº, Ã¼, Å± â†’ a, e, i, o, o, o, u, u, u |
| Nordic | Ã¥, Ã¤, Ã¶, Ã¸, Ã¦ â†’ a, a, o, o, a |

## First-time setup

After installing Search Plus, you need to **rebuild the External Index once** so existing content is re-processed with the new analyzer:

1. Go to **Settings â†’ Examine Management**
2. Find the **External** index
3. Click **Rebuild**

Future content publishes are automatically processed with the new analyzer â€” no manual action needed.

## Important notes

- The folding is **one-way** â€” content is stored in folded form in the index, but the original text in the database is unchanged
- Search results still display the original text (with diacritics) because results are resolved from the content cache, not from the index
- If Search Plus is uninstalled, rebuild the index to revert to the default analyzer
