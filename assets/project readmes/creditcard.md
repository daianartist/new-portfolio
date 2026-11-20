# CreditCard

A small, reliable command-line tool that works with **credit card numbers** — it can validate, generate, inspect, and issue them based on the **Luhn algorithm** and given brand/issuer data files.

It supports input both through **arguments** and **STDIN**, and outputs clean, human-readable results.

---

## Features (matches implementation)

-  **Validate** numbers using Luhn’s algorithm  
-  **Generate** possible numbers from templates like `440043018030****`  
-  **Show information** about a card’s validity, brand, and issuer  
-  **Issue** a random valid card number for a given brand and issuer  
-  Uses `brands.txt` and `issuers.txt` files for mapping prefixes  
-  Fully CLI-based — works with `stdin` or direct arguments  
-  Built only with Go’s **standard library**  
-  Strictly formatted with **gofumpt**  

---

## Build & Run

From the project root (where your `go.mod` and `cmd/main.go` are located):

```bash
# Format code according to gofumpt rules (required)
gofumpt -l -w .

# Build executable
go build -o creditcard ./cmd

# Run
./creditcard <command> [flags] [arguments]
```

---

## Commands

### 1️⃣ Validate

Checks if a credit card number is valid according to the **Luhn Algorithm**.

#### Usage
```bash
./creditcard validate "4400430180300003"
```

#### Output
```
OK
```

If the number is invalid:
```
INCORRECT
```

#### Multiple numbers
```bash
./creditcard validate "4400430180300003" "4400430180300011"
OK
OK
```

#### From STDIN
```bash
echo "4400430180300003 4400430180300011" | ./creditcard validate --stdin
OK
OK
```

🟡 Exit Codes  
- `0` → all numbers valid  
- `1` → one or more invalid inputs  

---

### 2️⃣ Generate

Replaces up to **4 trailing asterisks (`*`)** with digits to generate possible card numbers.

#### Usage
```bash
./creditcard generate "440043018030****"
```

#### Output
```
4400430180300003
4400430180300011
4400430180300029
...
4400430180309996
```

#### Random pick
```bash
./creditcard generate --pick "440043018030****"
4400430180304385
```

🟡 Errors  
- More than 4 asterisks → `stderr` + exit code `1`  
- Asterisks not at end → `stderr` + exit code `1`

---

### 3️⃣ Information

Displays **card number details** (validity, brand, issuer).

#### Usage
```bash
./creditcard information   --brands=data/brands.txt   --issuers=data/issuers.txt   "4400430180300003"
```

#### Output
```
4400430180300003
Correct: yes
Card Brand: VISA
Card Issuer: Kaspi Gold
```

If the number is invalid:
```
4400450180300003
Correct: no
Card Brand: -
Card Issuer: -
```

#### From STDIN
```bash
echo "4400430180300003" | ./creditcard information --brands=data/brands.txt --issuers=data/issuers.txt --stdin
```

---

### 4️⃣ Issue

Generates a **random valid** card number for a given brand and issuer.

#### Usage
```bash
./creditcard issue   --brands=data/brands.txt   --issuers=data/issuers.txt   --brand=VISA   --issuer="Kaspi Gold"
```

#### Output
```
4400430180300003
```

🟡 Errors  
If brand/issuer mismatch or missing → exit `1`

---

## Input Data Files

### `data/brands.txt`

```
VISA:4
MASTERCARD:51
MASTERCARD:52
MASTERCARD:53
MASTERCARD:54
MASTERCARD:55
AMEX:34
AMEX:37
```

### `data/issuers.txt`

```
Kaspi Gold:440043
Forte Black:404243
Forte Blue:517792
Halyk Bonus:440563
Jusan Pay:539545
```

Each line = `Name:Prefix`  
Used for matching card number prefixes.

---

## Project Structure

```
cardcli/
├── cmd/
│   └── main.go
├── internal/
│   ├── luhn.go         # Luhn algorithm
│   ├── prefixes.go     # Brand/issuer file parser
│   ├── validate.go     # Validation logic
│   ├── generate.go     # Generation logic
│   ├── info.go         # Info lookup
│   └── issue.go        # Issuance logic
├── brands.txt
└── issuers.txt
```

---

## Error Handling

- Invalid arguments or missing flags → descriptive message on `stderr`
- File read errors → exit `1`
- Any incorrect card number → printed as `INCORRECT`
- Program **never panics** (`index out of range`, `nil deref`, etc.)
- All input checked before processing

---

## Exit Codes

| Code | Meaning |
|------|----------|
| `0` | Success |
| `1` | Invalid input, file error, or failed validation |

---

## Example Session

```bash
$ ./creditcard validate "4400430180300003"
OK

$ ./creditcard generate "440043018030****" | head -3
4400430180300003
4400430180300011
4400430180300029

$ ./creditcard information --brands=data/brands.txt --issuers=data/issuers.txt "4400430180300003"
4400430180300003
Correct: yes
Card Brand: VISA
Card Issuer: Kaspi Gold
```

---