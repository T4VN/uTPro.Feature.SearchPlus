# uTPro Search Plus for Umbraco

Enhance site search with **synonym expansion** and **diacritics-insensitive matching** — managed directly from the Umbraco backoffice. Users searching "laptop" will also find content containing "máy tính", "pc", or "computer". Users searching without accents ("cong ty") will find "công ty".

Works with **Umbraco 17**. Database support: **SQL Server**, **SQLite** and **PostgreSQL**.

[![NuGet](https://img.shields.io/nuget/v/uTPro.Feature.SearchPlus.svg)](https://www.nuget.org/packages/uTPro.Feature.SearchPlus)
[![NuGet Downloads](https://img.shields.io/nuget/dt/uTPro.Feature.SearchPlus.svg)](https://www.nuget.org/packages/uTPro.Feature.SearchPlus)
[![Umbraco Marketplace](https://img.shields.io/badge/Umbraco-Marketplace-blue)](https://marketplace.umbraco.com/package/utpro.feature.searchplus)
[![Umbraco 17](https://img.shields.io/badge/Umbraco-17-3544B1)](https://umbraco.com)
[![License: Free (proprietary)](https://img.shields.io/badge/License-Free%20(proprietary)-green.svg)](LICENSE.txt)

---

## Features

- **Synonym groups** — define sets of equivalent terms
- **Diacritics-insensitive indexing** — ASCII-folding analyzer for all Latin-script languages
- **Backoffice management UI** — create, edit, delete and test synonym groups
- **Pre-loaded defaults** — 25 common Vietnamese–English synonym groups
- **Automatic integration** — works with Umbraco's Examine search at query time
- **REST API** — full CRUD + expansion test endpoints
- **Database storage** — supports multi-instance / load-balanced deployments

---

## Quick Start

```bash
dotnet add package uTPro.Feature.SearchPlus
```

After installing, go to **Settings → Examine Management** and **Rebuild the External Index**.

| Umbraco | .NET | Target |
|---|---|---|
| 17 | .NET 10 | `net10.0` |

---

## Configuration

No configuration is required. The package works out of the box.

---

## 📖 Full Documentation

**[docs.utpro.dev/uTPro.Feature.SearchPlus](https://docs.utpro.dev/uTPro.Feature.SearchPlus/)**

---

## License

Free to use (including commercially) under a proprietary [End User License Agreement](LICENSE.txt).

---

> 📦 [NuGet](https://www.nuget.org/packages/uTPro.Feature.SearchPlus) · [GitHub](https://github.com/T4VN/uTPro.Feature.SearchPlus) · [Umbraco Marketplace](https://marketplace.umbraco.com/package/utpro.feature.searchplus)
