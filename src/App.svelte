<script>
  import { onMount } from 'svelte';
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';
  import { historicalData } from './historical-data.js';
  
  // --- 1. IMPORT TURF (THE MATH LIBRARY) ---
  import * as turf from '@turf/turf';

  let map;
  let provinceLayer; 
  let eventIndex = 0; 

  $: currentData = historicalData[eventIndex];

  const colors = {
    caesar: "#d90429", 
    senate: "#203a43", 
    Contested: "#fca311", 
    Neutral: "transparent"
  };

  const provinceNameMapper = {
    "Gaul": "Gaul",
    "Hispania": "Hispania",
    "Italia": "Italia",
    "Africa": "Africa",
    "Greece": "Greece",
    "Asia": "Asia",
    "Aegyptus": "Aegyptus"
  };

  // --- 2. ROUGH LOCAL SHAPES ---
  // Notice: These are blocky and simple. We rely on the algorithm 
  // to "snag" them to the beautiful coastline.
  const roughProvinces = {
    "type": "FeatureCollection",
    "features": [
      // I intentionally made these extend into the ocean to prove the algorithm works
      { "type": "Feature", "properties": { "provname": "Gaul" }, "geometry": { "type": "Polygon", "coordinates": [[[-6.0, 42.0], [9.0, 42.0], [9.0, 52.0], [-6.0, 52.0], [-6.0, 42.0]]] } },
      { "type": "Feature", "properties": { "provname": "Hispania" }, "geometry": { "type": "Polygon", "coordinates": [[[-12.0, 35.0], [4.0, 35.0], [4.0, 44.0], [-12.0, 44.0], [-12.0, 35.0]]] } },
      { "type": "Feature", "properties": { "provname": "Italia" }, "geometry": { "type": "Polygon", "coordinates": [[[6.0, 36.0], [19.0, 36.0], [19.0, 47.0], [6.0, 47.0], [6.0, 36.0]]] } },
      { "type": "Feature", "properties": { "provname": "Africa" }, "geometry": { "type": "Polygon", "coordinates": [[[-1.0, 28.0], [16.0, 28.0], [16.0, 38.0], [-1.0, 38.0], [-1.0, 28.0]]] } },
      { "type": "Feature", "properties": { "provname": "Greece" }, "geometry": { "type": "Polygon", "coordinates": [[[19.0, 34.0], [28.0, 34.0], [28.0, 43.0], [19.0, 43.0], [19.0, 34.0]]] } },
      { "type": "Feature", "properties": { "provname": "Asia" }, "geometry": { "type": "Polygon", "coordinates": [[[26.0, 35.0], [38.0, 35.0], [38.0, 42.0], [26.0, 42.0], [26.0, 35.0]]] } },
      { "type": "Feature", "properties": { "provname": "Aegyptus" }, "geometry": { "type": "Polygon", "coordinates": [[[28.0, 29.0], [35.0, 29.0], [35.0, 32.0], [28.0, 32.0], [28.0, 29.0]]] } }
    ]
  };

  onMount(async () => {
    map = L.map('map-container', {
      center: [42, 15],
      zoom: 4.5,
      minZoom: 4,
      maxZoom: 7,
      zoomControl: false,
      attributionControl: false
    });

    L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Shaded_Relief/MapServer/tile/{z}/{y}/{x}', {
      attribution: 'Tiles &copy; Esri'
    }).addTo(map);
    
    try {
      // Fetch the Detailed Outline
      const outlineResponse = await fetch('https://raw.githubusercontent.com/sfsheath/roman-maps/refs/heads/master/roman_empire_bc_60_extent.geojson');
      const empireOutline = await outlineResponse.json();

      // Draw the black outline
      L.geoJSON(empireOutline, { 
        style: { color: '#000', weight: 2.5, fill: false, interactive: false } 
      }).addTo(map);

      // --- 3. THE "SNAGGING" ALGORITHM ---
      // We calculate the intersection of our Rough Shapes + Detailed Outline
      const snaggedProvinces = calculateSnagging(roughProvinces, empireOutline);
      
      // Draw the RESULT of the calculation
      provinceLayer = L.geoJSON(snaggedProvinces, { style: getStyle }).addTo(map);

    } catch (error) {
      console.error("Failed to fetch map data:", error);
    }
  });

  // --- THE ALGORITHM FUNCTION ---
  function calculateSnagging(roughData, detailedData) {
    let snaggedFeatures = [];

    // Loop through every Rough Province (Square)
    roughData.features.forEach(roughFeature => {
      
      // Loop through every piece of the Detailed Empire (Islands, Mainland)
      detailedData.features.forEach(detailedFeature => {
        
        // TURF.JS MAGIC: 
        // "Find the area that exists inside BOTH the Rough Square AND the Detailed Map"
        const intersection = turf.intersect(
          turf.feature(roughFeature.geometry), 
          turf.feature(detailedFeature.geometry)
        );

        // If they touch, we get a result!
        if (intersection) {
          // Copy the name (e.g., "Gaul") to the new detailed shape
          intersection.properties = roughFeature.properties;
          snaggedFeatures.push(intersection);
        }
      });
    });

    return { type: "FeatureCollection", features: snaggedFeatures };
  }

  function getStyle(feature) {
    const simpleName = feature.properties.provname;
    const owner = currentData ? (currentData.factions[simpleName] || "Neutral") : "Neutral";

    return {
      fillColor: colors[owner],
      weight: 1,       
      color: 'white',  
      fillOpacity: 0.6
    };
  }

  $: if (provinceLayer && currentData) {
    provinceLayer.setStyle(getStyle);
  }

  function formatDate(dateString) {
      if (!dateString) return "";
      const parts = dateString.split('-'); 
      const year = parseInt(parts[1]);     
      const monthNum = parseInt(parts[2]); 
      
      const dateObj = new Date();
      dateObj.setMonth(monthNum - 1);
      const monthName = dateObj.toLocaleString('default', { month: 'long' });

      return `${monthName}, ${year} BC`;
  }
