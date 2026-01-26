<script>
  let year = -50; 
  
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Standoff",
      desc: "Caesar is in Gaul. The Senate (Pompey) controls Italy, Spain, and the East.",
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
      desc: "Caesar takes Italy (Jan) and Spain (Aug). Pompey flees to Greece.",
      factions: {
        gallia: "caesar",
        hispania: "caesar", // RED
        italia: "caesar",   // RED
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
        graecia: "caesar",  // RED
        asia: "senate",
        africa: "senate",
        aegyptus: "contest" // ORANGE
      }
    },
    {
      year: -47,
      title: "47 BC: Veni Vidi Vici",
      desc: "Caesar secures the East. Cleopatra installed in Egypt.",
      factions: {
        gallia: "caesar",
        hispania: "caesar",
        italia: "caesar",
        graecia: "caesar",
        asia: "caesar",     // RED
        africa: "senate",
        aegyptus: "client"  // PURPLE
      }
    },
    {
      year: -46,
      title: "46 BC: Thapsus",
      desc: "Caesar conquers Africa. Cato commits suicide.",
      factions: {
        gallia: "caesar",
        hispania: "senate", // BLUE (Revolt)
        italia: "caesar",
        graecia: "caesar",
        asia: "caesar",
        africa: "caesar",   // RED
        aegyptus: "client"
      }
    },
    {
      year: -45,
      title: "45 BC: Munda",
      desc: "The final battle in Spain. The Civil War ends.",
      factions: {
        gallia: "caesar",
        hispania: "caesar", // RED
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
      desc: "Caesar is killed on the Ides of March.",
      factions: {
        gallia: "caesar",
        hispania: "caesar",
        italia: "neutral", // GREY
        graecia: "neutral",
        asia: "neutral",
        africa: "caesar",
        aegyptus: "client"
      }
    }
  ];

  // Logic: Ensure we always have data
  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];
  
  const colors = {
    caesar: "#D92828",  // Bright Red
    senate: "#1D3557",  // Deep Navy Blue
    neutral: "#9CA3AF", // Grey
    contest: "#F59E0B", // Orange
    client:  "#7C3AED"  // Purple
  };
</script>

