Shape Detector Challenge Solution
This repository contains my from-scratch solution to the Shape Detector Challenge. The goal was to implement a complete computer vision pipeline in TypeScript to identify and classify geometric shapes (circles, triangles, rectangles, pentagons, and stars) from a raw ImageData object, without using any external computer vision libraries like OpenCV.

This implementation successfully handles simple shapes, mixed scenes, and complex scenarios like noise and anti-aliasing artifacts.

My Algorithmic Approach
The core of this solution is a 4-phase pipeline implemented in src/main.ts.

1. Pre-processing
The 4-channel RGBA ImageData is converted into a clean, 1-channel binary image.

Grayscale: Converts the image using a standard luminance formula.

Blur: Applies a 3x3 Box Blur to smooth pixel noise and merge anti-aliased edges.

Binarize: Uses a fixed 127 threshold to create a pure black-and-white image.

2. Contour Detection
A Moore-Neighbor Tracing algorithm scans the binary image, pixel by pixel. When it finds the start of a new shape (a white pixel), it "walks" around its entire perimeter, recording the coordinates of every boundary pixel. This produces a list of contours.

3. Feature Extraction
Each raw contour (which can contain thousands of points) is analyzed:

Ramer-Douglas-Peucker (RDP) Simplification: The contour is simplified into its essential vertices. A small, fixed epsilon (e.g., 3.5) is used. This correctly reduces a square to 4 vertices and a triangle to 3, but (critically) fails to simplify a circle, leaving it with many vertices.

Feature Calculation: The area (Shoelace formula), perimeter, and boundingBox are calculated for each contour.

4. Classification & Filtering
A shape is identified using its simplified features:

Noise Filtering: Any contour with a tiny area (< 100px) or perimeter (< 50px) is discarded as noise.

Circle Check: If numVertices > 8 (meaning RDP failed to simplify it) and it has high circularity (> 0.8), it is classified as a Circle.

Polygon Check: If it's not a circle, it's checked by its vertex count (numVertices).

Convexity Check: A 5-vertex shape is only classified as a Pentagon if it passes an isConvex check. This prevents 5-point, non-convex noise from being misclassified.

Concentric Filter: Finally, a filterConcentricShapes function removes "holes" (like the inner ring of a blurry circle) by keeping only the largest shape at any given center point.

Setup and Usage
These are the original instructions to run the project.

Prerequisites
Node.js (v16+)

npm or yarn

Installation
Bash

# Install dependencies
npm install
Running the Development Server
Bash

# Start development server
npm run dev
The application will be available at http://localhost:5173/.
