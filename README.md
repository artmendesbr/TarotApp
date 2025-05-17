# TarotApp 🃏 - The Tarot Reading Journal

A tarot reading tracker and journal built with [Appsmith](https://www.appsmith.com/) and hosted on [Neon](https://neon.tech/).  
This app allows users to log tarot readings, track the cards drawn, and review past readings.

---

## 📌 Features

- **Create a Reading**: Choose a reader, querent, spread type, and enter a question.
- **Card Logging**: For each spread, log each card drawn with its position, whether it's reversed, and add notes.
- **Custom Spreads**: Define custom spreads with labels (e.g., Past, Present, Future).
- **Spread Notes**: Add a spread note for the entire reading.
- **Review Past Readings**: View and analyze past readings with the ability to filter by querent, reader, or spread.

---

## 🗃️ Database Schema

The app is powered by a PostgreSQL database hosted on Neon. Here's an overview of the schema:

### 1. `readings` Table
Stores details about each tarot reading session.

```sql
CREATE TABLE readings (
    id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    spread_id INTEGER NOT NULL,
    reader_id INTEGER NOT NULL,
    querent_id INTEGER NOT NULL,
    question TEXT,
    notes TEXT,
    created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_spread FOREIGN KEY (spread_id) REFERENCES public.spreads(id) ON DELETE SET NULL,
    CONSTRAINT fk_reader FOREIGN KEY (reader_id) REFERENCES public.users(id),
    CONSTRAINT fk_querent FOREIGN KEY (querent_id) REFERENCES public.users(id)
);
```

### 2. `cards` Table
Stores individual tarot cards information
```sql
CREATE TABLE cards (
    id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    deck_id INTEGER NOT NULL,  -- The deck this card belongs to
    name TEXT NOT NULL,  -- Card name (e.g., "The Fool")
    esoteric_title TEXT,  -- Esoteric title (e.g., "The Magician")
    suit_id INTEGER,  -- The suit the card belongs to (e.g., Wands, Cups)
    number INTEGER,  -- The number (if applicable, for numbered cards)
    court_rank_id INTEGER,  -- Rank (if applicable, for court cards)
    image_url TEXT,  -- URL to card image
    meaning TEXT,  -- General meaning of the card
    keywords TEXT[],  -- Keywords for the card
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_deck FOREIGN KEY (deck_id) REFERENCES public.decks(id) ON DELETE CASCADE
);
```

### 3. `card_logs` Table
Logs each card drawn in a reading, including position, reversed status, and notes.
```sql
CREATE TABLE card_logs (
    id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    reading_id INTEGER NOT NULL,
    card_id INTEGER NOT NULL,
    position_index INTEGER NOT NULL,  -- Position in the spread (1 for Past, 2 for Present, etc.)
    is_extra BOOLEAN DEFAULT false,  -- Whether this card is an extra one
    reversed BOOLEAN DEFAULT false,  -- Whether the card is reversed
    notes TEXT,  -- Notes for this specific card in this position
    created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_reading FOREIGN KEY (reading_id) REFERENCES public.readings(id) ON DELETE CASCADE,
    CONSTRAINT fk_card FOREIGN KEY (card_id) REFERENCES public.cards(id) ON DELETE CASCADE
);
```

## 🔧 How It Works
1. Start a Reading
Reader selects the reading session details (reader, querent, spread type, question).

A new reading record is created in the readings table.

2. Select Cards for the Spread
Based on the selected spread, positions (e.g., Past, Present, Future) are populated.

For each position, the user selects a card from the available tarot deck.

The app logs this card selection in the card_logs table, along with the position, reversed status, and any notes.

3. Spread Notes
The reading can have a global note which is stored in the readings table.

4. 

## 🚀 Future Enhancements
Review Past Readings: Past readings will be accessible for review, allowing users to filter and analyze past tarot readings.

Data Exploration: Data visualization to identify recurrent cards and themes.

## 📷 Screenshots
| Create Reading | Select Cards UI |
|-----------|-----------|
| ![Home](screenshots/sc-001.jpg) | ![Routine/Exercise](screenshots/sc-002.jpg) |

| Add Notes | Select Cards |
|-----------|-----------|
| ![Log](screenshots/sc-003.jpg) | ![History](screenshots/sc-004.jpg) |


## 🛠️ Technologies Used
Appsmith: Used for building the front-end UI with drag-and-drop components and custom logic.

PostgreSQL: For the relational database, hosted on Neon, to store readings, cards, and logs.

Neon: Cloud hosting for PostgreSQL databases.
