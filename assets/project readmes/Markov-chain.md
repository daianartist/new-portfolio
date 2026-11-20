# Markov Chain Text Generator (Go)

A Go implementation of a **Markov Chain text generator** — a statistical model that creates text resembling the structure and flow of natural language.  
This project follows the requirements of the *Alem Graph — Markov Chain* assignment.

---

## 📖 Overview

This tool reads a text (e.g., *The Great Gatsby*) and generates new text using the **Markov Chain algorithm**.  
It builds a probabilistic model of how words follow each other and then uses this model to produce realistic random text.

---

## 🚀 Features

- Reads input text from **stdin**
- Generates random text using the **Markov Chain algorithm**
- Supports options to control generation parameters:
  - `-w <N>` — maximum number of words (default: 100)
  - `-p <S>` — custom starting prefix (must exist in input text)
  - `-l <N>` — prefix length (default: 2, max: 5)
- Proper error messages for invalid inputs
- Fully complies with **gofumpt** formatting and **Go standard library only**

---

## 🧠 How It Works

1. The program reads all input text (space-separated words).
2. It constructs a *Markov state table*:
   - Each key = prefix of N words.
   - Each value = list of words that follow that prefix.
3. During generation:
   - It starts with the first prefix (or user-defined one).
   - It randomly selects a following word from that prefix’s suffix list.
   - The window slides forward, repeating the process until the word limit or text end.

---

## ⚙️ Usage

### Compile

From the project root:

```bash
go build -o markovchain ./cmd
```

### Run

#### Default run (reads from stdin)
```bash
cat the_great_gatsby.txt | ./markovchain
```

#### With max words
```bash
cat the_great_gatsby.txt | ./markovchain -w 10
```

#### With custom starting prefix
```bash
cat the_great_gatsby.txt | ./markovchain -w 10 -p "to play"
```

#### With longer prefix length
```bash
cat the_great_gatsby.txt | ./markovchain -w 10 -p "to something funny" -l 3
```

#### Help menu
```bash
./markovchain --help
```

---

## 🧩 Example Output

Input:
```
Bad programmers worry about code. Good programmers worry about data structures and their relationships.
```

Generated output example:
```
Bad programmers worry about data structures and their relationships. Good programmers worry about code.
```

---

## ⚠️ Error Handling

| Error case | Example | Output |
|-------------|----------|--------|
| No input text | `./markovchain` | `Error: no input text` |
| Negative word count | `./markovchain -w -1` | `Error: invalid word count` |
| Invalid prefix | `./markovchain -p "something missing"` | `Error: prefix not found in text` |
| Invalid prefix length | `./markovchain -l 10` | `Error: prefix length must be ≤ 5` |

---

## 🧱 Project Structure

```
markovchain/
├── cmd/
│   └── main.go        # Entry point
├── internal/
│   ├── logic.go
└── data/
    └── the_great_gatsby.txt
```
