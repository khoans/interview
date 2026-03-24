**44.** Design a Playlist Manager using the Iterator pattern. Support Sequential, Shuffle, Repeat One, Repeat All traversal modes.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Playlist Manager — Iterator Pattern

**Pattern:** Iterator (Behavioral)
**Why Iterator?** Same playlist, different traversal modes. Client code (media player) just calls `next()` regardless of whether it's shuffle, repeat, or sequential.

**SOLID Principles Applied:**
- **SRP:** Playlist stores songs; each iterator handles one playback mode
- **OCP:** Add new modes (Repeat Shuffle, Smart Shuffle) as new iterators
- **DIP:** MediaPlayer depends on Iterator interface, not playlist internals

### Text UML Diagram

```
┌──────────────┐     ┌────────────────────┐
│  Playlist    │────→│  <<interface>>      │
│──────────────│     │  PlaylistIterator   │
│ -songs       │     │────────────────────│
│ -name        │     │ +hasNext()          │
│ +addSong()   │     │ +next()             │
│ +iterator()  │     │ +current()          │
│ +shuffle()   │     │ +previous()         │
│ +repeatOne() │     │ +reset()            │
│ +repeatAll() │     └────────┬───────────┘
└──────────────┘              │ implements
                    ┌─────────┼──────────┐──────────┐
                Sequential  Shuffle   RepeatOne  RepeatAll
                Iterator    Iterator  Iterator   Iterator
```

### Key Design Decisions

1. **Bidirectional navigation** — `next()` and `previous()` for skip forward/backward
2. **`current()`** — Returns current song without advancing (for display)
3. **Shuffle uses Fisher-Yates** — Proper random permutation, not random index
4. **RepeatAll wraps around** — After last song, `next()` goes to first
5. **RepeatOne loops forever** — `next()` always returns same song

### Java Implementation

