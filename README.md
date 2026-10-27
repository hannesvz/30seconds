# 30 Seconds — Modern Word List

A fresh, South-African-flavoured word list for the board game *30 Seconds*, plus a small web app that mocks up game cards.

**Play it:** https://hannesvz.github.io/30seconds/

## What's here

- **`wordlist.json`** — ~2250 clues split into two decks:
  - `young` — well known to someone born after 2000
  - `old` — well known to someone born before 2000

  Each deck is grouped into categories (people, places, food & drink, entertainment, sport, brands & tech, etc.) for balance. Evergreen clues such as Mandela, braai and the Springboks appear in both decks on purpose.

- **`index.html`** — a card mockup. Pick a deck with the young/old toggle, hit **Next** to draw a card of 5 random concepts from across all categories. Each card renders on a blue or yellow template, chosen at random.

- **`card_blue.png` / `card_yellow.png`** — the card background templates.

- **`instructions.md`** — notes on sourcing extra entities from Wikidata (SPARQL + Python) if you want to extend the list.

## Running locally

The app fetches `wordlist.json`, so it needs to be served over HTTP (not opened as a `file://`):

```sh
python3 -m http.server 8000
```

Then open http://localhost:8000/.
