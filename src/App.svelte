<script>
  import { onMount } from 'svelte';
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';

  let year = -50;
  let map;
  let geoJsonLayer; // This will hold our province shapes

  // --- 1. HISTORICAL DATA ---
  const historicalData = [
    { year: -50, title: "50 BC: The Standoff", desc: "Caesar is in Gaul. The Senate (Pompey) controls Italy, Spain, and the East.", factions: { Gaul: "caesar", Spain: "senate", Italy: "senate", Greece: "senate", Asia: "senate", Africa: "senate" } },
    { year: -49, title: "49 BC: Crossing the Rubicon", desc: "Caesar invades Italy (Jan) and Spain (Aug). Pompey flees to Greece.", factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "senate", Asia: "senate", Africa: "senate" } },
    { year: -48, title: "48 BC: Pharsalus", desc: "Caesar defeats Pompey in Greece. Pompey is killed in Egypt.", factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "caesar", Asia: "senate", Africa: "senate" } },
    { year: -46, title: "46 BC: Thapsus", desc: "Caesar invades Africa, destroying the Senatorial army. Cato commits suicide.", factions: { Gaul: "caesar", Spain: "senate", Italy: "caesar", Greece: "caesar", Africa: "caesar" } },
    { year: -45, title: "45 BC: Munda", desc: "The final battle in Spain. Caesar defeats Pompey's sons. The Civil War ends.", factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "caesar", Africa: "caesar" } }
  ];

  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];

  const colors = {
    caesar: "#d90429",
    senate: "#203a43",
  };

  // --- 2. GEOJSON SHAPES FOR PROVINCES ---
  // In a final project, this would be loaded from an external file.
  // These coordinates are simplified but represent the correct geographic areas.
  const provinceGeoJSON = {
    "type": "FeatureCollection",
    "features": [
      { "type": "Feature", "properties": { "name": "Gaul" }, "geometry": { "type": "Polygon", "coordinates": [[[-4.6, 48.3], [1.7, 50.9], [7.5, 49.5], [7.0, 43.6], [3.1, 42.4], [-1.8, 43.3], [-4.6, 48.3]]] } },
      { "type": "Feature", "properties": { "name": "Spain" }, "geometry": { "type": "Polygon", "coordinates": [[[-9.3, 42.9], [-9.0, 36.9], [-2.2, 36.6], [3.2, 42.2], [-1.8, 43.3], [-9.3, 42.9]]] } },
      { "type": "Feature", "properties": { "name": "Italy" }, "geometry": { "type": "Polygon", "coordinates": [[[7.5, 43.8], [12.4, 45.5], [13.8, 45.6], [12.6, 42.5], [14.5, 40.8], [18.5, 40.1], [15.6, 37.9], [13.6, 41.2], [10.1, 43.0], [8.3, 44.2], [7.5, 43.8]]] } },
      { "type": "Feature", "properties": { "name": "Africa" }, "geometry": { "type": "Polygon", "coordinates": [[[9.5, 37.3], [11.1, 36.8], [15.3, 32.3], [10.0, 30.0], [0.0, 32.0], [0.0, 35.0], [9.5, 37.3]]] } },
      { "type": "Feature", "properties": { "name": "Greece" }, "geometry": { "type": "Polygon", "coordinates": [[[19.4, 42.2], [20.5, 39.0], [22.3, 36.4], [26.6, 41.2], [23.5, 41.5], [19.4, 42.2]]] } },
      { "type": "Feature", "properties": { "name": "Asia" }, "geometry": { "type": "Polygon", "coordinates": [[[26.1, 40.1], [27.3, 36.8], [35.4, 36.1],[36.0, 41.5], [29.0, 41.2], [26.1, 40.1]]] } }
    ]
  };

  // --- 3. MAP INITIALIZATION ---
  onMount(async () => {
    map = L.map('map-container', {
      center: [42, 15],
      zoom: 4.5,
      minZoom: 4,
      maxZoom: 7,
      zoomControl: false,
      attributionControl: false
    });

    // HIGH-DEFINITION BASE MAP - This provides the real geography
    L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Shaded_Relief/MapServer/tile/{z}/{y}/{x}', {
      attribution: 'Tiles &copy; Esri'
    }).addTo(map);

    // FETCH & DRAW THE 60 BC OUTLINE
    const response = await fetch('https://raw.githubusercontent.com/sfsheath/roman-maps/master/roman_empire_bc_60_extent.geojson');
    const empireOutline = await response.json();
    L.geoJSON(empireOutline, { style: { color: '#000', weight: 2, fill: false } }).addTo(map);

    // DRAW THE INTERACTIVE PROVINCES ON TOP
    geoJsonLayer = L.geoJSON(provinceGeoJSON, {
      style: getStyle,
    }).addTo(map);
  });

  // --- 4. REACTIVE UPDATE LOGIC ---
  function getStyle(feature) {
    const provinceName = feature.properties.name;
    const owner = currentData.factions[provinceName];
    return {
      fillColor: colors[owner] || 'transparent',
      weight: 1,
      color: 'white',
      fillOpacity: 0.65
    };
  }

  // When 'year' changes, this tells Leaflet to re-style the province layer
  $: if (geoJsonLayer && currentData) {
    geoJsonLayer.setStyle(getStyle);
  }
</script>

<main>
  <div class="layout">
    <div id="map-container"></div>
    <div class="ui-panel">
      <h1>CAESAR'S CIVIL WAR</h1>
      <div class="date-display">{Math.abs(year)} BC</div>
      <h2>{currentData.title}</h2>
      <p>{currentData.desc}</p>
      <input type="range" min="-50" max="-45" step="1" bind:value={year} />
      <div class="legend">
        <div class="item"><span style="background:{colors.caesar}"></span> Caesar</div>
        <div class="item"><span style="background:{colors.senate}"></span> Senate</div>
      </div>
      <div class="credits">
        Base Map: Esri World Relief <br/>
        Republic Data: sfsheath/roman-maps (GitHub)
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
    background: rgba(255, 255, 255, 0.9);
    padding: 20px;
    border-radius: 4px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.4);
    z-index: 1000;
    border-top: 4px solid #d90429;
  }
  h1 { margin: 0; font-size: 1.3rem; color: #333; text-transform: uppercase; }
  .date-display { font-size: 3.5rem; font-weight: bold; color: #d90429; margin: 5px 0; }
  h2 { margin: 0 0 10px 0; color: #1d3557; font-size: 1.2rem; }
  p { line-height: 1.5; font-size: 1rem; color: #444; }
  input[type=range] { width: 100%; margin: 15px 0; accent-color: #d90429; }
  .legend { display: flex; gap: 20px; margin-top: 15px; border-top: 1px solid #ddd; padding-top: 15px; }
  .item { display: flex; align-items: center; font-weight: bold; font-size: 0.9rem; }
  .item span { width: 15px; height: 15px; margin-right: 8px; border: 1px solid #555; }
  .credits { margin-top: 15px; font-size: 0.7rem; color: #777; }
</style>
