**31.** Version Control: Repositories, Branches, Commits, Files. A repository can have multiple branches. A branch can have multiple commits. A commit can modify multiple files.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### repositories
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| owner | VARCHAR(100) | NOT NULL |
| description | TEXT | |
| is_private | BOOLEAN | NOT NULL DEFAULT FALSE |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| UNIQUE(owner, name) | | |

### branches
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| repo_id | BIGINT | FK → repositories(id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| head_commit_id | BIGINT | FK → commits(id) |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| UNIQUE(repo_id, name) | | |

### commits
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| repo_id | BIGINT | FK → repositories(id), NOT NULL |
| sha | CHAR(40) | NOT NULL, UNIQUE |
| author | VARCHAR(100) | NOT NULL |
| message | TEXT | NOT NULL |
| parent_sha | CHAR(40) | (NULL for initial commit) |
| committed_at | TIMESTAMP | NOT NULL |

### branch_commits (M:N — branches ↔ commits)
| Column | Type | Constraints |
|--------|------|-------------|
| branch_id | BIGINT | FK → branches(id), NOT NULL |
| commit_id | BIGINT | FK → commits(id), NOT NULL |
| PK(branch_id, commit_id) | | |

### files
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| repo_id | BIGINT | FK → repositories(id), NOT NULL |
| path | VARCHAR(500) | NOT NULL |
| UNIQUE(repo_id, path) | | |

### commit_files (M:N — commits ↔ files)
| Column | Type | Constraints |
|--------|------|-------------|
| commit_id | BIGINT | FK → commits(id), NOT NULL |
| file_id | BIGINT | FK → files(id), NOT NULL |
| change_type | VARCHAR(10) | NOT NULL (ADD, MODIFY, DELETE) |
| lines_added | INT | DEFAULT 0 |
| lines_removed | INT | DEFAULT 0 |
| PK(commit_id, file_id) | | |

## Relationships
- repositories → branches: **1:N**
- branches ↔ commits: **M:N** via `branch_commits` (a commit can be on multiple branches after merge)
- commits ↔ files: **M:N** via `commit_files`

## ER Diagram (Text)
```
[repositories] 1───N [branches] 1───M [branch_commits] M───1 [commits] 1───M [commit_files] M───1 [files]
      │                                                          │
      └──────────────────────────────1:N─────────────────────────┘
```

## Example & Insight
The key insight is that branches and commits are M:N, not 1:N — when you merge branch B into main, all of B's commits now belong to both branches. `parent_sha` enables walking the commit DAG. Index `(repo_id, committed_at DESC)` for recent commit queries.

## Trade-off
Storing `branch_commits` as a junction table is explicit but grows fast (every merge duplicates entries). Git itself uses pointer-based DAG traversal instead. For a DB-backed system, consider materializing this only for active branches and computing history on-demand for old ones.
