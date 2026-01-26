<script>
  // Range: -50 (50 BC) to -44 (44 BC)
  let year = -50; 
  
  // --- HISTORICAL DATASET ---
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Standoff",
      desc: "Caesar is in Gaul. The Senate (Pompey) controls Italy, Spain, and the East. Egypt is neutral.",
      factions: {
        gallia: "caesar",
        hispania: "senate",
        italia: "senate",
        graecia: "senate",
        asia: "senate",
        africa: "senate",
        aegyptus: "neutral"
      }
    },
    {
      year: -49,
      title: "49 BC: Crossing the Rubicon",
      desc: "Caesar invades Italy (Jan) and Spain (Aug). Pompey flees to Greece.",
      factions: {
        gallia: "caesar",
        hispania: "caesar", // CHANGED: Caesar takes Spain
        italia: "caesar",   // CHANGED: Caesar takes Italy
        graecia: "senate",
        asia: "senate",
        africa: "senate",
        aegyptus: "neutral"
      }
    },
    {
      year: -48,
      title: "48 BC: Pharsalus",
      desc: "Caesar defeats Pompey in Greece. Pompey is killed in Egypt.",
      factions: {
        gallia: "caesar",
        hispania: "caesar",
        italia: "caesar",
        graecia: "caesar",  // CHANGED: Caesar wins Greece
        asia: "senate",
        africa: "senate",
        aegyptus: "contest" // CHANGED: Egypt in Chaos
      }
    },
    {
      year: -47,
      title: "47 BC: Veni Vidi Vici",
      desc: "Caesar secures the East and Egypt. The Senate regroups in Africa.",
      factions: {
        gallia: "caesar",
        hispania: "caesar",
        italia: "caesar",
        graecia: "caesar",
        asia: "caesar",     // CHANGED: Caesar takes Asia
        africa: "senate",
        aegyptus: "client"  // CHANGED: Cleopatra rules
      }
    },
    {
      year: -46,
      title: "46 BC: Thapsus",
      desc: "Caesar conquers Africa. Pompeian resistance crumbles.",
      factions: {
        gallia: "caesar",
        hispania: "senate", // CHANGED: Rebellion in Spain
        italia: "caesar",
        graecia: "caesar",
        asia: "caesar",
        africa: "caesar",   // CHANGED: Caesar takes Africa
        aegyptus: "client"
      }
    },
    {
      year: -45,
      title: "45 BC: Munda",
      desc: "The final battle in Spain. Caesar becomes sole Dictator.",
      factions: {
        gallia: "caesar",
        hispania: "caesar", // CHANGED: Rebellion crushed
        italia: "caesar",
        graecia: "caesar",
        asia: "caesar",
        africa: "caesar",
        aegyptus: "client"
      }
    },
    {
      year: -44,
      title: "44 BC: Assassination",
      desc: "Caesar is killed. The Republic falls into chaos.",
      factions: {
        gallia: "caesar",
        hispania: "caesar",
        italia: "neutral", // CHANGED: Chaos in Rome
        graecia: "neutral",
        asia: "neutral",
        africa: "caesar",
        aegyptus: "client"
      }
    }
  ];

  // --- LOGIC FIX: ROBUST LOOKUP ---
  // If we can't find an exact year match, find the closest PREVIOUS year.
  // This prevents the map from breaking if the slider is slightly off.
  $: currentData = historicalData
    .filter(d => d.year <= year) // Get all data up to current year
    .pop() || historicalData[0]; // Take the last one (most recent)
  
  // --- COLORS ---
  const colors = {
    caesar: "#ef4444",  // Bright Red
    senate: "#1e3a8a",  // Dark Blue
    neutral: "#d4d4d8", // Grey
    contest: "#f59e0b", // Orange
    client:  "#701a75"  // Purple
  };

  function getFill(provinceId) {
    const faction = currentData.factions[provinceId];
    return colors[faction] || "#cccccc";
  }
</script>

