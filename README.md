# Artista: learn to paint digitally, with feedback

Final-year project for the B.E. in Computer Engineering at Don Bosco College of
Engineering, Goa (2024), built by a team of five. Artista is a painting application for
beginners: it turns a reference image into a sketch outline and a colour palette, lets
the user repaint it on a grid canvas, and scores how close the result is to the reference.

This repository is my fork of the team's code. The application itself (Flask backend,
web interface, brushes, user accounts) was developed by my teammates.

## My part: the image analysis

I worked on the methods behind four features, and wrote the corresponding sections of
the project report.

**Sketch extraction.** A beginner should not have to draw the outline before painting.
The outline is extracted from the reference image: grayscale conversion, edge detection,
dilation of the edge map, inversion, then sharpening. I compared Laplacian, Sobel and
Canny edge detection on the same test images. Laplacian gave thick, noisy, broken
lines; Sobel was cleaner but cluttered; Canny gave clean continuous outlines and is
what the application uses.

**Colour palette extraction.** K-means clustering on the pixels of the reference image
returns its 12 most prominent colours, which become the user's palette.

**Image segmentation.** The same clustering assigns every pixel to its nearest palette
colour. The segmented image shows a beginner which regions get which colour.

**Error detection.** The goal was a similarity score between the user's painting and
the reference. The first idea was to segment the image and outline each region with
edge detection, then compare region by region. That did not work: Canny missed some
region boundaries, and Sobel drew boundaries around tiny colour changes. The final
method compares the two images cell by cell on the canvas grid. Colours are converted
to the LAB colour space and their difference is measured as Delta E, the Euclidean
distance between the two colours, which is then normalised to a percentage.

## Limitations

- The comparison treats every cell independently, so it ignores shapes and structure:
  a painting shifted by one cell scores badly although it looks right.
- It is slow on large images.
- The Delta E used is the simple Euclidean version; newer formulas such as CIEDE2000
  match human perception of colour difference more closely.

## Tools

Python, Flask, OpenCV, scikit-learn (K-means), NumPy, MySQL, HTML/CSS/JavaScript.
