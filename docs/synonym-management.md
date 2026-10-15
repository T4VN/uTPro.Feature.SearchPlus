> **Rendered documentation:** [docs.utpro.dev](https://docs.utpro.dev/uTPro.Feature.SearchPlus/) is the canonical, always-current home of this page.

# Synonym Management

## Overview

Synonym groups define sets of equivalent terms. When a visitor searches for any term in a group, the query expands to include all terms in that group.

**Example:** Group ["mÃ¡y tÃ­nh", "laptop", "pc", "computer", "notebook"]
- User searches "laptop" â†’ finds content containing any of: mÃ¡y tÃ­nh, laptop, pc, computer, notebook

## Accessing the UI

1. Open the Umbraco backoffice
2. Go to **Settings** (left sidebar)
3. Under the **uTPro Feature** group, click **Search Plus**

## Creating a group

1. Click the **+ Add Group** button (top right)
2. Enter terms separated by commas â€” minimum 2 terms per group
3. Press Enter or click **Add Group**

**Example input:** `mÃ¡y tÃ­nh, laptop, pc, computer, notebook`

## Editing a group

1. Find the group in the list (use the search bar to filter)
2. Click **Edit** on the group card
3. Modify the comma-separated terms
4. Press Enter or click **Save**

## Deleting a group

1. Find the group in the list
2. Click **Delete**
3. Confirm the deletion

## Searching and filtering

The search bar at the top serves dual purpose:

- **Test expansion** â€” type any term to see if it has synonyms configured and what it expands to
- **Filter groups** â€” the list below filters to show only groups containing matching terms

The search is **diacritics-insensitive**: typing "may" will find groups containing "mÃ¡y".

## Suggestions

When you search a term that has no exact synonym match, the UI shows:

- **"No synonyms configured"** message
- **Similar groups** â€” groups that contain terms partially matching your search
- **Quick add button** â€” pre-fills a new group form with the searched term

## Import and export (CSV)

The toolbar has two buttons for bulk management:

- **Export CSV** â€” downloads every group as `search-plus-synonyms.csv`. Each row is one group with terms joined by `|`, e.g. `mÃ¡y tÃ­nh|laptop|pc|computer`. The file opens cleanly in Excel (UTF-8 with BOM, so diacritics survive).
- **Import CSV** â€” upload a file in the same format and choose:
  - **Import & Merge** â€” adds new groups and skips duplicates (compared diacritics-insensitively, so "cong ty|company" is recognised as "cÃ´ng ty|company").
  - **Import & Replace All** â€” âš ï¸ deletes every existing group first, then imports the file.

A row with fewer than 2 distinct terms is reported as invalid in the result notification. This is the fastest way to seed a large synonym dictionary or move one between environments.

## Pre-loaded groups

On first install, 25 common groups are seeded:

| Category | Examples |
|---|---|
| Technology | mÃ¡y tÃ­nh/laptop/pc, Ä‘iá»‡n thoáº¡i/smartphone/mobile |
| E-commerce | mua/order/purchase, giÃ¡/price/cost |
| Contact | liÃªn há»‡/contact/support, Ä‘á»‹a chá»‰/address/location |
| Content | tin tá»©c/news/article, hÆ°á»›ng dáº«n/guide/tutorial |
| Services | dá»‹ch vá»¥/service/solution, sáº£n pháº©m/product/item |
| Delivery | giao hÃ ng/delivery/shipping, thanh toÃ¡n/payment |
| Organization | cÃ´ng ty/company/business, tuyá»ƒn dá»¥ng/recruitment/career |

These can be edited or deleted as needed.

## Tips

- Keep groups focused â€” terms in a group should be genuinely interchangeable in meaning
- Include both Vietnamese and English equivalents for bilingual sites
- Changes take effect immediately â€” no index rebuild needed
- Use the search bar to verify your groups work as expected before deploying
