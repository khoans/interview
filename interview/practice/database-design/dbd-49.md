**49.** CMS: Pages, Versions, Tags, Users, Edits. A page can have multiple versions. A page can be associated with multiple tags. A user can edit multiple pages.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| role | VARCHAR(20) | NOT NULL DEFAULT 'EDITOR' |

### pages
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| slug | VARCHAR(200) | NOT NULL, UNIQUE |
| title | VARCHAR(300) | NOT NULL |
| current_version_id | BIGINT | FK → page_versions(id) |
| status | VARCHAR(20) | NOT NULL DEFAULT 'DRAFT' |
| created_by | BIGINT | FK → users(id), NOT NULL |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| published_at | TIMESTAMP | |

### page_versions
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| page_id | BIGINT | FK → pages(id), NOT NULL |
| version_number | INT | NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| content | TEXT | NOT NULL |
| edited_by | BIGINT | FK → users(id), NOT NULL |
| change_summary | VARCHAR(300) | |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| UNIQUE(page_id, version_number) | | |

### tags
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(50) | NOT NULL, UNIQUE |
| slug | VARCHAR(50) | NOT NULL, UNIQUE |

### page_tags (M:N — pages ↔ tags)
| Column | Type | Constraints |
|--------|------|-------------|
| page_id | BIGINT | FK → pages(id), NOT NULL |
| tag_id | BIGINT | FK → tags(id), NOT NULL |
| PK(page_id, tag_id) | | |

## Relationships
- users → pages: **1:N** (creator)
- pages → page_versions: **1:N**
- pages → page_versions: **1:1** via `current_version_id` (pointer to published version)
- users → page_versions: **1:N** (editor)
- pages ↔ tags: **M:N** via `page_tags`

## ER Diagram (Text)
```
[users] 1───N [pages] 1───N [page_versions]
                │  ↑              │
                │  └──1:1─────────┘ (current_version_id)
               M:N
                │
          [page_tags] M───1 [tags]
```

## Example & Insight
Wikipedia-style versioning. The `current_version_id` pointer on pages lets you serve the published content with a single join. To publish: `UPDATE pages SET current_version_id = :newVersionId, status = 'PUBLISHED'`. To rollback: point `current_version_id` to any previous version. Version diff: compare `content` between two version numbers using application-level diff (like Java's `diff-match-patch` library).

## Trade-off
Storing full content in every version is simple but storage-heavy for large pages with frequent small edits. Alternative: store diffs (deltas) instead of full content — saves space but makes rendering any version require replaying all diffs from version 1. For most CMS systems, full content per version is fine — storage is cheap, and it makes reads O(1) instead of O(n).