<main>
  <div class="layout">
    <!-- MAP AREA -->
    <div class="map-container">
      <svg viewBox="0 0 1000 600" class="interactive-map">
        <!-- Ocean -->
        <rect width="1000" height="600" fill="#93c5fd" />

        <!-- NOTE: We use style="fill: ..." to force the color update -->
        
        <!-- SPAIN -->
        <path 
          d="M 50,350 L 180,330 L 220,400 L 150,480 L 80,450 Z" 
          style="fill: {getFill('hispania')}" 
          class="province"
        />
        <text x="110" y="410" class="label">HISPANIA</text>

        <!-- GAUL -->
        <path 
          d="M 180,330 L 220,180 L 380,160 L 400,280 L 320,320 L 220,400 Z" 
          style="fill: {getFill('gallia')}" 
          class="province"
        />
        <text x="280" y="260" class="label">GALLIA</text>

        <!-- ITALY -->
        <path 
          d="M 320,320 L 400,280 L 450,280 L 520,380 L 480,450 L 420,350 Z" 
          style="fill: {getFill('italia')}" 
          class="province"
        />
        <text x="440" y="340" class="label">ITALIA</text>

        <!-- GREECE -->
        <path 
          d="M 500,300 L 600,290 L 630,400 L 520,430 L 520,380 Z" 
          style="fill: {getFill('graecia')}" 
          class="province"
        />
        <text x="560" y="360" class="label">GRAECIA</text>

        <!-- ASIA -->
        <path 
          d="M 630,290 L 850,280 L 870,400 L 660,420 L 630,400 Z" 
          style="fill: {getFill('asia')}" 
          class="province"
        />
        <text x="750" y="350" class="label">ASIA</text>

        <!-- AFRICA -->
        <path 
          d="M 180,500 L 550,480 L 550,600 L 180,600 Z" 
          style="fill: {getFill('africa')}" 
          class="province"
        />
        <text x="350" y="550" class="label">AFRICA</text>

        <!-- EGYPT -->
        <path 
          d="M 600,450 L 870,450 L 870,600 L 600,600 Z" 
          style="fill: {getFill('aegyptus')}" 
          class="province"
        />
        <text x="730" y="520" class="label">AEGYPTUS</text>
      </svg>
      
      <!-- CONTROLS -->
      <div class="timeline-area">
        <div class="year-display">{Math.abs(year)} BC</div>
        <input 
          type="range" 
          min="-50" 
          max="-44" 
          step="1" 
          bind:value={year} 
        />
        <div class="ticks">
          <span>50</span><span>49</span><span>48</span><span>47</span><span>46</span><span>45</span><span>44</span>
        </div>
      </div>
    </div>

    <!-- INFO PANEL -->
    <div class="info-panel">
      <div class="header">
        <h1>CAESAR'S CIVIL WAR</h1>
        <div class="subtitle">Data extracted from Wikipedia</div>
      </div>
      
      <div class="content">
        <h2 class="year-title">{currentData.title}</h2>
        <p>{currentData.desc}</p>
        
        <!-- DEBUGGER: If this text changes, the logic is working -->
        <div class="debug-box">
          <strong>Debug Status:</strong><br>
          Italy Owner: {currentData.factions.italia}
        </div>

        <div class="legend">
          <h3>Factions</h3>
          <div class="legend-item"><span style="background:#ef4444"></span> Caesar (Red)</div>
          <div class="legend-item"><span style="background:#1e3a8a"></span> Senate/Pompey (Blue)</div>
          <div class="legend-item"><span style="background:#701a75"></span> Client (Purple)</div>
          <div class="legend-item"><span style="background:#f59e0b"></span> Contested (Orange)</div>
          <div class="legend-item"><span style="background:#d4d4d8"></span> Neutral (Grey)</div>
        </div>
      </div>
      
      <div class="footer">
         Interactive Map Prototype
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: 'Helvetica Neue', sans-serif; background-color: #f3f4f6; overflow: hidden; }
  
  .layout { display: flex; height: 100vh; width: 100vw; }
  
  .map-container { flex: 3; position: relative; background: #93c5fd; display: flex; flex-direction: column; border-right: 5px solid #333; }
  .interactive-map { width: 100%; height: 85%; }
  
  .province {
    stroke: #ffffff;
    stroke-width: 2px;
    transition: fill 0.5s ease; /* Animation for color change */
    cursor: pointer;
  }
  .province:hover { opacity: 0.8; stroke-width: 4px; }

  .label { fill: white; font-weight: 900; pointer-events: none; text-shadow: 0px 0px 5px rgba(0,0,0,0.5); text-anchor: middle; }

  .timeline-area { background: #1f2937; height: 15%; padding: 0 40px; display: flex; flex-direction: column; justify-content: center; align-items: center; color: white; }
  input[type=range] { width: 80%; accent-color: #ef4444; cursor: pointer; margin: 10px 0; }
  .year-display { font-size: 2.5rem; font-weight: bold; color: #fca5a5; font-family: monospace; }
  .ticks { display: flex; justify-content: space-between; width: 80%; color: #9ca3af; }

  .info-panel { flex: 1; min-width: 320px; background: #fdfbf7; padding: 30px; display: flex; flex-direction: column; overflow-y: auto; box-shadow: -5px 0 15px rgba(0,0,0,0.1); }
  .header h1 { color: #991b1b; margin: 0; border-bottom: 3px solid #991b1b; padding-bottom: 10px; }
  .content { margin-top: 20px; flex-grow: 1; }
  .year-title { color: #1f2937; font-size: 1.5rem; }
  
  .debug-box { background: #eee; padding: 10px; font-family: monospace; margin: 10px 0; border: 1px dashed #999; font-size: 0.8rem; }

  .legend { margin-top: 20px; background: #e5e7eb; padding: 15px; border-radius: 6px; }
  .legend-item { display: flex; align-items: center; margin-bottom: 5px; font-weight: bold; font-size: 0.9rem; }
  .legend-item span { display: block; width: 20px; height: 20px; margin-right: 10px; border: 1px solid #999; }
  
  @media (max-width: 900px) { .layout { flex-direction: column; overflow: auto; } .map-container { height: 50vh; } }
</style>
