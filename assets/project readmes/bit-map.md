### Bitmap Image Processing Tool

The Bitmap project provides a command-line tool to read, manipulate, and process bitmap (.bmp) image files. This project involves various operations like reading headers, mirroring, applying filters, rotating, cropping, and combining multiple image processing operations.

## Features

1. **Header: Display Bitmap Header Information**

This command reads the header of a bitmap image and prints key metadata, such as file type, size, and dimensions.

    ```bash
    ./bitmap header <bitmap_file>


## Example Output:

    BMP Header:
    - FileType: BM
    - FileSizeInBytes: 518456
    - HeaderSize: 54

    DIB Header:
    - DibHeaderSize: 40
    - WidthInPixels: 480
    - HeightInPixels: 360
    - PixelSizeInBits: 24
    - ImageSizeInBytes: 518402

2. **Mirror: Mirror the Image**

Use the --mirror flag to mirror an image either horizontally or vertically.

    ```bash
    ./bitmap apply --mirror=horizontal <source_file> <output_file>


## Options:

- horizontal, h, horizontally, hor — Mirror horizontally
- vertical, v, vertically, ver — Mirror vertically

3. **Filter: Apply Image Filters**

Apply a series of filters to modify the image. Filters are applied sequentially in the order provided.

    ```bash
    ./bitmap apply --filter=<filter_type> <source_file> <output_file>


## Available Filters:

- blue: Keep only the blue channel

- red: Keep only the red channel

- green: Keep only the green channel

- grayscale: Convert to grayscale

- negative: Invert colors (negative filter)

- pixelate: Pixelate the image

- blur: Apply a blur effect


4. **Rotate: Rotate the Image**

Rotate the image by a specified angle using the --rotate flag.

    ```bash 
    ./bitmap apply --rotate=<direction> <source_file> <output_file>


## Options:

- Available rotating degrees: right, 90; left, -90: 180; 270, -270: 

5. **Crop: Crop the Image**

Trim the image by specifying the starting X and Y offsets, and the width and height to crop.

    ```bash 
    ./bitmap apply --crop=<OffsetX>-<OffsetY>-<Width>-<Height> <source_file> <output_file>


## Example:

    --crop=20-20-100-100 — Crops a 100x100 section starting from (20, 20).

6. **Combine Apply Options: Combine Multiple Operations**

You can chain multiple operations (e.g., filter, rotate, crop) in a single command. The operations will be applied sequentially.

    ```bash
    ./bitmap apply --mirror=horizontal --rotate=right --filter=negative --filter=grayscale <source_file> <output_file>

7. Help: Get Help Information

Use the --help flag to get detailed usage information about the commands and flags.

    ```bash
    ./bitmap --help
    ./bitmap header --help
    ./bitmap apply --help

## How to Use

1. **To get started with the project, clone the repository and install dependencies:**

    ```bash
   git clone <repository-url>
   cd bitmap
   go mod tidy

2. **Run program with `go build -o bitmap .`.**


## Edge Cases

- The program only accepts uncompressed 24-bit BMP files. Other formats will be rejected.
- Operations like cropping will be limited to the image's boundaries, and the program will return an error if the crop values exceed the image dimensions.
- Overwriting files: The program allows overwriting output files but will warn you before doing so.


## Contributers

1. Akerke - @akkassymb
2. Aibibi - @ainassyr
3. Daiana - @daarapbek