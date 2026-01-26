<script>
  let year = -50; 
  
  // --- HISTORICAL DATA ---
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Standoff",
      desc: "Caesar is in Gaul. The Senate (Pompey) controls Italy, Spain, and the East. Egypt is neutral.",
      factions: {
        gallia: "caesar", hispania: "senate", italia: "senate",
        graecia: "senate", asia: "senate", africa: "senate",
        aegyptus: "neutral", pontus: "neutral"
      }
    },
    {
      year: -49,
      title: "49 BC: The Civil War Begins",
      desc: "Caesar crosses the Rubicon (Jan) taking Italy. He marches to Spain (Aug) and defeats Pompey's legates at Ilerda.",
      factions: {
        gallia: "caesar", hispania: "caesar", italia: "caesar",
        graecia: "senate", asia: "senate", africa: "senate",
        aegyptus: "neutral", pontus: "neutral"
      }
    },
    {
      year: -48,
      title: "48 BC: Battle of Pharsalus",
      desc: "Caesar chases Pompey to Greece and destroys his army at Pharsalus. Pompey flees to Egypt and is killed.",
      factions: {
        gallia: "caesar", hispania: "caesar", italia: "caesar",
        graecia: "caesar", asia: "senate", africa: "senate",
        aegyptus: "contest", pontus: "contest"
      }
    },
    {
      year: -47,
      title: "47 BC: Veni, Vidi, Vici",
      desc: "Caesar settles the Egyptian succession (Cleopatra). He marches through Asia Minor defeating Pharnaces at Zela.",
      factions: {
        gallia: "caesar", hispania: "caesar", italia: "caesar",
        graecia: "caesar", asia: "caesar", africa: "senate",
        aegyptus: "client", pontus: "caesar"
      }
    },
    {
      year: -46,
      title: "46 BC: Battle of Thapsus",
      desc: "Caesar invades Africa. The Senatorial army is destroyed. Cato the Younger commits suicide.",
      factions: {
        gallia: "caesar", hispania: "senate", italia: "caesar",
        graecia: "caesar", asia: "caesar", africa: "caesar",
        aegyptus: "client", pontus: "caesar"
      }
    },
    {
      year: -45,
      title: "45 BC: Battle of Munda",
      desc: "Caesar returns to Spain to crush the last rebellion led by Pompey's sons. The war ends.",
      factions: {
        gallia: "caesar", hispania: "caesar", italia: "caesar",
        graecia: "caesar", asia: "caesar", africa: "caesar",
        aegyptus: "client", pontus: "caesar"
      }
    },
    {
      year: -44,
      title: "44 BC: The Assassination",
      desc: "Caesar, now Dictator Perpetuo, is assassinated on the Ides of March.",
      factions: {
        gallia: "caesar", hispania: "caesar", italia: "neutral",
        graecia: "neutral", asia: "neutral", africa: "caesar",
        aegyptus: "client", pontus: "caesar"
      }
    }
  ];

  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];
  
  const colors = {
    caesar: "#e11d48",  // Red
    senate: "#1e3a8a",  // Blue
    neutral: "#d1d5db", // Grey
    contest: "#f59e0b", // Orange
    client:  "#7e22ce"  // Purple
  };
</script>

