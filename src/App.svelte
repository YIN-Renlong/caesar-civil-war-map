<script>
  import { onMount } from 'svelte';
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';

  let year = -50;
  let map;
  let geoJsonLayer;

  // --- 1. HISTORICAL DATA (The Logic) ---
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Standoff",
      desc: "Caesar is in Gaul. The Senate (Pompey) controls Italy, Spain, and the East. Egypt is neutral.",
      factions: { Gaul: "caesar", Spain: "senate", Italy: "senate", Greece: "senate", Asia: "senate", Africa: "senate", Egypt: "neutral" }
    },
    {
      year: -49,
      title: "49 BC: Rubicon & Ilerda",
      desc: "Caesar crosses the Rubicon (Jan) taking Italy. He marches to Spain (Aug) and defeats Pompey's legates.",
      factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "senate", Asia: "senate", Africa: "senate", Egypt: "neutral" }
    },
    {
      year: -48,
      title: "48 BC: Pharsalus",
      desc: "Caesar defeats Pompey in Greece (Pharsalus). Pompey flees to Egypt and is assassinated.",
      factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "caesar", Asia: "senate", Africa: "senate", Egypt: "contest" }
    },
    {
      year: -47,
      title: "47 BC: The East",
      desc: "Caesar settles Egypt (Cleopatra). He defeats Pharnaces in Asia Minor ('Veni Vidi Vici').",
      factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "caesar", Asia: "caesar", Africa: "senate", Egypt: "client" }
    },
    {
      year: -46,
      title: "46 BC: Thapsus",
      desc: "Caesar invades Africa. The Senate's army is destroyed. Cato commits suicide.",
      factions: { Gaul: "caesar", Spain: "senate", Italy: "caesar", Greece: "caesar", Asia: "caesar", Africa: "caesar", Egypt: "client" }
    },
    {
      year: -45,
      title: "45 BC: Munda",
      desc: "Caesar defeats the sons of Pompey in Spain. The war ends.",
      factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "caesar", Asia: "caesar", Africa: "caesar", Egypt: "client" }
    },
    {
      year: -44,
      title: "44 BC: Assassination",
      desc: "Caesar is Dictator Perpetuo. He is assassinated on the Ides of March.",
      factions: { Gaul: "caesar", Spain: "caesar", Italy: "neutral", Greece: "neutral", Asia: "neutral", Africa: "caesar", Egypt: "client" }
    }
  ];

  // --- 2. COLORS ---
  const colors = {
    caesar: "#d90429",  // Red
    senate: "#1e3a8a",  // Blue
    neutral: "transparent", // Clear
    contest: "#f59e0b", // Orange
    client:  "#7e22ce"  // Purple
  };

  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];

  // --- 3. GEOGRAPHIC SHAPES (Simplified GeoJSON for Demo) ---
  // In a real project, you would fetch this from a file called 'roman_provinces.json'
  const provinceShapes = {
    "type": "FeatureCollection",
    "features": [
      { "type": "Feature", "properties": { "name": "Italy" }, "geometry": { "type": "Polygon", "coordinates": [[[7.5,43.8],[12.4,45.5],[13.8,45.6],[12.6,42.5],[14.5,40.8],[18.5,40.1],[15.6,37.9],[13.6,41.2],[10.1,43.0],[8.3,44.2],[7.5,43.8]]] } },
      { "type": "Feature", "properties": { "name": "Gaul" }, "geometry": { "type": "Polygon", "coordinates": [[[-1.8,43.3],[-4.6,48.3],[1.7,50.9],[5.8,51.8],[7.5,49.5],[7.0,43.6],[3.1,42.4],[-1.8,43.3]]] } },
      { "type": "Feature", "properties": { "name": "Spain" }, "geometry": { "type": "Polygon", "coordinates": [[[-9.3,42.9],[-9.0,36.9],[-5.4,36.0],[-2.2,36.6],[0.4,38.7],[3.2,42.2],[-1.8,43.3],[-9.3,42.9]]] } },
      { "type": "Feature", "properties": { "name": "Africa" }, "geometry": { "type": "Polygon", "coordinates": [[[9.5,37.3],[11.1,36.8],[15.3,32.3],[10.0,30.0],[0.0,32.0],[0.0,35.0],[9.5,37.3]]] } },
      { "type": "Feature", "properties": { "name": "Greece" }, "geometry": { "type": "Polygon", "coordinates": [[[19.4,42.2],[20.5,39.0],[22.3,36.4],[24.0,36.7],[26.6,41.2],[23.5,41.5],[19.4,42.2]]] } },
      { "type": "Feature", "properties": { "name": "Asia" }, "geometry": { "type": "Polygon", "coordinates": [[[26.1,40.1],[27.3,36.8],[35.4,36.1],[36.0,41.5],[29.0,41.2],[26.1,40.1]]] } },
      { "type": "Feature", "properties": { "name": "Egypt" }, "geometry": { "type": "Polygon", "coordinates": [[[25.0,31.5],[29.8,31.2],[34.2,31.2],[34.8,27.0],[25.0,27.0],[25.0,31.5]]] } }
    ]
  };

  // --- 4. MAP INITIALIZATION ---
  onMount(() => {
    // A. Create Map
    map = L.map('map-container', {
      center: [41.9, 12.5], // Center on Rome
      zoom: 4,
      minZoom: 3,
      zoomControl: false // Hide zoom buttons for cleaner look
    });

    // B. Add Base Tile Layer (The Real Map)
    // We use CartoDB Voyager for a clean, non-distracting background
    L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager_nolabels/{z}/{x}/{y}{r}.png', {
      attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
      subdomains: 'abcd',
      maxZoom: 19
    }).addTo(map);

    // C. Add GeoJSON Layer (The Provinces)
    geoJsonLayer = L.geoJSON(provinceShapes, {
      style: getStyle,
      onEachFeature: onEachFeature
    }).addTo(map);
  });

  // --- 5. REACTIVITY ---
  // When 'year' changes, update the map styles
  $: if (geoJsonLayer && year) {
    geoJsonLayer.setStyle(getStyle);
  }

  // Helper to determine color
  function getStyle(feature) {
    const provinceName = feature.properties.name;
    const owner = currentData.factions[provinceName];
    const color = colors[owner] || "#cccccc";
    
    // Transparent if neutral to show the base map, otherwise colored opacity
    return {
      fillColor: color,
      weight: 2,
      opacity: 1,
      color: 'white',
      dashArray: '3',
      fillOpacity: owner === 'neutral' ? 0.1 : 0.6
    };
  }

  // Add interactions (Popups)
  function onEachFeature(feature, layer) {
    layer.on('click', () => {
      const prov = feature.properties.name;
      const owner = currentData.factions[prov];
      layer.bindPopup(`<strong>${prov}</strong><br>Controlled by: ${owner.toUpperCase()}`).openPopup();
    });
  }

