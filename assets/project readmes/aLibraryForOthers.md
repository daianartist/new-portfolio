# CSV Parser in Go "A library for others"

This project implements a custom CSV parsing library in Go, following
strict requirements:

## Features

-   Reads CSV line by line using `io.Reader`.
-   Handles fields with quotes and escaped quotes (`""`).
-   Detects malformed lines and returns `ErrQuote`.
-   Correctly processes:
    -   Comma-separated fields
    -   Quoted fields
    -   Escaped quotes
    -   Empty fields and empty lines
    -   Last line without newline
-   Provides:
    -   `GetField(n int)` --- returns the nth field
    -   `GetNumberOfFields()` --- returns number of fields in last read
        line
    -   `ReadLine(r io.Reader)` --- reads one line without `\n`

## Error Handling

-   If a line has incorrect quotes, the parser returns:
    -   Empty string
    -   `ErrQuote`
-   After any non-EOF error, the caller should stop reading the file.
-   `io.EOF` is returned only when the file truly ends.

## Usage Example

``` go
for {
    line, err := csvparser.ReadLine(file)
    if err != nil {
        if err == io.EOF {
            break
        }
        fmt.Println("Error:", err)
        return
    }
    // Process fields...
}
```

## Output Format

Each parsed line is shown in console like:

    Line 1: "Alice,20,London"
    Fields: 3
      Field 1: Alice
      Field 2: 20
      Field 3: London

## Behavior After Errors

If the parser encounters an error such as missing or excess quotes,
reading must stop immediately.\
This matches the project's required behavior and the example in the
official specification.

## Structure

    internal/
        parser.go   — parsing logic
        logic.go    — CSVParser interface + errors
    main.go

## Requirements Followed

✔ Uses only `io.Reader`\
✔ No full-file reads --- reads until newline each call\
✔ No panics or unexpected exits\
✔ Follows CSV rules for quoting\
✔ Correct behavior after EOF\
✔ Clean and readable code following Go style

------------------------------------------------------------------------