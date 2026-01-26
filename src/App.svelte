<script>
  import { onMount } from 'svelte';
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';
  // --- IMPORT YOUR NEW CLEAN DATA ---
  import { historicalData } from './historical-data.js';

  let map;
  let geoJsonLayer;
  let eventIndex = 0; // Controls the slider based on array index

  // --- REACTIVE LOGIC ---
  $: currentData = historicalData[eventIndex];
  // Convert "YYYY-MM" to a Date object. We append "-15" to pick the middle of the month
  $: currentDate = new Date(currentData.date + "-15");

  const colors = {
    caesar: "#d90429",
    senate: "#203a43",
    Contested: "#fca311", // Orange for contested zones
    Neutral: "transparent"
  };

  const provinceNameMapper = {
    "GALLIA LUGDUNENSIS": "Gaul",
    "GALLIA NARBONENSIS": "Gaul",
    "GALLIA AQUITANIA": "Gaul",
    "GALLIA BELGICA": "Gaul",
    "GERMANIA INFERIOR": "Gaul",
    "GERMANIA SUPERIOR": "Gaul",
    "HISPANIA TARRACONENSIS": "Hispania", // Updated to match your JSON key
    "HISPANIA BAETICA": "Hispania",
    "LUSITANIA": "Hispania",
    "ITALIA": "Italia", // Updated to match your JSON key
    "SICILIA": "Italia",
    "SARDINIA ET CORSICA": "Italia",
    "AFRICA PROCONSULARIS": "Africa",
    "NUMIDIA": "Africa",
    "ACHAEA": "Greece",
    "MACEDONIA": "Greece",
    "CRETA ET CYRENAICA": "Greece",
    "ASIA": "Asia",
    "BITHYNIA ET PONTUS": "Asia",
    "GALATIA": "Asia",
    "LYCIA ET PAMPHYLIA": "Asia",
    "CILICIA": "Asia",
    "AEGYPTUS": "Aegyptus" // Added Egypt
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
      const [outlineResponse, provincesResponse] = await Promise.all([
        fetch('https://raw.githubusercontent.com/sfsheath/roman-maps/master/roman_empire_bc_60_extent.geojson'),
        fetch('https://raw.githubusercontent.com/sfsheath/roman-maps/master/roman_provinces_senate_ad_69.geojson')
      ]);

      const empireOutline = await outlineResponse.json();
      const highResProvinces = await provincesResponse.json();

      L.geoJSON(empireOutline, { style: { color: '#000', weight: 2.5, fill: false, interactive: false } }).addTo(map);
      
      geoJsonLayer = L.geoJSON(highResProvinces, { style: getStyle }).addTo(map);

    } catch (error) {
      console.error("Failed to fetch map data:", error);
    }
  });

  function getStyle(feature) {
    const officialName = feature.properties.provname;
    const simpleName = provinceNameMapper[officialName];
    // Safety check: default to Neutral/Transparent if data is missing
    const owner = currentData ? (currentData.factions[simpleName] || "Neutral") : "Neutral";

    return {
      fillColor: colors[owner] || 'transparent',
      weight: 1.5,
      color: 'white',
      fillOpacity: 0.65
    };
  }

  $: if (geoJsonLayer && currentData) {
    geoJsonLayer.setStyle(getStyle);
  }

  function formatDate(date) {
      if (!date || isNaN(date)) return "";
      const year = Math.abs(date.getFullYear());
      const month = date.toLocaleString('default', { month: 'long' });
      return `${month}, ${year} BC`;
  }
</script>

<main>
  <div class="layout">
    <div id="map-container"></div>
    <div class="ui-panel">
      <h1>CAESAR'S CIVIL WAR</h1>
      <div class="date-display">{formatDate(currentDate)}</div>
      <h2>{currentData.title}</h2>
      <p>{currentData.key_events}</p>
      
      <!-- SLIDER CONTROLS THE ARRAY INDEX -->
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