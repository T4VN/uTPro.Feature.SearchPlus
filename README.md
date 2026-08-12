# uTPro Search Plus for Umbraco

Enhance site search with **synonym expansion** and **diacritics-insensitive matching** — managed
directly from the Umbraco backoffice. Users searching "laptop" will also find content containing
"máy tính", "pc", or "computer". Users searching without accents ("cong ty") will find "công ty".

Works with **Umbraco 17**.

Database support: **SQL Server**, **SQLite** and **PostgreSQL**.

[![NuGet](https://img.shields.io/nuget/v/uTPro.Feature.SearchPlus.svg)](https://www.nuget.org/packages/uTPro.Feature.SearchPlus)
[![NuGet Downloads](https://img.shields.io/nuget/dt/uTPro.Feature.SearchPlus.svg)](https://www.nuget.org/packages/uTPro.Feature.SearchPlus)
[![Umbraco Marketplace](https://img.shields.io/badge/Umbraco-Marketplace-blue)](https://marketplace.umbraco.com/package/utpro.feature.searchplus)
[![Umbraco 17](https://img.shields.io/badge/Umbraco-17-3544B1)](https://umbraco.com)
[![License: Free (proprietary)](https://img.shields.io/badge/License-Free%20(proprietary)-green.svg)](LICENSE.txt)

---

## Features

- **Synonym groups** — define sets of equivalent terms (e.g. "máy tính", "laptop", "pc", "computer", "notebook"). Searching for any term in a group expands the query to include all others.
- **Diacritics-insensitive indexing** — the Examine ExternalIndex is automatically configured with an ASCII-folding analyzer so accented characters match their unaccented equivalents across all Latin-script languages (Vietnamese, French, Spanish, Portuguese, German, Turkish, Polish, Czech, Romanian, and more).
- **Backoffice management UI** — create, edit, delete and test synonym groups from **Settings → uTPro Feature → Search Plus**. Includes real-time search/filter with diacritics-insensitive matching and pagination.
- **Pre-loaded defaults** — 25 common Vietnamese–English synonym groups are seeded on first install.
- **Automatic integration** — when used alongside `uTPro.Extension.Search`, synonym expansion is applied automatically at query time with no code changes.
- **REST API** — full CRUD + expansion test endpoints for automation and CI/CD pipelines.
- **Database storage** — synonym data is stored in the Umbraco database (not files), supporting multi-instance / load-balanced deployments out of the box.
- **Secure by default** — every endpoint requires access to the Settings section.

---

## Quick Start

```bash
dotnet add package uTPro.Feature.SearchPlus
```

Start Umbraco and open **Settings → uTPro Feature → Search Plus**. Synonym groups appear
automatically — 25 common groups are pre-loaded on first install.

After installing, go to **Settings → Examine Management** and **Rebuild the External Index** so the
new diacritics-insensitive analyzer takes effect on existing content.

| Umbraco | .NET | Target |
|---|---|---|
| 17 | .NET 10 | `net10.0` |

---

## Configuration

No configuration is required. The package works out of the box.

---

## How it works

```
User searches "laptop"
  → SearchPlus expands: ["máy tính", "laptop", "pc", "computer", "notebook"]
  → Examine queries all terms (OR)
  → Results include content mentioning any of those terms

User searches "cong ty" (no accents)
  → Diacritics folding matches "công ty" in the index
  → Synonym expansion returns: ["công ty", "company", "doanh nghiệp", "enterprise", "business"]
```

---

## Backoffice UI

The **Search Plus** workspace provides:

- **Unified search bar** — type any term to instantly test expansion or find existing groups
- **Suggestions** — when no exact synonym match is found, related groups are suggested (diacritics-insensitive partial matching)
- **Quick add** — one-click to create a new group pre-filled with the searched term
- **Inline edit/delete** — manage groups without leaving the page
- **Pagination** — handles large synonym lists efficiently
- **Highlight** — matching terms are visually highlighted in search results

---

## Custom integration

### With uTPro.Extension.Search (automatic)

If your site uses `uTPro.Extension.Search` (the built-in uTPro search service), synonym expansion is **automatic** — no code changes needed. The search service detects SearchPlus at runtime and expands queries before executing them.

### With Umbraco's default Examine search (manual)

If you use Examine directly (without uTPro.Extension.Search), inject `ISynonymProvider` and expand the query before passing it to Examine:

```csharp
using Examine;
using Examine.Search;
using Umbraco.Cms.Core;
using uTPro.Feature.SearchPlus.Services;

public class SearchController : Controller
{
    private readonly IExamineManager _examineManager;
    private readonly ISynonymProvider _synonyms;

    public SearchController(IExamineManager examineManager, ISynonymProvider synonyms)
    {
        _examineManager = examineManager;
        _synonyms = synonyms;
    }

    public IActionResult Search(string q)
    {
        if (!_examineManager.TryGetIndex(Constants.UmbracoIndexes.ExternalIndexName, out var index))
            return NotFound();

        var searcher = index.Searcher;

        // 1. Expand synonyms: "laptop" → ["máy tính", "laptop", "pc", "computer", "notebook"]
        var terms = _synonyms.Expand(q);

        // 2. Build OR query across all expanded terms
        var query = searcher.CreateQuery("content");
        var boolOp = query.ManagedQuery(terms[0]);

        for (var i = 1; i < terms.Count; i++)
        {
            boolOp = boolOp.Or().ManagedQuery(terms[i]);
        }

        // 3. Execute and return results
        var results = boolOp.Execute();
        return Ok(results.Select(r => new { r.Id, r.Score }));
    }
}
```

### Key points

- `ISynonymProvider.Expand(term)` handles diacritics-insensitive lookup internally — passing "cong ty" will match the "công ty" group and return all synonyms.
- The diacritics-insensitive analyzer is applied to the ExternalIndex automatically on startup (via `DiacriticsInsensitiveIndexOptions`). No manual Examine configuration is needed.
- After installing, **rebuild the External Index** from Examine Management so the new analyzer processes existing content.
- If SearchPlus is uninstalled, the Examine index reverts to Umbraco's default analyzer on next rebuild.

---

## REST API

All endpoints require backoffice authentication (Settings section access).

Base path: `/umbraco/management/api/v1/utpro/search-plus/synonyms`

| Method | Path | Description |
|---|---|---|
| GET | `/` | List all synonym groups |
| POST | `/` | Create a new group |
| PUT | `/{id}` | Update a group |
| DELETE | `/{id}` | Delete a group |
| GET | `/expand?term=...` | Test synonym expansion |
| GET | `/suggest?term=...` | Get suggested groups (partial match) |

---

## Database

On first startup, a state-keyed migration creates two tables:

| Table | Purpose |
|---|---|
| `uTProSynonymGroup` | Synonym group header (ID, GroupKey, timestamps) |
| `uTProSynonymTerm` | Individual terms within a group (GroupId, Term, SortOrder) |

Data access uses NPoco strongly-typed queries with provider-quoted identifiers, so it runs on
**SQL Server, SQLite and PostgreSQL** — the same cross-database approach used by
`uTPro.Feature.SimpleFormBuilder` and `uTPro.Feature.JobMonitor`.

---

## Documentation

| Guide | What's inside |
|---|---|
| [Getting Started](https://github.com/T4VN/uTPro.Feature.SearchPlus/blob/main/docs/getting-started.md) | Install, backoffice location, first-time setup |
| [How It Works](https://github.com/T4VN/uTPro.Feature.SearchPlus/blob/main/docs/how-it-works.md) | Diacritics folding, synonym expansion flow, supported languages |
| [Synonym Management](https://github.com/T4VN/uTPro.Feature.SearchPlus/blob/main/docs/synonym-management.md) | How to create, edit, delete and test groups |
| [Diacritics Support](https://github.com/T4VN/uTPro.Feature.SearchPlus/blob/main/docs/diacritics.md) | How the ASCII-folding analyzer works, supported languages |
| [Integration](https://github.com/T4VN/uTPro.Feature.SearchPlus/blob/main/docs/integration.md) | Using ISynonymProvider in custom code, automatic integration with uTPro.Extension.Search |
| [API Reference](https://github.com/T4VN/uTPro.Feature.SearchPlus/blob/main/docs/api-reference.md) | Full REST API documentation |

---

## License & Author

By [T4VN](https://github.com/T4VN). Free to use — including in commercial projects — under a proprietary [End User License Agreement](LICENSE.txt). The package ships as a compiled NuGet package; the source is not published, and modifying, reverse engineering, or redistributing the package is not permitted. See [LICENSE.txt](LICENSE.txt) for full terms. Issues welcome on the [GitHub repository](https://github.com/T4VN/uTPro.Feature.SearchPlus).
