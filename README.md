# Spatiotemporal Visualization of Caesar's Civil War (49–45 BCE)

**Author:** YIN Renlong  
**Affiliation:** Faculty of Engineering Science, KU Leuven  
**Context:** A Digital Humanities demonstrator based on the text *Caesar's Civil War: Historical Reality and Fabrication* by Prof. Richard W. Westall.

## Project Overview
This web application provides an interactive, data-driven visualization of the geopolitical shifts during the Great Roman Civil War. Unlike static historical maps, this tool utilizes a continuous temporal slider to visualize the rapid fluctuation of territorial control between Caesarian and Senatorial factions on a month-by-month basis.

The project bridges the gap between traditional historiography and modern geospatial web technologies, offering a dynamic narrative tool for students and researchers.

## Technical Methodology

### 1. The Stack
*   **Framework:** Svelte (Vite) for reactive UI and state management.
*   **Mapping Engine:** Leaflet.js with Esri World Shaded Relief basemaps.
*   **Geospatial Processing:** Turf.js (Geospatial analysis engine).

### 2. The "Snagging" Algorithm (Geospatial Clipping)
To allow for rapid prototyping of provincial control without manual digitization of historical atlases, this project employs a novel client-side clipping algorithm. 
*   **Input 1:** A high-precision GeoJSON vector of the Roman Empire's outer extent in 60 BCE (Source: *sfsheath/roman-maps*).
*   **Input 2:** Approximate "Rough Polygons" defining the general centroids of major provinces (Gaul, Hispania, Italia, etc.).
*   **Process:** The application uses `Turf.js` to calculate the boolean intersection of the rough inputs and the detailed empire outline in real-time. 
*   **Result:** This generates visually coherent provincial borders that adhere perfectly to the detailed coastlines and outer frontiers of the Republic, ensuring a professional aesthetic even with simplified input data.

### 3. Event-Driven Data Structure
The historical timeline is powered by a custom JSON dataset derived from Prof. Westall's text. The data is optimized into an "Event-Driven" model, storing state changes only when significant military or political shifts occur, ensuring high performance and narrative clarity.

## Current Status & Limitations
*   **Status:** Functional Prototype (v0.9).
*   **Data Accuracy:** The temporal event data is structurally complete.
*   **Geospatial Accuracy:** The internal borders between provinces (e.g., the exact line between Cisalpine Gaul and Italy) are currently algorithmic approximations. Future work will involve vectorizing precise historical boundaries to replace the current rough input polygons.

## Credits & Sources
*   **Historical Text:** Westall, R. Westall (2017). *Caesar's Civil War: Historical Reality and Fabrication*. Brill.
*   **Base Geographic Data:** Sebastian Heath (ISAW/NYU), *roman-maps* repository.
*   **Basemaps:** Esri, USGS, NOAA.

---
*Deployed via GitHub Pages.*
