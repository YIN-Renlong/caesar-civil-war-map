<script>
  import { onMount, onDestroy } from 'svelte';
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';
  import { historicalData } from './historical-data.js';
  import * as turf from '@turf/turf';

  let map;
  let provinceLayer; 
  let eventIndex = 0; 
  
  // --- PLAYBACK VARIABLES ---
  let isPlaying = false;
  let playInterval;
  const PLAY_SPEED_MS = 800; // How fast the timeline moves

  $: currentData = historicalData[eventIndex];

  // --- 1. FIXED COLORS (Capitalized to match your Data) ---
  const colors = {
    "Caesar": "#d90429", // Red
    "Senate": "#203a43", // Dark Blue
    "Contested": "#fca311", // Orange
    "Neutral": "transparent"
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
  const roughProvinces = {
    "type": "FeatureCollection",
    "features": [
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
      const outlineResponse = await fetch('https://raw.githubusercontent.com/sfsheath/roman-maps/refs/heads/master/roman_empire_bc_60_extent.geojson');
      const empireOutline = await outlineResponse.json();

      L.geoJSON(empireOutline, { 
        style: { color: '#000', weight: 2.5, fill: false, interactive: false } 
      }).addTo(map);

      const snaggedProvinces = calculateSnagging(roughProvinces, empireOutline);
      
      provinceLayer = L.geoJSON(snaggedProvinces, { style: getStyle }).addTo(map);

    } catch (error) {
      console.error("Failed to fetch map data:", error);
    }
  });

  // Cleanup timer when component is destroyed
  onDestroy(() => {
    if (playInterval) clearInterval(playInterval);
  });

  // --- 3. SNAGGING ALGORITHM (Fixed for Turf v7) ---
  function calculateSnagging(roughData, detailedData) {
    let snaggedFeatures = [];
    roughData.features.forEach(roughFeature => {
      detailedData.features.forEach(detailedFeature => {
        try {
          const intersection = turf.intersect(
            turf.featureCollection([
              turf.feature(roughFeature.geometry), 
              turf.feature(detailedFeature.geometry)
            ])
          );
          if (intersection) {
            intersection.properties = roughFeature.properties;
            snaggedFeatures.push(intersection);
          }
        } catch (e) {}
      });
    });
    return { type: "FeatureCollection", features: snaggedFeatures };
  }

  function getStyle(feature) {
    const simpleName = feature.properties.provname;
    const owner = currentData ? (currentData.factions[simpleName] || "Neutral") : "Neutral";

    return {
      fillColor: colors[owner],
      weight: 0, 
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

  // --- 4. PLAY BUTTON LOGIC ---
  function togglePlay() {
    if (isPlaying) {
      pause();
    } else {
      play();
    }
  }

  function play() {
    isPlaying = true;
    playInterval = setInterval(() => {
      if (eventIndex < historicalData.length - 1) {
        eventIndex++;
      } else {
        pause(); // Stop when we reach the end
      }
    }, PLAY_SPEED_MS);
  }

  function pause() {
    isPlaying = false;
    clearInterval(playInterval);
  }

  // Pause if user manually drags the slider
  function handleSliderInput() {
    pause();
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
      
      <!-- CONTROLS CONTAINER -->
      <div class="controls">
        <button class="play-btn" on:click={togglePlay}>
          {#if isPlaying}
            <span>&#10074;&#10074;</span> <!-- Pause Icon -->
          {:else}
            <span>&#9658;</span> <!-- Play Icon -->
          {/if}
        </button>

        <input 
          type="range" 
          min="0" 
          max={historicalData.length - 1} 
          step="1" 
          bind:value={eventIndex}
          on:input={handleSliderInput} 
        />
      </div>

      <div class="legend">
        <div class="item"><span style="background:{colors.Caesar}"></span> Caesar</div>
        <div class="item"><span style="background:{colors.Senate}"></span> Senate</div>
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
  
  /* NEW CONTROLS STYLING */
  .controls {
    display: flex;
    align-items: center;
    gap: 10px;
    margin: 15px 0;
  }
  .play-btn {
    background: #d90429;
    color: white;
    border: none;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    font-size: 1.2rem;
    cursor: pointer;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: background 0.2s;
  }
  .play-btn:hover { background: #b00321; }
  
  input[type=range] { flex-grow: 1; accent-color: #d90429; }
  
  .legend { display: flex; gap: 20px; margin-top: 15px; border-top: 1px solid #ddd; padding-top: 15px; }
  .item { display: flex; align-items: center; font-weight: bold; font-size: 0.9rem; }
  .item span { width: 15px; height: 15px; margin-right: 8px; border: 1px solid #555; }
  .credits { margin-top: 15px; font-size: 0.7rem; color: #777; }
</style>