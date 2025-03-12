// "Image Processing & Enhancement" -> Powered by Romaniuc Albert-Iulian

/*
   Image Processing Program
   This program allows basic image processing operations on PGM (grayscale) and PPM (color) images.
   The program can:
   - Load and save images in ASCII and binary formats.
   - Select regions or the entire image for processing.
   - Apply transformations such as rotate, crop, and filters (edge detection, blur, sharpen, equalization, etc.).
   - Generate histograms for grayscale images.
   - Apply various filters using convolution kernels.
   The program uses dynamic memory allocation to handle images efficiently.
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
#include <stdint.h>
#include <stdbool.h>
#define NMAX 30

// Image
typedef struct {
	int latime, inaltime, ci;
	unsigned char ***px;
} im;

// Selection
typedef struct {
	int x1, x2, y1, y2;
	bool ok;
} ss;

// Function to load an image from a file (PGM/PPM in ASCII or binary format)
im *load(char *nume)
{
	// Open the file in binary mode
	FILE *file = fopen(nume, "rb");
	if (!file) {
		printf("Failed to load %s\n", nume);
		return NULL;
	}
	int max_value, pixel_value;
	char s[3];
	// Allocate memory for the image structure
	im *a = NULL;
	a = malloc(sizeof(im));
	fscanf(file, "%3s ", s);
	fscanf(file, "%d %d %d ", &a->latime, &a->inaltime, &max_value);
	// Allocate memory for image data
	a->px = (unsigned char ***)malloc(a->latime * sizeof(unsigned char **));
	for (int i = 0; i < a->latime; i++)
		a->px[i] =
		(unsigned char **)malloc(a->inaltime * sizeof(unsigned char *));
	// Load image data based on format type
	if (s[1] == '2') {
		// PGM format (grayscale, 1 channel)
		a->ci = 1;
		// Allocate memory for each pixel
		for (int i = 0; i < a->latime; i++)
			for (int j = 0; j < a->inaltime; j++)
				a->px[i][j] =
				(unsigned char *)malloc(a->ci * sizeof(unsigned char));
		// Read pixel values (1 channel per pixel)
		for (int i = 0; i < a->inaltime; i++) {
			for (int j = 0; j < a->latime; j++) {
				fscanf(file, "%d ", &pixel_value);
				a->px[j][i][0] = pixel_value;
			}
		}
	} else if (s[1] == '3') {
		// PPM format (color, 3 channels: R, G, B)
		a->ci = 3;
		// Allocate memory for each pixel
		for (int i = 0; i < a->latime; i++)
			for (int j = 0; j < a->inaltime; j++)
				a->px[i][j] =
				(unsigned char *)malloc(a->ci * sizeof(unsigned char));
		// Read pixel values (3 channels per pixel)
		for (int i = 0; i < a->inaltime; i++) {
			for (int j = 0; j < a->latime; j++) {
				fscanf(file, "%d ", &pixel_value);
				a->px[j][i][0] = pixel_value;  // R
				fscanf(file, "%d ", &pixel_value);
				a->px[j][i][1] = pixel_value;  // G
				fscanf(file, "%d ", &pixel_value);
				a->px[j][i][2] = pixel_value;  // B
			}
		}
	} else if (s[1] == '5') {
		// PGM format (grayscale, 1 channel), binary mode
		a->ci = 1;
		// Allocate memory for each pixel
		for (int i = 0; i < a->latime; i++)
			for (int j = 0; j < a->inaltime; j++)
				a->px[i][j] =
				(unsigned char *)malloc(a->ci * sizeof(unsigned char));
		// Read pixel values from binary file (1 channel per pixel)
		for (int i = 0; i < a->inaltime; i++) {
			for (int j = 0; j < a->latime; j++) {
				fread(&a->px[j][i][0], 1, 1, file);
			}
		}
	} else if (s[1] == '6') {
		// PPM format (color, 3 channels), binary mode
		a->ci = 3;
		// Allocate memory for each pixel
		for (int i = 0; i < a->latime; i++)
			for (int j = 0; j < a->inaltime; j++)
				a->px[i][j] =
				(unsigned char *)malloc(a->ci * sizeof(unsigned char));
		// Read pixel values from binary file (3 channels per pixel)
		for (int i = 0; i < a->inaltime; i++) {
			for (int j = 0; j < a->latime; j++) {
				fread(&a->px[j][i][0], 1, 1, file); // R
				fread(&a->px[j][i][1], 1, 1, file); // G
				fread(&a->px[j][i][2], 1, 1, file); // B
			}
		}
	}
	// Close the file after reading the data
	fclose(file);
	printf("Loaded %s\n", nume);
	return a;
}

// Function to save an image to a file in either ASCII or binary format
void save(im *a, char *nume, bool ok)
{
	if (!a) {
		printf("No image loaded\n");
		return;
	}
	// Open the file in the appropriate mode (w for ASCII, wb for binary)
	FILE *f = ok ? fopen(nume, "w") : fopen(nume, "wb");
	if (!f) {
		printf("Failed to open file for saving\n");
		return;
	}
	// Write the header for PGM/PPM based on format
	if (a->ci == 1) {
		fprintf(f, ok ? "P2\n" : "P5\n");
	} else {
		fprintf(f, ok ? "P3\n" : "P6\n");
	}
	// Write image dimensions and max pixel value
	fprintf(f, "%d %d\n255\n", a->latime, a->inaltime);
	// Write image data
	for (int i = 0; i < a->inaltime; i++) {
		for (int j = 0; j < a->latime; j++) {
			for (int k = 0; k < a->ci; k++) {
				if (ok) {
					// ASCII format
					fprintf(f, "%d ", a->px[j][i][k]);
				} else {
					// Binary format
					fwrite(&a->px[j][i][k], sizeof(unsigned char), 1, f);
				}
			}
		}
		if (ok) {
			// Add a newline at the end of each row for ASCII format
			fprintf(f, "\n");
		}
	}
	// Close the file
	fclose(f);
	printf("Saved %s\n", nume);
}

// Function to select a specific region of the image
void sselectie(im *a, ss *m, int x1, int y1, int x2, int y2)
{
	if (!a) {
		printf("No image loaded\n");
		return;
	}
	if (x2 < x1) {
		int aux = x2;
		x2 = x1;
		x1 = aux;
	}
	if (y2 < y1) {
		int aux = y2;
		y2 = y1;
		y1 = aux;
	}
	if (x1 < 0 || y1 < 0 || x2 > a->latime ||
		y2 > a->inaltime || y1 == y2) {
		printf("Invalid set of coordinates\n");
		return;
	}
	m->x1 = x1;
	m->x2 = x2;
	m->y1 = y1;
	m->y2 = y2;
	m->ok = true;
	printf("Selected %d %d %d %d\n", x1, y1, x2, y2);
}

// Function to select the entire image
void sselectietotala(im *a, ss *m)
{
	if (!a) {
		printf("No image loaded\n");
		return;
	}
	m->x1 = 0;
	m->y1 = 0;
	m->x2 = a->latime;
	m->y2 = a->inaltime;
	m->ok = true;
	printf("Selected ALL\n");
}

// Function to clamp a value within the range [0, 255]
unsigned char verif(int x)
{
	if (x < 0) {
		return 0;
	}
	if (x > 255) {
		return 255;
	}
	return (unsigned char)x;
}

  // Function to compute and display the histogram of a grayscale image
  void histogram(im *a, int x, int y)
  {
  	// Check if an image is loaded
  	if (!a) {
  		printf("No image loaded\n");
  		return;
  	}
  	// Ensure the image is grayscale (single channel)
  	if (a->ci != 1) {
  		printf("Black and white image needed\n");
  		return;
  	}
  	// Validate y is within the allowed range [2, 256]
  	if (y < 2 || y > 256) {
  		return;
  	}
  	// Ensure y is a power of 2
  	int aux = y;
  	while (aux % 2 == 0) {
  		aux /= 2;
  	}
  	if (aux != 1) {
  		return;
  	}
  	int binSize = 256 / y;
  	// Allocate memory for the histogram bins
  	int *binCounts = malloc(y * sizeof(int));
  	if (!binCounts) {
  		return;
  	}
  	for (int i = 0; i < y; i++) {
  		binCounts[i] = 0;
  	}
  	// Compute histogram based on pixel values
  	for (int i = 0; i < a->latime; i++) {
  		for (int j = 0; j < a->inaltime; j++) {
  			unsigned char pixelValue = a->px[i][j][0];
  			int index = pixelValue / binSize;
  			// Ensure index is within range [0, y-1]
  			if (index >= y) {
  				index = y - 1;
  			}
  			binCounts[index]++;
  		}
  	}
  	// Determine the maximum value in the histogram for scaling
  	int maxCount = 0;
  	for (int i = 0; i < y; i++) {
  		if (binCounts[i] > maxCount) {
  			maxCount = binCounts[i];
  		}
  	}
  	// Print the histogram
  	for (int i = 0; i < y; i++) {
  		int scaledValue = (binCounts[i] * x) / maxCount;
  		printf("%d\t|\t", scaledValue);
  		for (int val = 0; val < scaledValue; val++) {
  			printf("*");
  		}
  		printf("\n");
  	}
  	free(binCounts);
  }

// Function to equalize the histogram of a grayscale image
void equalize(im *imagineSursa)
{
	// Check if an image is loaded
	if (!imagineSursa) {
		printf("No image loaded\n");
		return;
	}
	// Ensure the image is grayscale (single channel)
	if (imagineSursa->ci != 1) {
		printf("Black and white image needed\n");
		return;
	}
	// Initialize histogram and required variables
	int grayscaleHistogram[256] = {0};
	int imageWidth = imagineSursa->latime;
	int imageHeight = imagineSursa->inaltime;
	int totalPixels = imageWidth * imageHeight;

	// Compute histogram
	for (int x = 0; x < imageWidth; x++) {
		for (int y = 0; y < imageHeight; y++) {
			unsigned char intensity = imagineSursa->px[x][y][0];
			grayscaleHistogram[intensity]++;
		}
	}
	// Compute cumulative distribution function (CDF)
	int cumulative[256];
	cumulative[0] = grayscaleHistogram[0];
	for (int i = 1; i < 256; i++) {
		cumulative[i] = cumulative[i - 1] + grayscaleHistogram[i];
	}
	// Build equalization lookup table (LUT)
	unsigned char equalizationLUT[256];
	float totalPixelsFloat = (float)totalPixels;

	for (int i = 0; i < 256; i++) {
		float cdfValue = 255.0f * ((float)cumulative[i] / totalPixelsFloat);
		cdfValue = round(cdfValue);
		cdfValue = verif(cdfValue);
		equalizationLUT[i] = (unsigned char)cdfValue;
	}
	// Apply LUT to the image
	for (int x = 0; x < imageWidth; x++) {
		for (int y = 0; y < imageHeight; y++) {
			imagineSursa->px[x][y][0] = equalizationLUT[imagineSursa->px[x][y][0]];
		}
	}
	// Display completion message
	printf("Equalize done\n");
}

// Function to crop an image based on a selected region
void crop(im *a, ss *m)
{
	// Calculate the new dimensions of the cropped image
	int new_width = m->x2 - m->x1, new_height = m->y2 - m->y1;
	// Check if an image is loaded
	if (!a) {
		printf("No image loaded\n");
		return;
	}
	// If the selected region matches the original dimensions, no need to crop
	if (new_width == a->latime && new_height == a->inaltime) {
		printf("Image cropped\n");
		return;
	}
	// Allocate memory for the cropped image
	unsigned char ***cropped;
	cropped = (unsigned char ***)malloc(new_width * sizeof(unsigned char **));
	if (!cropped) {
		return;
	}
	for (int i = 0; i < new_width; i++) {
		cropped[i] =
		(unsigned char **)malloc(new_height * sizeof(unsigned char *));
		if (!cropped[i]) {
			// Free previously allocated memory in case of failure
			for (int j = 0; j < i; j++) {
				free(cropped[j]);
			}
			free(cropped);
			return;
		}
		for (int j = 0; j < new_height; j++) {
			cropped[i][j] =
			(unsigned char *)malloc(a->ci * sizeof(unsigned char));
			if (!cropped[i][j]) {
				// Free previously allocated memory in case of failure
				for (int k = 0; k < j; k++) {
					free(cropped[i][k]);
				}
				for (int k = 0; k < i; k++) {
					free(cropped[k]);
				}
				free(cropped);
				return;
			}
		}
	}
	// Copy pixel data from the selected region to the new cropped image
	for (int i = 0; i < new_width; i++) {
		for (int j = 0; j < new_height; j++) {
			for (int k = 0; k < a->ci; k++) {
				cropped[i][j][k] = a->px[i + m->x1][j + m->y1][k];
			}
		}
	}
	// Free memory of the original image data
	for (int i = 0; i < a->latime; i++) {
		for (int j = 0; j < a->inaltime; j++) {
			free(a->px[i][j]);
		}
		free(a->px[i]);
	}
	free(a->px);
	// Update image structure with the cropped image data
	a->latime = new_width;
	a->inaltime = new_height;
	a->px = cropped;
	// Reset selection to cover the entire cropped image
	m->x1 = 0;
	m->y1 = 0;
	m->x2 = new_width;
	m->y2 = new_height;
	m->ok = true;
	printf("Image cropped\n");
}

// Function to apply an edge detection filter to an image or a selected region
void edge(im *a, ss *m)
{
	int edge[3][3] = {
		{-1, -1, -1},
		{-1,  8, -1},
		{-1, -1, -1}
	};
	if (m->ok == 0) {
		m->x1 = 0;
		m->y1 = 0;
		m->x2 = a->latime;
		m->y2 = a->inaltime;
	}
	unsigned char ***aux =
	(unsigned char ***)malloc(a->latime * sizeof(unsigned char **));
	if (!aux) {
		return;
	}
	for (int i = 0; i < a->latime; i++) {
		aux[i] =
		(unsigned char **)malloc(a->inaltime * sizeof(unsigned char *));
		if (!aux[i]) {
			return;
		}
		for (int j = 0; j < a->inaltime; j++) {
			aux[i][j] =
			(unsigned char *)malloc(a->ci * sizeof(unsigned char));
			if (!aux[i][j]) {
				return;
			}
		}
	}
	for (int c = 0; c < a->ci; c++) {
		for (int i = m->x1; i < m->x2; i++) {
			for (int j = m->y1; j < m->y2; j++) {
				if (i == 0 || j == 0 || i == a->latime - 1 ||
					j == a->inaltime - 1) {
					aux[i][j][c] = a->px[i][j][c];
				} else {
					int sum = 0;
					sum += edge[0][0] * a->px[i - 1][j - 1][c];
					sum += edge[0][1] * a->px[i - 1][j][c];
					sum += edge[0][2] * a->px[i - 1][j + 1][c];
					sum += edge[1][0] * a->px[i][j - 1][c];
					sum += edge[1][1] * a->px[i][j][c];
					sum += edge[1][2] * a->px[i][j + 1][c];
					sum += edge[2][0] * a->px[i + 1][j - 1][c];
					sum += edge[2][1] * a->px[i + 1][j][c];
					sum += edge[2][2] * a->px[i + 1][j + 1][c];
					aux[i][j][c] = verif(sum);
				}
			}
		}
	}
	for (int i = m->x1; i < m->x2; i++) {
		for (int j = m->y1; j < m->y2; j++) {
			for (int c = 0; c < a->ci; c++) {
				a->px[i][j][c] = aux[i][j][c];
			}
		}
	}
	for (int i = 0; i < a->latime; i++) {
		for (int j = 0; j < a->inaltime; j++) {
			free(aux[i][j]);
		}
		free(aux[i]);
	}
	free(aux);
	printf("APPLY EDGE done\n");
}

// Function to apply a sharpen filter to an image or a selected region
void sharpen(im *a, ss *m)
{
	int sha[3][3] = {
		{0, -1, 0},
		{-1, 5, -1},
		{0, -1, 0}
	};
	if (m->ok == 0) {
		m->x1 = 0;
		m->y1 = 0;
		m->x2 = a->latime;
		m->y2 = a->inaltime;
	}
	unsigned char ***aux =
	(unsigned char ***)malloc(a->latime * sizeof(unsigned char **));
	if (!aux) {
		return;
	}
	for (int i = 0; i < a->latime; i++) {
		aux[i] =
		(unsigned char **)malloc(a->inaltime * sizeof(unsigned char *));
		if (!aux[i]) {
			return;
		}
		for (int j = 0; j < a->inaltime; j++) {
			aux[i][j] =
			(unsigned char *)malloc(a->ci * sizeof(unsigned char));
			if (!aux[i][j]) {
				return;
			}
		}
	}
	for (int c = 0; c < a->ci; c++) {
		for (int i = m->x1; i < m->x2; i++) {
			for (int j = m->y1; j < m->y2; j++) {
				if (i == 0 || j == 0 || i == a->latime - 1 ||
					j == a->inaltime - 1) {
					aux[i][j][c] = a->px[i][j][c];
				} else {
					int sum = 0;
					sum += sha[0][0] * a->px[i - 1][j - 1][c];
					sum += sha[0][1] * a->px[i - 1][j][c];
					sum += sha[0][2] * a->px[i - 1][j + 1][c];
					sum += sha[1][0] * a->px[i][j - 1][c];
					sum += sha[1][1] * a->px[i][j][c];
					sum += sha[1][2] * a->px[i][j + 1][c];
					sum += sha[2][0] * a->px[i + 1][j - 1][c];
					sum += sha[2][1] * a->px[i + 1][j][c];
					sum += sha[2][2] * a->px[i + 1][j + 1][c];
					aux[i][j][c] = verif(sum);
				}
			}
		}
	}
	for (int i = m->x1; i < m->x2; i++) {
		for (int j = m->y1; j < m->y2; j++) {
			for (int c = 0; c < a->ci; c++) {
				a->px[i][j][c] = aux[i][j][c];
			}
		}
	}
	for (int i = 0; i < a->latime; i++) {
		for (int j = 0; j < a->inaltime; j++) {
			free(aux[i][j]);
		}
		free(aux[i]);
	}
	free(aux);
	printf("APPLY SHARPEN done\n");
}

// Function to apply a simple blur filter to an image or a selected region
void blur(im *a, ss *m)
{
	int blur[3][3] = {
		{1, 1, 1},
		{1, 1, 1},
		{1, 1, 1}
	};
	unsigned char ***aux =
	(unsigned char ***)malloc(a->latime * sizeof(unsigned char **));
	if (!aux) {
		return;
	}
	for (int i = 0; i < a->latime; i++) {
		aux[i] =
		(unsigned char **)malloc(a->inaltime * sizeof(unsigned char *));
		if (!aux[i]) {
			return;
		}
		for (int j = 0; j < a->inaltime; j++) {
			aux[i][j] =
			(unsigned char *)malloc(a->ci * sizeof(unsigned char));
			if (!aux[i][j]) {
				return;
			}
		}
	}
	for (int c = 0; c < a->ci; c++) {
		for (int i = m->x1; i < m->x2; i++) {
			for (int j = m->y1; j < m->y2; j++) {
				if (i == 0 || j == 0 || i == a->latime - 1 ||
					j == a->inaltime - 1) {
					aux[i][j][c] = a->px[i][j][c];
				} else {
					float sum = 0;
					sum += blur[0][0] * a->px[i - 1][j - 1][c];
					sum += blur[0][1] * a->px[i - 1][j][c];
					sum += blur[0][2] * a->px[i - 1][j + 1][c];
					sum += blur[1][0] * a->px[i][j - 1][c];
					sum += blur[1][1] * a->px[i][j][c];
					sum += blur[1][2] * a->px[i][j + 1][c];
					sum += blur[2][0] * a->px[i + 1][j - 1][c];
					sum += blur[2][1] * a->px[i + 1][j][c];
					sum += blur[2][2] * a->px[i + 1][j + 1][c];
					aux[i][j][c] = verif(round(sum / 9.0f));
				}
			}
		}
	}
	for (int i = m->x1; i < m->x2; i++) {
		for (int j = m->y1; j < m->y2; j++) {
			for (int c = 0; c < a->ci; c++) {
				a->px[i][j][c] = aux[i][j][c];
			}
		}
	}
	for (int i = 0; i < a->latime; i++) {
		for (int j = 0; j < a->inaltime; j++) {
			free(aux[i][j]);
		}
		free(aux[i]);
	}
	free(aux);
	printf("APPLY BLUR done\n");
}

// Function to apply a Gaussian blur filter to an image or a selected region
void gaussianblur(im *a, ss *m)
{
	int gauss[3][3] = {
		{1, 2, 1},
		{2, 4, 2},
		{1, 2, 1}};
	unsigned char ***aux =
	(unsigned char ***)malloc(a->latime * sizeof(unsigned char **));
	for (int i = 0; i < a->latime; i++) {
		aux[i] =
		(unsigned char **)malloc(a->inaltime * sizeof(unsigned char *));
		for (int j = 0; j < a->inaltime; j++) {
			aux[i][j] =
			(unsigned char *)malloc(a->ci * sizeof(unsigned char));
		}
	}
	for (int c = 0; c < a->ci; c++) {
		for (int i = m->x1; i < m->x2; i++) {
			for (int j = m->y1; j < m->y2; j++) {
				if (i == 0 || j == 0 || i == a->latime - 1 ||
					j == a->inaltime - 1) {
					aux[i][j][c] = a->px[i][j][c];
				} else {
					float sum = 0;
					sum += gauss[0][0] * a->px[i - 1][j - 1][c];
					sum += gauss[0][1] * a->px[i - 1][j][c];
					sum += gauss[0][2] * a->px[i - 1][j + 1][c];
					sum += gauss[1][0] * a->px[i][j - 1][c];
					sum += gauss[1][1] * a->px[i][j][c];
					sum += gauss[1][2] * a->px[i][j + 1][c];
					sum += gauss[2][0] * a->px[i + 1][j - 1][c];
					sum += gauss[2][1] * a->px[i + 1][j][c];
					sum += gauss[2][2] * a->px[i + 1][j + 1][c];
					aux[i][j][c] = verif(round(sum / 16.0f));
				}
			}
		}
	}
	for (int i = m->x1; i < m->x2; i++) {
		for (int j = m->y1; j < m->y2; j++) {
			for (int c = 0; c < a->ci; c++) {
				a->px[i][j][c] = aux[i][j][c];
			}
		}
	}
	for (int i = 0; i < a->latime; i++) {
		for (int j = 0; j < a->inaltime; j++) {
			free(aux[i][j]);
		}
		free(aux[i]);
	}
	free(aux);
	printf("APPLY GAUSSIAN_BLUR done\n");
}

// Function to apply a filter to an image (only for color images)
void apply(im *a, ss *m, char s[NMAX])
{
	if (a->ci != 3) {
		printf("Easy, Charlie Chaplin\n");
		return;
	}
	if (strcmp(s, "EDGE") == 0) {
		edge(a, m);
	} else if (strcmp(s, "SHARPEN") == 0) {
		sharpen(a, m);
	} else if (strcmp(s, "BLUR") == 0) {
		blur(a, m);
	} else if (strcmp(s, "GAUSSIAN_BLUR") == 0) {
		gaussianblur(a, m);
	} else {
		printf("APPLY parameter invalid\n");
	}
}

// Function to rotate an image or a selected region by 90 degrees counterclockwise
void rotatie(im *a, ss *m, bool ok)
{
	int w, l;
	if (ok || !m) {
		w = a->latime;
		l = a->inaltime;
	} else {
		w = m->x2 - m->x1;
		l = m->y2 - m->y1;
	}
	unsigned char ***aux = (unsigned char ***)malloc(l * sizeof(unsigned char **));
	for (int i = 0; i < l; i++) {
		aux[i] = (unsigned char **)malloc(w * sizeof(unsigned char *));
	}
	for (int i = 0; i < l; i++) {
		for (int j = 0; j < w; j++) {
			aux[i][j] = (unsigned char *)malloc(a->ci * sizeof(unsigned char));
		}
	}
	for (int i = 0; i < w; i++) {
		for (int j = 0; j < l; j++) {
			for (int k = 0; k < a->ci; k++) {
				aux[l - j - 1][i][k] = a->px[i + m->x1][j + m->y1][k];
			}
		}
	}
	if (ok) {
		for (int i = 0; i < a->latime; i++) {
			for (int j = 0; j < a->inaltime; j++) {
				free(a->px[i][j]);
			}
			free(a->px[i]);
		}
		free(a->px);
		a->inaltime = w;
		a->latime = l;
		a->px = aux;
	} else {
		for (int i = 0; i < w; i++) {
			for (int j = 0; j < l; j++) {
				for (int k = 0; k < a->ci; k++) {
					a->px[i + m->x1][j + m->y1][k] = aux[i][j][k];
				}
			}
		}
		for (int i = 0; i < w; i++) {
			for (int j = 0; j < l; j++) {
				free(aux[i][j]);
			}
			free(aux[i]);
		}
		free(aux);
		if (m->x1 == 0 && m->y1 == 0 && m->x2 == a->latime &&
			m->y2 == a->inaltime) {
			a->inaltime = w;
			a->latime = l;
		}
	}
}

// Function to rotate an image by a given angle (multiple of 90 degrees)
void rotire(im *a, ss *m, int u)
{
	bool ok = false;
	int w = m->x2 - m->x1, l = m->y2 - m->y1;
	if (!a) {
		printf("No image loaded\n");
		return;
	}
	if (u % 90 != 0 || u <= -361 || u >= 361) {
		printf("Unsupported rotation angle\n");
		return;
	}
	if (m->x1 == 0 && m->y1 == 0 &&
		m->x2 == a->latime && m->y2 == a->inaltime) {
		ok = true;
	}
	if (!m->ok) {
		ok = true;
	} else if (l != w && (m->x1 != 0 || m->y1 != 0 ||
			   m->x2 != a->latime || m->y2 != a->inaltime)) {
		printf("The selection must be square\n");
		return;
	}
	if (w == a->latime && l == a->inaltime) {
		ok = true;
	}
	if (u >= 0) {
		if (u == 0) {
			printf("Rotated 0\n");
		} else if (u == 90) {
			rotatie(a, m, ok);
			printf("Rotated 90\n");
		} else if (u == 180) {
			rotatie(a, m, ok);
			rotatie(a, m, ok);
			printf("Rotated 180\n");
		} else if (u == 270) {
			rotatie(a, m, ok);
			rotatie(a, m, ok);
			rotatie(a, m, ok);
			printf("Rotated 270\n");
		} else if (u == 360) {
			printf("Rotated 360\n");
		}
	} else {
		if (u == -90) {
			rotatie(a, m, ok);
			rotatie(a, m, ok);
			rotatie(a, m, ok);
			printf("Rotated -90\n");
		} else if (u == -180) {
			rotatie(a, m, ok);
			rotatie(a, m, ok);
			printf("Rotated -180\n");
		} else if (u == -270) {
			rotatie(a, m, ok);
			printf("Rotated -270\n");
		} else if (u == -360) {
			printf("Rotated -360\n");
		}
	}
}

// Function to free memory allocated for an image
void freeim(im *a)
{
	if (!a) {
		return;
	}
	for (int i = 0; i < a->latime; i++) {
		for (int j = 0; j < a->inaltime; j++) {
			free(a->px[i][j]);
		}
		free(a->px[i]);
	}
	free(a->px);
	free(a);
}

int main(void)
{
	im *a = NULL; ss selection;
	selection.ok = false;
	char nume[NMAX], buffer[256], s[NMAX]; int ok = 1, g = 1; g += 2;
	while (ok != 0) {
		fgets(buffer, 256, stdin);
		g = sscanf(buffer, "%s", s);
		if (strcmp(s, "LOAD") == 0) {
			if (a) {
				freeim(a);
			}
			sscanf(buffer, "LOAD %s", nume);
			selection.ok = false;
			a = load(nume);
		} else if (strcmp(s, "SAVE") == 0) {
			char format[6];
			int res = sscanf(buffer, "SAVE %s %s", nume, format);
			if (res == 2 && strcmp(format, "ascii") == 0) {
				save(a, nume, true);
			} else if (res == 1) {
				save(a, nume, false);
			}
		} else if (strcmp(s, "EXIT") == 0) {
			ok = 0;
			if (a) {
				freeim(a);
			} else {
				printf("No image loaded\n");
			}
		} else if (strcmp(s, "ROTATE") == 0) {
			int u;
			g = sscanf(buffer, "ROTATE %d", &u);
			rotire(a, &selection, u);
		} else if (strcmp(s, "APPLY") == 0) {
			if (!a) {
				printf("No image loaded\n");
			} else {
				char comanda[NMAX];
				int nr = sscanf(buffer, "APPLY %s", comanda);
				if (nr == 1) {
					apply(a, &selection, comanda);
				} else {
					printf("Invalid command\n");
				}
			}
		} else if (strcmp(s, "EQUALIZE") == 0) {
			equalize(a);
		} else if (strcmp(s, "HISTOGRAM") == 0) {
			if (!a) {
				printf("No image loaded\n");
			} else {
				int x, y, z, nr;
				nr = sscanf(buffer, "HISTOGRAM %d %d %d", &x, &y, &z);
				if (nr == 2) {
					histogram(a, x, y);
				} else {
					printf("Invalid command\n");
				}
			}
		} else if (strcmp(s, "CROP") == 0) {
			crop(a, &selection);
		} else if (strcmp(s, "SELECT") == 0) {
			char comanda[NMAX];
			g = sscanf(buffer, "SELECT %s", comanda);
			if (strcmp(comanda, "ALL") == 0) {
				sselectietotala(a, &selection);
			} else {
				int x1, y1, x2, y2, nr;
				nr = sscanf(buffer, "SELECT %d %d %d %d", &x1, &y1, &x2, &y2);
				if (nr == 4) {
					sselectie(a, &selection, x1, y1, x2, y2);
				} else {
					printf("Invalid command\n");
				}
			}
		} else {
			printf("Invalid command\n");
		}
	}
	return 0;
}
