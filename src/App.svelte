<script>
  import { onMount } from 'svelte';
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';

  let year = -50;
  let map;
  let provinceLayers = {}; // To store our interactive layers

  // --- 1. HISTORICAL DATA ---
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Standoff",
      desc: "Caesar is in Gaul. The Senate (Pompey) controls Italy, Spain, and the East.",
      factions: { Gaul: "caesar", Spain: "senate", Italy: "senate", Greece: "senate", Africa: "senate" }
    },
    {
      year: -49,
      title: "49 BC: Crossing the Rubicon",
      desc: "Caesar crosses the Rubicon (Jan). He takes Italy in weeks. He marches to Spain (Aug) and defeats Pompey's armies.",
      factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "senate", Africa: "senate" }
    },
    {
      year: -48,
      title: "48 BC: Pharsalus",
      desc: "Caesar chases Pompey to Greece. He wins the decisive Battle of Pharsalus. Pompey flees to Egypt.",
      factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "caesar", Africa: "senate" }
    },
    {
      year: -46,
      title: "46 BC: Thapsus",
      desc: "Caesar invades North Africa. The Senate's army is destroyed. Cato commits suicide.",
      factions: { Gaul: "caesar", Spain: "senate", Italy: "caesar", Greece: "caesar", Africa: "caesar" }
    },
    {
      year: -45,
      title: "45 BC: Munda",
      desc: "The final battle in Spain. Caesar defeats Pompey's sons. The Civil War ends.",
      factions: { Gaul: "caesar", Spain: "caesar", Italy: "caesar", Greece: "caesar", Africa: "caesar" }
    }
  ];

  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];

  const colors = {
    caesar: "#d90429", // Red
    senate: "#1d3557", // Blue
    neutral: "transparent"
  };

  // --- 2. MAP SETUP ---
  onMount(async () => {
    // A. Initialize Leaflet
    map = L.map('map-container', {
      center: [41.9, 12.5], // Center on Rome
      zoom: 4,
      zoomControl: false,
      attributionControl: false
    });

    // B. Add "Real World" Base Map (Esri World Shaded Relief for that historical look)
    L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Shaded_Relief/MapServer/tile/{z}/{y}/{x}', {
      maxZoom: 13,
      attribution: 'Tiles &copy; Esri'
    }).addTo(map);

    // C. Fetch the 60 BC Outline (The file you found)
    // This draws the background "Roman Republic" shape
    try {
      const response = await fetch('https://raw.githubusercontent.com/sfsheath/roman-maps/master/roman_empire_bc_60_extent.geojson');
      const data = await response.json();
      
      L.geoJSON(data, {
        style: { color: '#444', weight: 1, fillOpacity: 0.1, fillColor: '#000' }
      }).addTo(map);
    } catch (e) {
      console.error("Could not load the 60BC file, using fallback", e);
    }

    // D. Define Interactive Zones
    // Since the 60BC file is one big shape, we define the provinces here to allow them to change color.
    // In the future, you can replace this with a 'provinces.geojson' file.
    const provinceDefinitions = {
      "Italy":  [[46, 7], [46, 14], [44, 14], [40, 18], [38, 16], [41, 14], [44, 10]],
      "Gaul":   [[51, 2], [49, 8], [43, 7], [42, 3], [48, -4]],
      "Spain":  [[43, -2], [42, 3], [36, -2], [36, -6], [38, -9], [43, -9]],
      "Greece": [[42, 19], [41, 26], [36, 23], [38, 20]],
      "Africa": [[37, 8], [36, 11], [32, 10], [33, 0]]
    };

    // Draw the interactive provinces
    for (const [name, coords] of Object.entries(provinceDefinitions)) {
      provinceLayers[name] = L.polygon(coords, {
        color: 'white',
        weight: 1,
        fillOpacity: 0.5
      }).addTo(map).bindPopup(name);
    }

    // Trigger initial color update
    updateColors();
  });

  // --- 3. COLOR LOGIC ---
  $: if (currentData && map) {
    updateColors();
  }

  function updateColors() {
    if(!provinceLayers.Italy) return; // Wait for map to load

    for (const [name, layer] of Object.entries(provinceLayers)) {
      const owner = currentData.factions[name];
      const color = colors[owner] || "#999";
      layer.setStyle({ fillColor: color });
    }
  }
</script>

<main>
  <div class="layout">
    <div id="map-container"></div>
    
    <div class="ui-layer">
      <div class="card">
        <h1>CAESAR'S CIVIL WAR</h1>
        <div class="date-display">{Math.abs(year)} BC</div>
        <h2>{currentData.title}</h2>
        <p>{currentData.desc}</p>
        
        <input type="range" min="-50" max="-45" step="1" bind:value={year} />
        
        <div class="legend">
          <div class="item"><span style="background:{colors.caesar}"></span> Caesar</div>
          <div class="item"><span style="background:{colors.senate}"></span> Senate</div>
        </div>

        <div class="source-credit">
          Base Map: Esri World Relief<br>
          Republic Data: sfsheath/roman-maps (GitHub)
        </div>
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: 'Georgia', serif; }
  .layout { position: relative; height: 100vh; width: 100vw; }
  
  #map-container { height: 100%; width: 100%; z-index: 1; background: #e6e6e6; }
  
  .ui-layer {
    position: absolute;
    top: 20px;
    right: 20px;
    width: 350px;
    z-index: 1000;
  }

  .card {
    background: rgba(255, 255, 255, 0.95);
    padding: 25px;
    border-radius: 2px;
    box-shadow: 0 10px 25px rgba(0,0,0,0.3);
    border-top: 5px solid #d90429;
  }

  h1 { margin: 0; font-size: 1.2rem; color: #555; letter-spacing: 1px; }
  h2 { margin: 10px 0; color: #1d3557; }
  .date-display { font-size: 3rem; font-weight: bold; color: #d90429; margin: 10px 0; font-family: monospace; }
  p { line-height: 1.5; color: #444; font-size: 1rem; }

  input[type=range] { width: 100%; margin: 20px 0; accent-color: #d90429; cursor: pointer; }

  .legend { display: flex; gap: 20px; margin-top: 15px; border-top: 1px solid #ddd; padding-top: 15px; }
  .item { display: flex; align-items: center; font-weight: bold; font-size: 0.9rem; }
  .item span { width: 15px; height: 15px; display: block; margin-right: 8px; border: 1px solid #333; }

  .source-credit { margin-top: 20px; font-size: 0.7rem; color: #888; border-top: 1px solid #eee; padding-top: 5px; }
</style>
