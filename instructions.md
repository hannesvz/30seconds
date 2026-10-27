# How to Build a Custom South African "30 Seconds" Dataset

This guide explains how to generate a custom, localized dataset of South African entities (people, places, landmarks, food, and cultural references) using open-source Wikidata queries, format them into 5-clue cards, and export them as JSON/CSV for use in apps or printables.

---

## Overview of the Process

1. **Extract Data:** Run SPARQL queries against Wikidata to fetch localized South African entities across different categories (Sports, Geography, Pop Culture, History, Cuisine).
2. **Process & Format:** Use a Python script to clean the data and group entries into standard 5-clue *30 Seconds* card layouts.
3. **Export:** Save the output as structured JSON or CSV files.

---

## Step 1: Gather Entities via Wikidata SPARQL

Wikidata contains structured information on millions of topics. You can query its public SPARQL endpoint at [query.wikidata.org](https://query.wikidata.org/).

### Sample SPARQL Queries

Run these queries on [query.wikidata.org](https://query.wikidata.org/) and download the results as JSON or CSV.

#### Query A: Famous South African People
```sparql
SELECT DISTINCT ?itemLabel WHERE {
  ?item wdt:P27 wd:Q258.          # Country of citizenship: South Africa
  ?item wdt:P106 ?occ.            # Has an occupation
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
LIMIT 500
```

#### Query B: South African Geography & Landmarks
```sparql
SELECT DISTINCT ?itemLabel WHERE {
  ?item wdt:P17 wd:Q258.          # Located in South Africa
  ?item wdt:P31/wdt:279* wd:Q2221906. # Is a geographic feature / location / landmark
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
LIMIT 500
```

---

## Step 2: Python Script to Parse and Format Cards

Use the following Python script to take raw entity lists, shuffle them, remove duplicates/formatting errors, and output cards containing 5 items each.

```python
import json
import random

def load_word_list(filename):
    """Loads a line-separated text file of clues."""
    with open(filename, 'r', encoding='utf-8') as f:
        return [line.strip() for line in f if line.strip()]

def generate_cards(clues, items_per_card=5):
    """Shuffles clues and groups them into 5-clue cards."""
    # Deduplicate while preserving basic cleaning
    clean_clues = list(set([c for c in clues if len(c) > 2]))
    random.shuffle(clean_clues)
    
    cards = []
    card_id = 1
    
    for i in range(0, len(clean_clues), items_per_card):
        chunk = clean_clues[i:i + items_per_card]
        if len(chunk) == items_per_card:
            cards.append({
                "card_id": card_id,
                "clues": chunk
            })
            card_id += 1
            
    return cards

# Example usage:
if __name__ == "__main__":
    # Seed data (can be replaced with loaded files from Wikidata)
    sample_sa_clues = [
        "Table Mountain", "Siya Kolisi", "Biltong", "Kruger National Park", "Amapiano",
        "Durban", "Boerewors", "Nelson Mandela", "Soweto", "Springboks",
        "Trevor Noah", "Robben Island", "Braai", "Bafana Bafana", "Rands",
        "Vuvuzela", "Drakensberg", "Chakalaka", "Cape Point", "Kirstenbosch",
        "Bunny Chow", "Caster Semenya", "Gold Reef City", "Potjiekos", "Proteas"
    ]
    
    deck = generate_cards(sample_sa_clues, items_per_card=5)
    
    # Export to JSON
    with open('sa_30_seconds_deck.json', 'w', encoding='utf-8') as f:
        json.dump(deck, f, indent=2, ensure_ascii=False)
        
    print(f"Successfully generated {len(deck)} cards!")
```

---

## Step 3: Expected JSON Output Structure

The output file `sa_30_seconds_deck.json` will match the format standard for game applications:

```json
[
  {
    "card_id": 1,
    "clues": [
      "Table Mountain",
      "Siya Kolisi",
      "Biltong",
      "Kruger National Park",
      "Amapiano"
    ]
  },
  {
    "card_id": 2,
    "clues": [
      "Durban",
      "Boerewors",
      "Nelson Mandela",
      "Soweto",
      "Springboks"
    ]
  }
]
```

---

## Step 4: Converting to CSV for Printing / Physical Cards

If you want to print physical cards or import them into spreadsheet software like Google Sheets or Excel, use this script to convert the JSON output to CSV format:

```python
import json
import csv

with open('sa_30_seconds_deck.json', 'r', encoding='utf-8') as f:
    cards = json.load(f)

with open('sa_30_seconds_deck.csv', 'w', newline='', encoding='utf-8') as f:
    writer = csv.writer(f)
    # Header row
    writer.writerow(['Card ID', 'Clue 1', 'Clue 2', 'Clue 3', 'Clue 4', 'Clue 5'])
    
    for card in cards:
        writer.writerow([card['card_id']] + card['clues'])
```

---

## Categorization Tip for Balanced Decks

To make cards balanced and engaging like the official board game, mix items across categories rather than grouping them randomly:

1. **Category 1:** South African Places & Geography
2. **Category 2:** South African Figures / Celebrities / Sports Stars
3. **Category 3:** Local Culture / Food / Slang
4. **Category 4:** Global Landmarks or International Knowledge
5. **Category 5:** History or General Science

You can maintain 5 separate lists (one for each category) and take 1 item from each list when constructing a card in Python.