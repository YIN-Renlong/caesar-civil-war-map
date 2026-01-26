<script>
  let year = -50; 
  
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Gathering Storm",
      desc: "Caesar is in Gaul. The Senate in Rome demands he disband his army. Pompey controls Spain and Italy.",
      factions: { "gaul": "caesar", "italy": "senate", "spain": "senate", "greece": "senate", "africa": "senate", "egypt": "neutral" }
    },
    {
      year: -49,
      title: "49 BC: Crossing the Rubicon",
      desc: "Caesar crosses the Rubicon in January. Pompey and the Senate flee Italy for Greece. Caesar secures Spain.",
      factions: { "gaul": "caesar", "italy": "caesar", "spain": "caesar", "greece": "senate", "africa": "senate", "egypt": "neutral" }
    },
    {
      year: -48,
      title: "48 BC: Battle of Pharsalus",
      desc: "Caesar defeats Pompey in Greece. Pompey flees to Egypt and is assassinated. Caesar arrives in Egypt.",
      factions: { "gaul": "caesar", "italy": "caesar", "spain": "caesar", "greece": "caesar", "africa": "senate", "egypt": "caesar" }
    },
    {
      year: -47,
      title: "47 BC: The Alexandrian War",
      desc: "Caesar secures Egypt and defeats Pharnaces in the East ('Veni, vidi, vici'). Senate forces regroup in Africa.",
      factions: { "gaul": "caesar", "italy": "caesar", "spain": "caesar", "greece": "caesar", "africa": "senate", "egypt": "caesar" }
    },
    {
      year: -46,
      title: "46 BC: Thapsus & Triumph",
      desc: "Caesar defeats the Senate forces in Africa. He returns to Rome for a massive triumph.",
      factions: { "gaul": "caesar", "italy": "caesar", "spain": "caesar", "greece": "caesar", "africa": "caesar", "egypt": "caesar" }
    }
  ];

  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];
  
  const colors = {
    caesar: "#C8102E", 
    senate: "#1C3132", 
    neutral: "#E6E6A6" 
  };

  function getFill(provinceId) {
    const owner = currentData.factions[provinceId];
    return colors[owner] || "#ccc";
  }
</script>

<main>
  <div class="layout">
    <div class="map-container">
      <svg viewBox="0 0 800 500" class="interactive-map">
        <rect width="800" height="500" fill="#a4c6d1" />
        
        <!-- SPAIN -->
        <path d="M 50 300 L 150 280 L 180 350 L 100 420 Z" fill={getFill('spain')} stroke="white" stroke-width="2"/>
        <text x="100" y="350" class="label">Hispania</text>

        <!-- GAUL -->
        <path d="M 160 150 L 300 140 L 320 250 L 180 260 Z" fill={getFill('gaul')} stroke="white" stroke-width="2"/>
        <text x="230" y="200" class="label">Gallia</text>

        <!-- ITALY -->
        <path d="M 330 220 L 400 220 L 450 350 L 400 380 Z" fill={getFill('italy')} stroke="white" stroke-width="2"/>
        <text x="390" y="300" class="label">Italia</text>

        <!-- GREECE -->
        <path d="M 480 280 L 550 280 L 560 380 L 490 390 Z" fill={getFill('greece')} stroke="white" stroke-width="2"/>
        <text x="510" y="340" class="label">Graecia</text>

        <!-- AFRICA -->
        <path d="M 150 450 L 500 450 L 500 500 L 150 500 Z" fill={getFill('africa')} stroke="white" stroke-width="2"/>
        <text x="300" y="480" class="label">Africa</text>

        <!-- EGYPT -->
        <path d="M 580 400 L 700 400 L 700 500 L 580 500 Z" fill={getFill('egypt')} stroke="white" stroke-width="2"/>
        <text x="630" y="450" class="label">Aegyptus</text>
      </svg>
      
      <div class="timeline-area">
        <input type="range" min="-50" max="-46" step="1" bind:value={year} />
        <div class="year-display">{Math.abs(year)} BC</div>
      </div>
    </div>

    <div class="info-panel">
      <h1>The Civil War</h1>
      <h2 class="year-title">{currentData.title}</h2>
      <p>{currentData.desc}</p>
      <div class="legend">
        <div class="legend-item"><span style="background:{colors.caesar}"></span> Caesar</div>
        <div class="legend-item"><span style="background:{colors.senate}"></span> Pompey/Senate</div>
        <div class="legend-item"><span style="background:{colors.neutral}"></span> Neutral</div>
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: "Georgia", serif; background-color: #f4f4e8; color: #333; }
  .layout { display: flex; height: 100vh; }
  .map-container { flex: 2; position: relative; background: #a4c6d1; display: flex; flex-direction: column; }
  .interactive-map { width: 100%; height: 100%; max-height: 80vh; }
  .label { fill: white; font-family: sans-serif; font-size: 14px; font-weight: bold; pointer-events: none; text-shadow: 1px 1px 2px black; }
  .timeline-area { background: #1C3132; padding: 20px; display: flex; align-items: center; justify-content: center; color: white; height: 20vh; }
  input[type=range] { width: 300px; margin-right: 20px; cursor: pointer; }
  .year-display { font-size: 2rem; font-weight: bold; font-family: "Courier New", monospace; }
  .info-panel { flex: 1; background: #f4f4e8; padding: 40px; border-left: 5px solid #1C3132; display: flex; flex-direction: column; }
  h1 { color: #C8102E; text-transform: uppercase; border-bottom: 2px solid #ccc; padding-bottom: 10px; }
  .legend { margin-top: 30px; background: rgba(255,255,255,0.5); padding: 15px; }
  .legend-item { display: flex; align-items: center; margin-bottom: 10px; font-weight: bold; }
  .legend-item span { display: block; width: 20px; height: 20px; margin-right: 10px; border: 1px solid #333; }
  @media (max-width: 768px) { .layout { flex-direction: column; } .timeline-area { height: auto; padding: 10px; } }
</style>