```java
import java.util.*;

// ============================================================
// DOMAIN MODEL
// ============================================================

class Song {
    private final String title;
    private final String artist;
    private final int durationSec;

    public Song(String title, String artist, int durationSec) {
        this.title = title;
        this.artist = artist;
        this.durationSec = durationSec;
    }

    public String getTitle() { return title; }
    public String getArtist() { return artist; }
    public int getDurationSec() { return durationSec; }

    @Override
    public String toString() {
        return String.format("%s - %s (%d:%02d)", artist, title,
                durationSec / 60, durationSec % 60);
    }
}

// ============================================================
// ITERATOR INTERFACE
// ============================================================

interface PlaylistIterator {
    boolean hasNext();
    Song next();
    boolean hasPrevious();
    Song previous();
    Song current();
    void reset();
    String getModeName();
}

// ============================================================
// CONCRETE ITERATORS
// ============================================================

class SequentialIterator implements PlaylistIterator {
    private final List<Song> songs;
    private int index;

    public SequentialIterator(List<Song> songs) {
        this.songs = songs;
        this.index = -1;
    }

    @Override public boolean hasNext() { return index < songs.size() - 1; }
    @Override public Song next() {
        if (!hasNext()) throw new NoSuchElementException("End of playlist");
        return songs.get(++index);
    }
    @Override public boolean hasPrevious() { return index > 0; }
    @Override public Song previous() {
        if (!hasPrevious()) throw new NoSuchElementException("Start of playlist");
        return songs.get(--index);
    }
    @Override public Song current() {
        if (index < 0 || index >= songs.size()) return null;
        return songs.get(index);
    }
    @Override public void reset() { index = -1; }
    @Override public String getModeName() { return "Sequential"; }
}

class ShuffleIterator implements PlaylistIterator {
    private final List<Song> shuffled;
    private int index;

    public ShuffleIterator(List<Song> songs) {
        this.shuffled = new ArrayList<>(songs);
        Collections.shuffle(this.shuffled); // Fisher-Yates internally
        this.index = -1;
    }

    @Override public boolean hasNext() { return index < shuffled.size() - 1; }
    @Override public Song next() {
        if (!hasNext()) throw new NoSuchElementException("End of shuffled playlist");
        return shuffled.get(++index);
    }
    @Override public boolean hasPrevious() { return index > 0; }
    @Override public Song previous() {
        if (!hasPrevious()) throw new NoSuchElementException("Start of playlist");
        return shuffled.get(--index);
    }
    @Override public Song current() {
        if (index < 0 || index >= shuffled.size()) return null;
        return shuffled.get(index);
    }
    @Override public void reset() {
        Collections.shuffle(shuffled); // re-shuffle on reset
        index = -1;
    }
    @Override public String getModeName() { return "Shuffle"; }
}

class RepeatOneIterator implements PlaylistIterator {
    private final List<Song> songs;
    private int index;

    public RepeatOneIterator(List<Song> songs, int startIndex) {
        this.songs = songs;
        this.index = startIndex;
    }

    @Override public boolean hasNext() { return true; } // always has next
    @Override public Song next() { return songs.get(index); } // always same song
    @Override public boolean hasPrevious() { return true; }
    @Override public Song previous() { return songs.get(index); }
    @Override public Song current() { return songs.get(index); }
    @Override public void reset() { /* stays on same song */ }
    @Override public String getModeName() { return "Repeat One"; }
}

class RepeatAllIterator implements PlaylistIterator {
    private final List<Song> songs;
    private int index;

    public RepeatAllIterator(List<Song> songs) {
        this.songs = songs;
        this.index = -1;
    }

    @Override public boolean hasNext() { return !songs.isEmpty(); } // always true
    @Override public Song next() {
        index = (index + 1) % songs.size();
        return songs.get(index);
    }
    @Override public boolean hasPrevious() { return !songs.isEmpty(); }
    @Override public Song previous() {
        index = (index - 1 + songs.size()) % songs.size();
        return songs.get(index);
    }
    @Override public Song current() {
        if (index < 0) return null;
        return songs.get(index);
    }
    @Override public void reset() { index = -1; }
    @Override public String getModeName() { return "Repeat All"; }
}

// ============================================================
// PLAYLIST — collection that produces iterators
// ============================================================

class Playlist {
    private final String name;
    private final List<Song> songs = new ArrayList<>();
    private PlaylistIterator currentIterator;

    public Playlist(String name) {
        this.name = name;
        this.currentIterator = new SequentialIterator(songs);
    }

    public void addSong(Song song) { songs.add(song); }
    public int size() { return songs.size(); }

    public PlaylistIterator sequential() {
        currentIterator = new SequentialIterator(songs);
        return currentIterator;
    }

    public PlaylistIterator shuffle() {
        currentIterator = new ShuffleIterator(songs);
        return currentIterator;
    }

    public PlaylistIterator repeatOne(int songIndex) {
        currentIterator = new RepeatOneIterator(songs, songIndex);
        return currentIterator;
    }

    public PlaylistIterator repeatAll() {
        currentIterator = new RepeatAllIterator(songs);
        return currentIterator;
    }

    public PlaylistIterator getCurrentIterator() { return currentIterator; }
    public String getName() { return name; }
}

// ============================================================
// MEDIA PLAYER — uses iterators uniformly
// ============================================================

class MediaPlayer {
    private Playlist playlist;
    private PlaylistIterator iterator;

    public void loadPlaylist(Playlist playlist) {
        this.playlist = playlist;
        this.iterator = playlist.sequential(); // default mode
        System.out.println("Loaded playlist: " + playlist.getName()
                + " (" + playlist.size() + " songs)");
    }

    public void setMode(PlaylistIterator newIterator) {
        this.iterator = newIterator;
        System.out.println("Mode: " + iterator.getModeName());
    }

    public void play() {
        if (iterator.hasNext()) {
            Song song = iterator.next();
            System.out.println("Now playing: " + song);
        }
    }

    public void skipForward() {
        if (iterator.hasNext()) {
            Song song = iterator.next();
            System.out.println("Skipped to: " + song);
        }
    }

    public void skipBackward() {
        if (iterator.hasPrevious()) {
            Song song = iterator.previous();
            System.out.println("Back to: " + song);
        }
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class PlaylistDemo {
    public static void main(String[] args) {
        Playlist playlist = new Playlist("Road Trip Mix");
        playlist.addSong(new Song("Bohemian Rhapsody", "Queen", 354));
        playlist.addSong(new Song("Hotel California", "Eagles", 391));
        playlist.addSong(new Song("Stairway to Heaven", "Led Zeppelin", 482));
        playlist.addSong(new Song("Comfortably Numb", "Pink Floyd", 383));

        MediaPlayer player = new MediaPlayer();
        player.loadPlaylist(playlist);

        // Sequential play
        System.out.println("\n--- Sequential ---");
        player.play(); player.play(); player.play();
        player.skipBackward();

        // Switch to shuffle
        System.out.println("\n--- Shuffle ---");
        player.setMode(playlist.shuffle());
        player.play(); player.play(); player.play(); player.play();

        // Repeat All — loops back to start
        System.out.println("\n--- Repeat All ---");
        player.setMode(playlist.repeatAll());
        for (int i = 0; i < 6; i++) player.play(); // plays 4 + wraps 2

        // Repeat One
        System.out.println("\n--- Repeat One ---");
        player.setMode(playlist.repeatOne(0));
        player.play(); player.play(); player.play(); // same song 3 times
    }
}
```

### Interview Talking Points

- **Iterator + Strategy hybrid:** Each iterator mode is also a strategy for how to traverse. This shows patterns often combine naturally.
- **Spotify/Apple Music:** Real music players use exactly this — shuffle generates a permutation list (not random per-call) so "previous" works correctly in shuffle mode.
- **Thread safety:** If playlist is modified while iterating, need `ConcurrentModificationException` or copy-on-write semantics. Our shuffle copies the list, making it safe.
