# Spatiotemporal Visualization of Caesar's Civil War (49–45 BCE)

![main](img/main.png)

![Status](https://img.shields.io/badge/Status-Prototype%20v0.5-orange)
![License](https://img.shields.io/badge/License-MIT-blue)
![Tech](https://img.shields.io/badge/Built%20With-Svelte%20%7C%20Leaflet%20%7C%20Turf.js-green)

**Author:** YIN Renlong  
**Department:** Faculty of Engineering Science, KU Leuven  
**Context:** Digital Humanities Demonstrator  
**Primary Source:** Westall, R. W. (2017). *Caesar's Civil War: Historical Reality and Fabrication*. Brill.

**Live demo:** https://yin-renlong.github.io/caesar-civil-war-map/

---

## 1. Abstract

This project represents a spatiotemporal interface designed to visualize the geopolitical dynamics of the Great Roman Civil War (49–45 BCE). Unlike traditional static cartography, which often fails to capture the fluid nature of civil conflict, this application utilizes a reactive, event-driven timeline to render the rapid fluctuation of territorial control between Caesarian and Senatorial factions.

The application serves as a technical demonstrator for the **Digital Humanities**, bridging the gap between classical historiography and modern client-side geospatial engineering. It specifically addresses the challenge of visualizing historical data where precise vector shapefiles are unavailable, utilizing a novel algorithmic clipping approach.

## 2. Technical Architecture

### 2.1 The Technology Stack
The application is built as a Single Page Application (SPA) optimized for performance and reactivity.

*   **Frontend Framework:** [Svelte](https://svelte.dev/) (via Vite) - Chosen for its compile-time optimization and reactive DOM updates, essential for smooth timeline scrubbing.
*   **Mapping Engine:** [Leaflet.js](https://leafletjs.com/) - Used for rendering the tile layer and handling vector overlays.
*   **Geospatial Analysis:** [Turf.js](https://turfjs.org/) - A modular geospatial engine used for client-side boolean operations (see Section 3).

### 2.2 Data Model: Event-Driven Topology
To maximize efficiency, the historical narrative is not stored as a continuous time-series but as a sparse **Event-Driven Dataset**.
*   **Structure:** The timeline is an array of state objects.
*   **Optimization:** Data points are only generated for months where significant military or political shifts occur (e.g., the Crossing of the Rubicon, the Battle of Pharsalus).
*   **Interpolation:** The application logic retains the state of the previous event until a new keyframe is reached, reducing data redundancy by approximately 85%.

## 3. Methodology: Algorithmic Geospatial Clipping

A primary challenge in ancient historical mapping is the scarcity of high-precision shapefiles for specific temporal administrative divisions (e.g., the exact border between *Gallia Cisalpina* and *Italia* in 50 BCE).

To circumvent the need for manual digitization of rough historical atlases, this project implements a client-side **Boolean Intersection Algorithm** (referred to internally as the "Snagging Algorithm").

### The Algorithm Process
1.  **The Masking Layer (Input A):** The application fetches a high-precision, verified GeoJSON vector of the Roman Republic's outer extent in 60 BCE (Source: *sfsheath/roman-maps*). This acts as the "truth" layer for coastlines and frontiers.
2.  **The Thematic Layer (Input B):** The application defines "Rough Polygons"—simplified geometric centroids representing the approximate area of a province. These polygons are intentionally over-extended beyond coastlines to ensure coverage.
3.  **Real-Time Intersection:** Upon initialization, the application employs `Turf.js` to calculate the geometric intersection ($A \cap B$).
    *   `Intersection = turf.intersect(RoughPolygon, EmpireOutline)`
4.  **Result:** The operation dynamically generates a new `FeatureCollection` where the rough provincial shapes are perfectly "snagged" or clipped to the detailed coastline of the Empire.

**Benefit:** This allows for the rapid prototyping of internal borders while maintaining a professional, high-resolution aesthetic at the coastlines and outer frontiers.

## 4. Current Status & Roadmap

**Current Version:** Prototype v0.5

### Known Limitations
*   **Internal Boundaries:** While the coastlines are highly accurate due to the clipping algorithm, the internal borders between provinces (e.g., Gaul/Spain) are currently defined by the edges of the rough input polygons. They represent algorithmic approximations rather than precise historical survey lines.
*   **Granularity:** The current model visualizes macro-provincial control. City-level control is not yet implemented.

### Future Development
1.  **Vectorization:** Replacement of "Rough Polygons" with precise, vectorized historical boundaries for internal divisions.
2.  **Granularity Increase:** Implementation of point-data for individual cities (e.g., Massilia, Corfinium) to visualize sieges distinct from provincial control.

## 5. Installation & Usage

To run this project locally for development or review:

```bash
# 1. Clone the repository
git clone [repository-url]

# 2. Install dependencies
npm install

# 3. Start the development server
npm run dev
```

## 6. Credits & Bibliography

- **Primary Historical Source:** Westall, R. (2017). *Caesar's Civil War: Historical Reality and Fabrication*. Leiden: Brill.
- **Geospatial Data Repository:** Heath, S. (ISAW/NYU). *Roman Maps*. GitHub. Available: [https://github.com/sfsheath/roman-maps](https://github.com/sfsheath/roman-map)
- **Basemap Provider:** Esri World Shaded Relief.

## 7. Development Context & Tasks

*This section provides technical context for contributors with future iterations.*

### 7.1 Current Logic State

- **Logic:** The turf.intersect logic is fully functional.
- **Data Source A (Mask):** roman_empire_bc_60_extent.geojson (Working).
- **Data Source B (Rough Input):** roughProvinces object inside App.svelte (Needs refinement).

### 7.2 Priority Task: Coordinate Refinement

The current roughProvinces polygons are defined as simple rectangles. This causes historical inaccuracies (e.g., the border between Gaul and Italy is a straight vertical line rather than following the Alps).

**Required Action:**
Without altering the "Snagging" logic, the coordinates in the roughProvinces object need to be redefined to approximate the historical borders more closely. The intersection algorithm will handle the coastlines, so the focus should be on the **internal borders**:

- **Gaul:** Define a polygon that stops roughly at the Pyrenees (West), the Alps (East), and the Rhine (North).
- **Hispania:** Define a polygon covering the Iberian peninsula, meeting Gaul at the Pyrenees.
- **Italia:** Define a polygon that includes Sicily and Sardinia but stops at the Alps (Cisalpine Gaul border).
- **Greece/Macedonia:** Define a polygon covering the Greek peninsula and Macedonia, distinct from Asia.
- **Asia:** Define a polygon covering modern Turkey (Asia Minor).
- **Africa:** Define a polygon covering the North African coast (modern Tunisia/Libya coast).

*Refining these coordinates will immediately improve the visual accuracy of the resulting clipped map.*