</script>

<main>
  <div class="layout">
    <div id="map-container"></div>
    <div class="ui-panel">
      <h1>CAESAR'S CIVIL WAR</h1>
      <div class="date-display">{formatDate(currentData.date)}</div>
      <h2>{currentData.title}</h2>
      <p>{currentData.key_events}</p>
      
      <input 
        type="range" 
        min="0" 
        max={historicalData.length - 1} 
        step="1" 
        bind:value={eventIndex} 
      />

      <div class="legend">
        <div class="item"><span style="background:{colors.caesar}"></span> Caesar</div>
        <div class="item"><span style="background:{colors.senate}"></span> Senate</div>
        <div class="item"><span style="background:{colors.Contested}"></span> Contested</div>
      </div>
      <div class="credits">
        Base Map: Esri World Relief <br/>
        Republic Data: sfsheath/roman-maps (GitHub) <br/>
        Historical Text: "Caesar's Civil War", R. Westall
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: 'Georgia', serif; }
  .layout { position: relative; height: 100vh; width: 100vw; }
  #map-container { height: 100%; width: 100%; z-index: 1; }
  .ui-panel {
    position: absolute;
    top: 20px;
    right: 20px;
    width: 380px;
    background: rgba(255, 255, 255, 0.95);
    padding: 20px;
    border-radius: 4px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.4);
    z-index: 1000;
    border-top: 4px solid #d90429;
  }
  h1 { margin: 0; font-size: 1.3rem; color: #333; text-transform: uppercase; }
  .date-display { font-size: 2.5rem; font-weight: bold; color: #d90429; margin: 5px 0; }
  h2 { margin: 0 0 10px 0; color: #1d3557; font-size: 1.2rem; }
  p { line-height: 1.5; font-size: 1rem; color: #444; }
  input[type=range] { width: 100%; margin: 15px 0; accent-color: #d90429; }
  .legend { display: flex; gap: 20px; margin-top: 15px; border-top: 1px solid #ddd; padding-top: 15px; }
  .item { display: flex; align-items: center; font-weight: bold; font-size: 0.9rem; }
  .item span { width: 15px; height: 15px; margin-right: 8px; border: 1px solid #555; }
  .credits { margin-top: 15px; font-size: 0.7rem; color: #777; }
</style>