<main>
  <div class="layout">
    <div class="map-container">
      
      <!-- REALISTIC MAP SVG -->
      <!-- ViewBox optimized for Mediterranean Europe -->
      {#key year}
      <svg viewBox="0 0 900 550" class="interactive-map">
        <rect width="900" height="550" fill="#a5f3fc" /> <!-- Ocean -->

        <!-- HISPANIA (Spain/Portugal) -->
        <path 
          class="province" 
          fill={colors[currentData.factions.hispania]}
          d="M 60,320 L 80,310 L 130,300 L 170,290 L 180,285 L 170,260 L 190,250 L 180,230 L 150,230 L 120,230 L 80,235 L 50,240 L 40,260 L 30,300 L 40,340 L 60,350 L 100,360 L 130,350 L 150,340 Z"
        />
        <text x="90" y="300" class="label">HISPANIA</text>

        <!-- GALLIA (France) -->
        <path 
          class="province" 
          fill={colors[currentData.factions.gallia]}
          d="M 180,230 L 190,200 L 170,160 L 180,130 L 220,110 L 260,100 L 300,105 L 320,130 L 330,160 L 310,200 L 290,210 L 250,220 L 220,230 L 190,230 Z"
        />
        <text x="240" y="170" class="label">GALLIA</text>

        <!-- ITALIA (The Boot) -->
        <path 
          class="province" 
          fill={colors[currentData.factions.italia]}
          d="M 290,210 L 330,195 L 360,190 L 390,200 L 395,220 L 400,240 L 415,260 L 430,290 L 450,300 L 460,310 L 440,320 L 410,310 L 390,290 L 370,260 L 350,240 L 320,225 Z"
        />
        <text x="380" y="240" class="label">ITALIA</text>

        <!-- GRAECIA (Greece/Macedonia) -->
        <path 
          class="province" 
          fill={colors[currentData.factions.graecia]}
          d="M 440,230 L 480,230 L 510,235 L 530,250 L 535,280 L 520,310 L 500,320 L 490,300 L 470,290 L 460,260 L 450,240 Z"
        />
        <text x="490" y="270" class="label">GRAECIA</text>

        <!-- ASIA (Turkey) -->
        <path 
          class="province" 
          fill={colors[currentData.factions.asia]}
          d="M 540,240 L 570,220 L 620,210 L 680,210 L 700,230 L 680,260 L 650,280 L 600,290 L 570,280 L 550,260 Z"
        />
        <text x="610" y="250" class="label">ASIA</text>

        <!-- PONTUS (Northern Turkey Coast) -->
        <path 
          class="province" 
          fill={colors[currentData.factions.pontus]}
          d="M 580,210 L 620,200 L 660,200 L 700,210 L 700,225 L 670,220 L 620,215 Z"
        />
        <text x="640" y="210" class="label small">PONTUS</text>

        <!-- AFRICA (Tunisia/Libya) -->
        <path 
          class="province" 
          fill={colors[currentData.factions.africa]}
          d="M 280,380 L 320,360 L 360,350 L 390,360 L 400,390 L 380,410 L 340,420 L 280,410 L 240,400 L 250,380 Z"
        />
        <text x="330" y="390" class="label">AFRICA</text>

        <!-- AEGYPTUS (Egypt) -->
        <path 
          class="province" 
          fill={colors[currentData.factions.aegyptus]}
          d="M 570,360 L 650,350 L 660,380 L 650,450 L 570,450 L 560,400 Z"
        />
        <text x="610" y="400" class="label">AEGYPTUS</text>
      </svg>
      {/key}
      
      <div class="timeline-area">
        <div class="year-display">{Math.abs(year)} BC</div>
        <input type="range" min="-50" max="-44" step="1" bind:value={year} />
      </div>
    </div>

    <div class="info-panel">
      <div class="header">
        <h1>CAESAR'S CIVIL WAR</h1>
        <div class="sub">50 BC — 44 BC</div>
      </div>
      
      <div class="content">
        <h2 class="year-title">{currentData.title}</h2>
        <div class="desc-text">{currentData.desc}</div>
        
        <div class="legend">
          <div class="legend-item"><span style="background:{colors.caesar}"></span> Caesar (Populares)</div>
          <div class="legend-item"><span style="background:{colors.senate}"></span> Senate (Optimates)</div>
          <div class="legend-item"><span style="background:{colors.client}"></span> Client State</div>
          <div class="legend-item"><span style="background:{colors.contest}"></span> Contested</div>
          <div class="legend-item"><span style="background:{colors.neutral}"></span> Neutral</div>
        </div>
      </div>
      <div class="footer">
         Map vectors approximated from historical data.
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: 'Garamond', serif; background-color: #f0f0f0; overflow: hidden; }
  .layout { display: flex; height: 100vh; width: 100vw; }
  
  .map-container { flex: 3; position: relative; background: #a5f3fc; display: flex; flex-direction: column; border-right: 2px solid #555; }
  .interactive-map { width: 100%; height: 85%; }
  
  /* Make provinces look like paper/land */
  .province {
    stroke: #555;
    stroke-width: 1px;
    cursor: pointer;
    transition: fill 0.3s ease;
    filter: drop-shadow(2px 2px 3px rgba(0,0,0,0.3));
  }
  .province:hover { opacity: 0.9; stroke: white; stroke-width: 2px; }
  
  .label { fill: white; font-family: sans-serif; font-weight: bold; pointer-events: none; text-shadow: 1px 1px 2px black; text-anchor: middle; font-size: 14px; letter-spacing: 1px; }
  .label.small { font-size: 10px; }

  .timeline-area { background: #333; height: 15%; display: flex; flex-direction: column; justify-content: center; align-items: center; color: white; }
  input[type=range] { width: 80%; cursor: pointer; accent-color: #e11d48; }
  .year-display { font-size: 2.5rem; font-weight: bold; color: #e11d48; margin-bottom: 10px; font-family: monospace; }
  
  .info-panel { flex: 1; min-width: 320px; background: #fdfbf7; padding: 40px; display: flex; flex-direction: column; box-shadow: -5px 0 20px rgba(0,0,0,0.2); }
  .header h1 { color: #e11d48; margin: 0; border-bottom: 3px double #e11d48; padding-bottom: 5px; }
  .sub { color: #666; font-style: italic; margin-top: 5px; }
  .content { margin-top: 30px; flex-grow: 1; }
  .year-title { font-size: 1.6rem; color: #111; margin-bottom: 10px; }
  .desc-text { font-size: 1.1rem; line-height: 1.5; font-family: sans-serif; color: #333; }
  
  .legend { margin-top: 30px; background: #e5e5e5; padding: 15px; border-radius: 5px; }
  .legend-item { display: flex; align-items: center; margin-bottom: 5px; font-family: sans-serif; font-size: 0.9rem; font-weight: bold; }
  .legend-item span { display: block; width: 15px; height: 15px; margin-right: 10px; border: 1px solid #999; }
  
  .footer { margin-top: auto; font-size: 0.8rem; color: #999; text-align: center; border-top: 1px solid #ddd; padding-top: 10px; }
  
  @media (max-width: 900px) { .layout { flex-direction: column; } .map-container { height: 50vh; } }
</style>
