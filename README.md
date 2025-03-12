# Image Processing & Enhancement

## Description
"Image Processing & Enhancement" is a C-based program that provides fundamental image processing capabilities. It supports PGM (grayscale) and PPM (color) images and allows users to apply transformations, filters, and other enhancements. The program efficiently handles images using dynamic memory allocation.

## Features
- Load and save images in both ASCII and binary formats
- Select specific regions or apply operations to the entire image
- Perform transformations: **rotate, crop**
- Apply filters: **edge detection, blur, sharpen, Gaussian blur**
- Histogram generation for grayscale images
- Histogram equalization to enhance contrast
- Supports PGM (grayscale) and PPM (color) images
- Efficient memory handling

## Installation
1. Clone or download the repository.
2. Compile the program using a C compiler (GCC recommended):
   ```sh
   gcc -o image_processor image_processor.c -lm
   ```
3. Run the program:
   ```sh
   ./image_processor
   ```

## Usage
The program is interactive, allowing users to enter commands for various image processing tasks.

### Available Commands:
- **LOAD \<filename\>** – Load an image (PGM/PPM in ASCII or binary format)
- **SAVE \<filename\> [ascii]** – Save the image (optionally in ASCII format)
- **SELECT ALL** – Select the entire image
- **SELECT x1 y1 x2 y2** – Select a specific region of the image
- **ROTATE angle** – Rotate the image by multiples of 90°
- **APPLY \<filter\>** – Apply a filter (**EDGE, SHARPEN, BLUR, GAUSSIAN_BLUR**)
- **HISTOGRAM x y** – Generate a histogram for a grayscale image
- **EQUALIZE** – Perform histogram equalization
- **CROP** – Crop the image to the selected region
- **EXIT** – Exit the program

## Example Usage
1. Load an image:
   ```sh
   LOAD image.pgm
   ```
2. Select a region:
   ```sh
   SELECT 10 10 100 100
   ```
3. Apply a sharpen filter:
   ```sh
   APPLY SHARPEN
   ```
4. Rotate the image 90 degrees:
   ```sh
   ROTATE 90
   ```
5. Save the processed image:
   ```sh
   SAVE output.pgm ascii
   ```

## License
This project is licensed under the MIT License.

