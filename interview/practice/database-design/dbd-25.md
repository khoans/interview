**25.** Design a database for a tournament system. A team can have multiple players. A team can participate in multiple matches. A match can have multiple referees. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### team
| Column | Type | Constraints |
|--------|------|-------------|
| team_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL, UNIQUE |
| coach | VARCHAR(200) | NULL |
| founded_year | SMALLINT | NULL |

### player
| Column | Type | Constraints |
|--------|------|-------------|
| player_id | BIGINT | PK, AUTO_INCREMENT |
| team_id | BIGINT | FK → team(team_id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| position | VARCHAR(50) | NULL |
| jersey_number | INT | NULL |
| dob | DATE | NULL |
| UNIQUE | | (team_id, jersey_number) |

### referee
| Column | Type | Constraints |
|--------|------|-------------|
| referee_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| certification_level | VARCHAR(50) | NOT NULL |

### match
| Column | Type | Constraints |
|--------|------|-------------|
| match_id | BIGINT | PK, AUTO_INCREMENT |
| home_team_id | BIGINT | FK → team(team_id), NOT NULL |
| away_team_id | BIGINT | FK → team(team_id), NOT NULL |
| match_date | TIMESTAMP | NOT NULL |
| venue | VARCHAR(200) | NOT NULL |
| home_score | INT | NULL |
| away_score | INT | NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'SCHEDULED' |
| CHECK | | (home_team_id != away_team_id) |

### match_referee (junction — Match ↔ Referee)
| Column | Type | Constraints |
|--------|------|-------------|
| match_id | BIGINT | PK, FK → match(match_id) |
| referee_id | BIGINT | PK, FK → referee(referee_id) |
| role | VARCHAR(50) | NOT NULL, DEFAULT 'MAIN' |

## Relationships
- **Team → Player**: 1:N
- **Team → Match**: 1:N (twice — as home and away team)
- **Match ↔ Referee**: M:N via `match_referee`

## ER Diagram
```
+--------+       +---------+
| player |       |  team   |
+--------+       +---------+
|player_id|─N:1──| team_id |──(home_team)──┐
| team_id|       | name    |               |
| name   |       | coach   |        +------+------+
|position|       |founded_yr|       |    match    |
|jersey  |       +---------+       +-------------+
| dob    |            |            | match_id    |
+--------+       (away_team)       |home_team_id |
                      └────────────|away_team_id |
                                   | match_date  |
                                   | venue       |
                                   | home_score  |
                                   | away_score  |
                                   | status      |
                                   +------+------+
                                          |1:N
                                   +------+--------+      +----------+
                                   | match_referee |      |  referee |
                                   +---------------+      +----------+
                                   |match_id  (PK) |─N:1──|referee_id|
                                   |referee_id(PK) |      | name     |
                                   | role          |      |cert_level|
                                   +---------------+      +----------+
```

## Key Design Insights
- **Two FKs from `match` to `team`** (home and away) — the same dual-FK pattern as airport→flight. CHECK constraint `home_team_id != away_team_id` prevents a team from playing against itself. Queries need two JOINs: `JOIN team home ON m.home_team_id = home.team_id JOIN team away ON m.away_team_id = away.team_id`.
- **`role` in `match_referee`** distinguishes MAIN referee from ASSISTANT, VAR (Video Assistant Referee), etc. A match typically has 1 main + 2 assistants + 1 VAR — the junction table naturally supports this without hardcoding.
- **Trade-off**: UNIQUE(team_id, jersey_number) prevents two players on the same team from having the same number. Scores are stored directly on `match` (denormalized) rather than computed from a `goal` event table. For a basic tournament system this is sufficient; for detailed stats, you'd add a `match_event` table tracking goals, cards, substitutions with timestamps. Also, "a team can participate in multiple matches" is naturally handled by the two FK columns — no explicit junction needed since each match always has exactly 2 teams.