</script>

<main>
  <div class="layout">
    <!-- MAP -->
    <div id="map-container"></div>

    <!-- UI CONTROLS OVERLAY -->
    <div class="overlay-container">
      <div class="header">
        <h1>CAESAR'S CIVIL WAR</h1>
      </div>

      <div class="info-box">
        <h2 class="year">{Math.abs(year)} BC</h2>
        <h3>{currentData.title}</h3>
        <p>{currentData.desc}</p>
        
        <input 
          type="range" 
          min="-50" 
          max="-44" 
          step="1" 
          bind:value={year} 
        />
        
        <div class="legend">
          <div><span style="background:{colors.caesar}"></span> Caesar</div>
          <div><span style="background:{colors.senate}"></span> Senate</div>
          <div><span style="background:{colors.contest}"></span> Contested</div>
          <div><span style="background:{colors.client}"></span> Client State</div>
        </div>
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: 'Helvetica', sans-serif; }
  
  .layout {
    position: relative;
    height: 100vh;
    width: 100vw;
  }

  #map-container {
    height: 100%;
    width: 100%;
    z-index: 1;
    background: #a5f3fc; /* Fallback color */
  }

  /* Floating UI */
  .overlay-container {
    position: absolute;
    top: 20px;
    left: 20px;
    z-index: 1000; /* Above Leaflet */
    width: 350px;
    pointer-events: none; /* Let clicks pass through empty areas */
  }

  .header {
    background: #991b1b;
    color: white;
    padding: 15px;
    border-radius: 8px;
    margin-bottom: 10px;
    box-shadow: 0 4px 6px rgba(0,0,0,0.3);
    pointer-events: auto;
  }
  .header h1 { margin: 0; font-size: 1.5rem; letter-spacing: 2px; }

  .info-box {
    background: rgba(255, 255, 255, 0.95);
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0,0,0,0.3);
    pointer-events: auto;
  }

  .year { color: #991b1b; font-size: 2.5rem; margin: 0; }
  h3 { margin: 5px 0 15px 0; color: #333; }
  p { line-height: 1.5; color: #444; }

  input[type=range] {
    width: 100%;
    margin-top: 20px;
    cursor: pointer;
    accent-color: #991b1b;
  }

  .legend {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    margin-top: 20px;
    font-size: 0.9rem;
    font-weight: bold;
    color: #555;
  }
  .legend span {
    display: inline-block;
    width: 15px;
    height: 15px;
    margin-right: 5px;
    border: 1px solid #ccc;
  }
</style>
