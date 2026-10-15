> **Rendered documentation:** [docs.utpro.dev](https://docs.utpro.dev/uTPro.Feature.SearchPlus/) is the canonical, always-current home of this page.

# API Reference

All endpoints are authenticated and require **Settings section access** in the Umbraco backoffice.

Base path: `/umbraco/management/api/v1/utpro/search-plus/synonyms`

---

## List all groups

```
GET /umbraco/management/api/v1/utpro/search-plus/synonyms
```

**Response:** `200 OK`

```json
[
  {
    "id": "a1b2c3d4-0001-4000-8000-000000000001",
    "terms": ["mÃ¡y tÃ­nh", "laptop", "pc", "computer", "notebook"]
  },
  {
    "id": "a1b2c3d4-0001-4000-8000-000000000002",
    "terms": ["Ä‘iá»‡n thoáº¡i", "smartphone", "mobile", "phone", "di Ä‘á»™ng"]
  }
]
```

---

## Create a group

```
POST /umbraco/management/api/v1/utpro/search-plus/synonyms
Content-Type: application/json

{
  "terms": ["mÃ¡y tÃ­nh", "laptop", "pc", "computer"]
}
```

**Response:** `201 Created`

```json
{
  "id": "generated-guid",
  "terms": ["mÃ¡y tÃ­nh", "laptop", "pc", "computer"]
}
```

**Validation:** Minimum 2 terms required.

---

## Update a group

```
PUT /umbraco/management/api/v1/utpro/search-plus/synonyms/{id}
Content-Type: application/json

{
  "terms": ["mÃ¡y tÃ­nh", "laptop", "pc", "computer", "notebook"]
}
```

**Response:** `200 OK` or `404 Not Found`

---

## Delete a group

```
DELETE /umbraco/management/api/v1/utpro/search-plus/synonyms/{id}
```

**Response:** `200 OK` or `404 Not Found`

---

## Test expansion

```
GET /umbraco/management/api/v1/utpro/search-plus/synonyms/expand?term=laptop
```

**Response:** `200 OK`

```json
{
  "term": "laptop",
  "expanded": ["mÃ¡y tÃ­nh", "laptop", "pc", "computer", "notebook"]
}
```

If no synonyms are found, returns the original term:

```json
{
  "term": "unknown",
  "expanded": ["unknown"]
}
```

The expansion is **diacritics-insensitive**: `?term=cong ty` will match the "cÃ´ng ty" group.

---

## Suggest groups (partial match)

```
GET /umbraco/management/api/v1/utpro/search-plus/synonyms/suggest?term=may
```

**Response:** `200 OK`

```json
[
  {
    "id": "a1b2c3d4-0001-4000-8000-000000000001",
    "terms": ["mÃ¡y tÃ­nh", "laptop", "pc", "computer", "notebook"]
  },
  {
    "id": "a1b2c3d4-0001-4000-8000-000000000003",
    "terms": ["mÃ¡y tÃ­nh báº£ng", "tablet", "ipad"]
  }
]
```

Returns groups where any term partially contains the query (diacritics-insensitive). Maximum 10 results.

---

## Export synonyms (CSV)

```
GET /umbraco/management/api/v1/utpro/search-plus/synonyms/export
```

**Response:** `200 OK` â€” `text/csv` file (`search-plus-synonyms.csv`, UTF-8 with BOM so Excel renders diacritics correctly).

Each row is one synonym group; terms are joined with `|` so commas inside terms never collide with the CSV delimiter:

```csv
terms
"mÃ¡y tÃ­nh|laptop|pc|computer"
"cÃ´ng ty|company|firm"
```

---

## Import synonyms (CSV)

```
POST /umbraco/management/api/v1/utpro/search-plus/synonyms/import
Content-Type: multipart/form-data
```

| Field | Type | Description |
|---|---|---|
| `file` | file | CSV file, one synonym group per row â€” terms separated by `\|` (a quoted comma-separated cell is also accepted). Max 2 MB. |
| `mode` | string | `merge` (default) keeps existing groups and skips duplicates; `replace` deletes **all** existing groups before importing. |

**Response:** `200 OK`

```json
{
  "imported": 12,
  "skippedDuplicates": 3,
  "invalidRows": 1
}
```

Duplicate detection is diacritics-insensitive: a file row "cong ty|company" is skipped when the database already has "cÃ´ng ty|company". Rows with fewer than 2 distinct terms count as invalid.

---

## Authentication

All endpoints require a valid Umbraco backoffice authentication token. The API is scoped to the **Settings section** â€” users must have Settings access to call these endpoints.

Use the standard Umbraco Management API authentication flow (Bearer token from `/umbraco/management/api/v1/security/back-office/authorize`).

---

## Swagger

When running in development, the API is documented under the **utpro-search-plus** definition in the Swagger UI at `/umbraco/swagger`.
