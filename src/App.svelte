<script>
  import { onMount } from 'svelte';
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';

  let year = -50;
  let map;
  let geoJsonLayer; // This will hold our province shapes

  // --- 1. HISTORICAL DATA (Unchanged) ---
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

  // --- 2. GEOJSON SHAPES FOR PROVINCES (Completely Replaced) ---
  
  // --- REMOVED --- The old low-resolution `provinceGeoJSON` object is gone.
  
  // +++ ADDED +++
  // This mapper connects the official province names from the GeoJSON file
  // to the simplified names we use in our historicalData.
  const provinceNameMapper = {
    "GALLIA LUGDUNENSIS": "Gaul",
    "GALLIA NARBONENSIS": "Gaul",
    "GALLIA AQUITANIA": "Gaul",
    "GALLIA BELGICA": "Gaul",
    "GERMANIA INFERIOR": "Gaul", // Historically part of Gaul at the time
    "GERMANIA SUPERIOR": "Gaul",
    "HISPANIA TARRACONENSIS": "Spain",
    "HISPANIA BAETICA": "Spain",
    "LUSITANIA": "Spain",
    "ITALIA": "Italy",
    "SICILIA": "Italy", // Grouping Sicily with Italy
    "SARDINIA ET CORSICA": "Italy", // Grouping Sardinia/Corsica with Italy
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
    // Note: Other provinces like AEGYPTUS, SYRIA etc. exist in the file
    // but are not in our simplified model. They will render as transparent.
  };


  // --- 3. MAP INITIALIZATION (Modified) ---
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
    
    // FETCH & DRAW THE 60 BC OUTLINE (Unchanged)
    const outlineResponse = await fetch('https://raw.githubusercontent.com/sfsheath/roman-maps/master/roman_empire_bc_60_extent.geojson');
    const empireOutline = await outlineResponse.json();
    L.geoJSON(empireOutline, { style: { color: '#000', weight: 2, fill: false, interactive: false } }).addTo(map);

    // +++ ADDED +++
    // FETCH HIGH-RESOLUTION PROVINCE DATA
    const provincesResponse = await fetch('https://raw.githubusercontent.com/sfsheath/roman-maps/master/roman_provinces_senate_ad_69.geojson');
    const highResProvinces = await provincesResponse.json();

    // DRAW THE INTERACTIVE PROVINCES ON TOP
    geoJsonLayer = L.geoJSON(highResProvinces, { // Use the fetched data
      style: getStyle,
    }).addTo(map);
  });

  // --- 4. REACTIVE UPDATE LOGIC (Modified) ---
  function getStyle(feature) {
    // +++ MODIFIED LOGIC +++
    // Use the mapper to find the simplified name
    const officialName = feature.properties.provname;
    const simpleName = provinceNameMapper[officialName];
    
    const owner = currentData.factions[simpleName];

    return {
      fillColor: colors[owner] || 'transparent', // Default to transparent if no owner
      weight: 1,
      color: 'white',
      fillOpacity: 0.65
    };
  }

  // This reactive block remains the same and will work perfectly.
  $: if (geoJsonLayer && currentData) {
    geoJsonLayer.setStyle(getStyle);
  }
</script>

<main>
  <!-- The HTML and CSS structure can remain exactly the same -->
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
  /* Your CSS can remain exactly the same */
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