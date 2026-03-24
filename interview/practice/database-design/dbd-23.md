**23.** Design a database for a film production system. An actor can act in multiple movies. A director can direct multiple movies. A movie can have multiple genres. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### actor
| Column | Type | Constraints |
|--------|------|-------------|
| actor_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| dob | DATE | NULL |
| nationality | VARCHAR(100) | NULL |

### director
| Column | Type | Constraints |
|--------|------|-------------|
| director_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| dob | DATE | NULL |
| nationality | VARCHAR(100) | NULL |

### genre
| Column | Type | Constraints |
|--------|------|-------------|
| genre_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(50) | UNIQUE, NOT NULL |

### movie
| Column | Type | Constraints |
|--------|------|-------------|
| movie_id | BIGINT | PK, AUTO_INCREMENT |
| title | VARCHAR(300) | NOT NULL |
| director_id | BIGINT | FK → director(director_id), NOT NULL |
| release_year | SMALLINT | NULL |
| duration_min | INT | NULL |
| budget | DECIMAL(15,2) | NULL |
| rating | DECIMAL(3,1) | NULL, CHECK (rating BETWEEN 0 AND 10) |

### movie_actor (junction — Movie ↔ Actor)
| Column | Type | Constraints |
|--------|------|-------------|
| movie_id | BIGINT | PK, FK → movie(movie_id) |
| actor_id | BIGINT | PK, FK → actor(actor_id) |
| role_name | VARCHAR(200) | NOT NULL |
| billing_order | INT | NULL |

### movie_genre (junction — Movie ↔ Genre)
| Column | Type | Constraints |
|--------|------|-------------|
| movie_id | BIGINT | PK, FK → movie(movie_id) |
| genre_id | BIGINT | PK, FK → genre(genre_id) |

## Relationships
- **Director → Movie**: 1:N (each movie has one director)
- **Movie ↔ Actor**: M:N via `movie_actor`
- **Movie ↔ Genre**: M:N via `movie_genre`

## ER Diagram
```
+----------+       +----------+       +-------------+       +--------+
| director |       |  movie   |       | movie_actor |       | actor  |
+----------+       +----------+       +-------------+       +--------+
|director_id|─1:N──| movie_id |──1:N──|movie_id(PK) |──N:1──|actor_id|
| name     |       |director_id|      |actor_id(PK) |       | name   |
| dob      |       | title    |       | role_name   |       | dob    |
|nationality|      |release_yr|       |billing_order|       |nation  |
+----------+       |duration  |       +-------------+       +--------+
                   | budget   |
                   | rating   |       +-------------+       +--------+
                   +----+-----+       | movie_genre |       | genre  |
                        |──────1:N────|movie_id(PK) |──N:1──|genre_id|
                                      |genre_id(PK) |       | name   |
                                      +-------------+       +--------+
```

## Key Design Insights
- **Two M:N junctions off the same table** (`movie`): one for actors (with attributes like `role_name`), one for genres (pure association, no extra attributes). This contrast shows when junction tables need extra columns vs. when they're purely structural.
- **`billing_order`** in `movie_actor` determines the credit sequence — who appears first in the cast list. This is a real-world requirement that naturally belongs in the junction table. `role_name` captures the character played.
- **Trade-off**: `director` and `actor` are separate tables, but many directors also act (Clint Eastwood, Ben Affleck). A production system would use a single `person` table with role-based junctions (`movie_cast`, `movie_crew`). For the interview, separate tables directly answer the question — then mention: "In IMDB's actual design, they use a unified person table because the same individual can be both director and actor."
