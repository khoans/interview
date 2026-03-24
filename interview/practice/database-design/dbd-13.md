**13.** Design a database for a music streaming service. An artist can create multiple albums. An album can have multiple songs. A user can create multiple playlists. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### artist
| Column | Type | Constraints |
|--------|------|-------------|
| artist_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| genre | VARCHAR(100) | NULL |
| bio | TEXT | NULL |

### album
| Column | Type | Constraints |
|--------|------|-------------|
| album_id | BIGINT | PK, AUTO_INCREMENT |
| artist_id | BIGINT | FK → artist(artist_id), NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| release_date | DATE | NULL |
| cover_url | VARCHAR(500) | NULL |

### song
| Column | Type | Constraints |
|--------|------|-------------|
| song_id | BIGINT | PK, AUTO_INCREMENT |
| album_id | BIGINT | FK → album(album_id), NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| duration_sec | INT | NOT NULL |
| track_number | INT | NOT NULL |
| file_url | VARCHAR(500) | NOT NULL |

### app_user
| Column | Type | Constraints |
|--------|------|-------------|
| user_id | BIGINT | PK, AUTO_INCREMENT |
| username | VARCHAR(50) | UNIQUE, NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| subscription_type | VARCHAR(20) | NOT NULL, DEFAULT 'FREE' |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### playlist
| Column | Type | Constraints |
|--------|------|-------------|
| playlist_id | BIGINT | PK, AUTO_INCREMENT |
| user_id | BIGINT | FK → app_user(user_id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| is_public | BOOLEAN | NOT NULL, DEFAULT FALSE |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

### playlist_song (junction — Playlist ↔ Song)
| Column | Type | Constraints |
|--------|------|-------------|
| playlist_id | BIGINT | PK, FK → playlist(playlist_id) |
| song_id | BIGINT | PK, FK → song(song_id) |
| position | INT | NOT NULL |
| added_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

## Relationships
- **Artist → Album**: 1:N
- **Album → Song**: 1:N
- **User → Playlist**: 1:N
- **Playlist ↔ Song**: M:N via `playlist_song`

## ER Diagram
```
+--------+       +--------+       +--------+
| artist |       | album  |       |  song  |
+--------+       +--------+       +--------+
|artist_id|─1:N──|album_id|──1:N──|song_id |
| name   |       |artist_id|      |album_id|
| genre  |       | title  |       | title  |
| bio    |       |rel_date|       |duration|
+--------+       |cover   |       |track_no|
                 +--------+       |file_url|
                                  +---+----+
                                      |
+----------+    +----------+    +-----+--------+
| app_user |    | playlist |    |playlist_song |
+----------+    +----------+    +--------------+
| user_id  |─1:N|playlist_id|─1:N|playlist_id(PK)|
| username |    | user_id  |    | song_id   (PK)|
| email    |    | name     |    | position      |
|sub_type  |    |is_public |    | added_at      |
+----------+    +----------+    +--------------+
```

## Key Design Insights
- **Chain of 1:N**: Artist → Album → Song forms a natural hierarchy. This is the dominant pattern in content management — parent owns children. The M:N only appears when users curate content (playlists).
- **`position` in `playlist_song`** maintains song ordering within a playlist. Without it, you can't preserve the user's intended sequence. This is a frequently overlooked requirement — ordered M:N relationships need a position/sort column.
- **Trade-off**: This design assumes one artist per album. In reality, collaborative albums exist (multiple artists). To handle that, you'd replace `album.artist_id` with a junction table `album_artist`. Start with 1:N for the interview — then mention the M:N evolution as a "production consideration." Also, a `play_history` table tracking `(user_id, song_id, played_at)` would be essential for recommendations but is outside the core schema.