<main>
  <div class="layout">
    <div class="map-container">
      
      <!-- 
        THE KEY BLOCK: 
        This tells Svelte: "Whenever 'year' changes, destroy and recreate the map."
        This forces the colors to update, 100% guaranteed.
      -->
      {#key year}
      <svg viewBox="0 0 1000 600" class="interactive-map">
        <!-- Sea -->
        <rect width="1000" height="600" fill="#A8D0E6" />

        <!-- 
           DIRECT BINDING: 
           fill={colors[currentData.factions.provinceName]}
           We removed the helper function. This connects the data directly to the visual.
        -->

        <!-- SPAIN -->
        <path 
          d="M 50,350 L 180,330 L 220,400 L 150,480 L 80,450 Z" 
          fill={colors[currentData.factions.hispania]} 
          class="province"
        />
        <text x="110" y="410" class="label">HISPANIA</text>

        <!-- GAUL -->
        <path 
          d="M 180,330 L 220,180 L 380,160 L 400,280 L 320,320 L 220,400 Z" 
          fill={colors[currentData.factions.gallia]} 
          class="province"
        />
        <text x="280" y="260" class="label">GALLIA</text>

        <!-- ITALY -->
        <path 
          d="M 320,320 L 400,280 L 450,280 L 520,380 L 480,450 L 420,350 Z" 
          fill={colors[currentData.factions.italia]} 
          class="province"
        />
        <text x="440" y="340" class="label">ITALIA</text>

        <!-- GREECE -->
        <path 
          d="M 500,300 L 600,290 L 630,400 L 520,430 L 520,380 Z" 
          fill={colors[currentData.factions.graecia]} 
          class="province"
        />
        <text x="560" y="360" class="label">GRAECIA</text>

        <!-- ASIA -->
        <path 
          d="M 630,290 L 850,280 L 870,400 L 660,420 L 630,400 Z" 
          fill={colors[currentData.factions.asia]} 
          class="province"
        />
        <text x="750" y="350" class="label">ASIA</text>

        <!-- AFRICA -->
        <path 
          d="M 180,500 L 550,480 L 550,600 L 180,600 Z" 
          fill={colors[currentData.factions.africa]} 
          class="province"
        />
        <text x="350" y="550" class="label">AFRICA</text>

        <!-- EGYPT -->
        <path 
          d="M 600,450 L 870,450 L 870,600 L 600,600 Z" 
          fill={colors[currentData.factions.aegyptus]} 
          class="province"
        />
        <text x="730" y="520" class="label">AEGYPTUS</text>
      </svg>
      {/key}
      
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
      </div>
      
      <div class="content">
        <h2 class="year-title">{currentData.title}</h2>
        <p>{currentData.desc}</p>
        
        <div class="legend">
          <h3>Factions</h3>
          <div class="legend-item"><span style="background:{colors.caesar}"></span> Caesar</div>
          <div class="legend-item"><span style="background:{colors.senate}"></span> Senate / Pompey</div>
          <div class="legend-item"><span style="background:{colors.client}"></span> Client Kingdom</div>
          <div class="legend-item"><span style="background:{colors.contest}"></span> Contested</div>
          <div class="legend-item"><span style="background:{colors.neutral}"></span> Neutral</div>
        </div>
      </div>

      <div class="debug-text">
        Debug Check: Italy is {currentData.factions.italia}
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: 'Georgia', serif; background-color: #f3f4f6; overflow: hidden; }
  .layout { display: flex; height: 100vh; width: 100vw; }
  .map-container { flex: 3; position: relative; background: #A8D0E6; display: flex; flex-direction: column; border-right: 5px solid #333; }
  .interactive-map { width: 100%; height: 85%; }
  
  /* Removed CSS transitions to ensure instant color snap */
  .province {
    stroke: #ffffff;
    stroke-width: 2px;
    cursor: pointer;
  }
  .province:hover { opacity: 0.8; stroke-width: 4px; }
  
  .label { fill: white; font-family: sans-serif; font-weight: 900; pointer-events: none; text-shadow: 0px 0px 5px rgba(0,0,0,0.5); text-anchor: middle; }
  .timeline-area { background: #1f2937; height: 15%; padding: 0 40px; display: flex; flex-direction: column; justify-content: center; align-items: center; color: white; }
  input[type=range] { width: 80%; accent-color: #D92828; cursor: pointer; margin: 10px 0; }
  .year-display { font-size: 2.5rem; font-weight: bold; color: #fca5a5; font-family: monospace; }
  .ticks { display: flex; justify-content: space-between; width: 80%; color: #9ca3af; }
  .info-panel { flex: 1; min-width: 300px; background: #fdfbf7; padding: 30px; display: flex; flex-direction: column; overflow-y: auto; box-shadow: -5px 0 15px rgba(0,0,0,0.1); }
  .header h1 { color: #991b1b; margin: 0; border-bottom: 3px solid #991b1b; padding-bottom: 10px; }
  .content { margin-top: 20px; flex-grow: 1; }
  .year-title { color: #1f2937; font-size: 1.5rem; }
  .legend { margin-top: 20px; background: #e5e7eb; padding: 15px; border-radius: 6px; }
  .legend-item { display: flex; align-items: center; margin-bottom: 5px; font-weight: bold; font-size: 0.9rem; }
  .legend-item span { display: block; width: 20px; height: 20px; margin-right: 10px; border: 1px solid #999; }
  .debug-text { margin-top: 20px; font-family: monospace; font-size: 0.8rem; color: #999; border-top: 1px solid #ddd; padding-top: 10px;}
  @media (max-width: 900px) { .layout { flex-direction: column; overflow: auto; } .map-container { height: 50vh; } }
</style>
