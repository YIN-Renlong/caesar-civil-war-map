<script>
  let year = -50; 
  
  // --- HISTORICAL DATA ---
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Standoff",
      desc: "Caesar is in Gaul. The Senate (Pompey) controls Italy, Spain, and the East. Egypt is independent.",
      factions: {
        gallia: "caesar",
        hispania: "senate",
        italia: "senate",
        graecia: "senate",
        asia: "senate",
        africa: "senate",
        aegyptus: "neutral",
        pontus: "neutral"
      }
    },
    {
      year: -49,
      title: "49 BC: Civil War Begins",
      desc: "Jan: Caesar crosses the Rubicon and takes Italy. Aug: Caesar conquers Spain (Ilerda). Pompey flees to Greece.",
      factions: {
        gallia: "caesar",
        hispania: "caesar", // RED
        italia: "caesar",   // RED
        graecia: "senate",
        asia: "senate",
        africa: "senate",   // Curio defeated
        aegyptus: "neutral",
        pontus: "neutral"
      }
    },
    {
      year: -48,
      title: "48 BC: Pharsalus",
      desc: "Caesar defeats Pompey at Pharsalus (Greece). Pompey flees to Egypt and is assassinated.",
      factions: {
        gallia: "caesar",
        hispania: "caesar",
        italia: "caesar",
        graecia: "caesar",  // RED
        asia: "senate",
        africa: "senate",
        aegyptus: "contest", // Civil War in Egypt
        pontus: "contest"    // Pharnaces invades
      }
    },
    {
      year: -47,
      title: "47 BC: Veni, Vidi, Vici",
      desc: "Caesar settles Egypt (Cleopatra). He marches north and crushes Pharnaces at Zela in days.",
      factions: {
        gallia: "caesar",
        hispania: "caesar",
        italia: "caesar",
        graecia: "caesar",
        asia: "caesar",     // RED
        africa: "senate",   // Senate regroups
        aegyptus: "client", // Cleopatra
        pontus: "caesar"
      }
    },
    {
      year: -46,
      title: "46 BC: Thapsus",
      desc: "Caesar invades Africa. Cato the Younger commits suicide. The Senate's main army is destroyed.",
      factions: {
        gallia: "caesar",
        hispania: "senate", // REVOLT by Pompey's sons
        italia: "caesar",
        graecia: "caesar",
        asia: "caesar",
        africa: "caesar",   // RED
        aegyptus: "client",
        pontus: "caesar"
      }
    },
    {
      year: -45,
      title: "45 BC: Munda",
      desc: "Caesar returns to Spain and defeats the last Pompeian army at Munda. The war is effectively over.",
      factions: {
        gallia: "caesar",
        hispania: "caesar", // RED
        italia: "caesar",
        graecia: "caesar",
        asia: "caesar",
        africa: "caesar",
        aegyptus: "client",
        pontus: "caesar"
      }
    },
    {
      year: -44,
      title: "44 BC: Ides of March",
      desc: "Caesar is Dictator Perpetuo. He is assassinated on March 15th.",
      factions: {
        gallia: "caesar",
        hispania: "caesar",
        italia: "neutral", // CHAOS
        graecia: "neutral",
        asia: "neutral",
        africa: "caesar",
        aegyptus: "client",
        pontus: "caesar"
      }
    }
  ];

  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];
  
  const colors = {
    caesar: "#bf0a30",  // Roman Red
    senate: "#203a43",  // Senate Blue/Green
    neutral: "#d3d3d3", // Land Grey
    contest: "#f59e0b", // Conflict Orange
    client:  "#6b21a8"  // Royal Purple
  };
</script>

<main>
  <div class="layout">
    <div class="map-container">
      
      <!-- 
        HIGH FIDELITY SVG MAP 
        These 'd' paths are simplified vectors of real geography.
      -->
      {#key year}
      <svg viewBox="0 0 800 500" class="interactive-map">
        <!-- Mediterranean Sea Color -->
        <rect width="800" height="500" fill="#a5c4d4" />

        <!-- GALLIA (France) -->
        <path 
          class="province"
          fill={colors[currentData.factions.gallia]} 
          d="M 230,130 L 260,90 L 320,80 L 360,110 L 370,180 L 340,210 L 290,220 L 240,210 L 210,180 Z"
        />
        <text x="280" y="160" class="label">GALLIA</text>

        <!-- HISPANIA (Spain) -->
        <path 
          class="province"
          fill={colors[currentData.factions.hispania]} 
          d="M 90,250 L 180,240 L 210,210 L 240,210 L 245,280 L 210,320 L 150,330 L 90,300 Z"
        />
        <text x="150" y="280" class="label">HISPANIA</text>

        <!-- ITALIA (The Boot) -->
        <path 
          class="province"
          fill={colors[currentData.factions.italia]} 
          d="M 340,210 L 380,190 L 410,200 L 440,260 L 460,300 L 430,310 L 410,280 L 380,240 Z"
        />
        <text x="400" y="240" class="label">ITALIA</text>

        <!-- AFRICA (North Africa West) -->
        <path 
          class="province"
          fill={colors[currentData.factions.africa]} 
          d="M 250,340 L 400,340 L 420,380 L 400,410 L 250,400 Z"
        />
        <text x="320" y="370" class="label">AFRICA</text>

        <!-- GRAECIA (Greece) -->
        <path 
          class="province"
          fill={colors[currentData.factions.graecia]} 
          d="M 460,240 L 510,240 L 530,280 L 510,310 L 480,290 L 470,260 Z"
        />
        <text x="490" y="270" class="label">GRAECIA</text>

        <!-- ASIA (Turkey) -->
        <path 
          class="province"
          fill={colors[currentData.factions.asia]} 
          d="M 540,230 L 650,220 L 670,270 L 640,290 L 560,280 L 540,250 Z"
        />
        <text x="600" y="260" class="label">ASIA</text>

        <!-- PONTUS (North Turkey) -->
        <path 
          class="province"
          fill={colors[currentData.factions.pontus]} 
          d="M 590,200 L 680,190 L 680,220 L 590,230 Z"
        />
        <text x="635" y="215" class="label sm">PONTUS</text>

        <!-- AEGYPTUS (Egypt) -->
        <path 
          class="province"
          fill={colors[currentData.factions.aegyptus]} 
          d="M 580,330 L 650,330 L 650,420 L 580,420 Z"
        />
        <text x="615" y="380" class="label">AEGYPTUS</text>

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
      </div>
    </div>

    <!-- INFO PANEL -->
    <div class="info-panel">
      <div class="header">
        <h1>CAESAR'S WAR</h1>
        <div class="sub">50 BC - 44 BC</div>
      </div>
      
      <div class="content">
        <h2 class="year-title">{currentData.title}</h2>
        <div class="desc-box">
          <p>{currentData.desc}</p>
        </div>
        
        <div class="legend">
          <div class="legend-item"><span style="background:{colors.caesar}"></span> Caesar (Populares)</div>
          <div class="legend-item"><span style="background:{colors.senate}"></span> Senate (Optimates)</div>
          <div class="legend-item"><span style="background:{colors.contest}"></span> Contested / War</div>
          <div class="legend-item"><span style="background:{colors.client}"></span> Client State</div>
        </div>

        <div class="debug">
          Current State: <strong>{currentData.factions.italia.toUpperCase()}</strong> controls Italy.
        </div>
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: 'Times New Roman', serif; background-color: #2c3e50; color: #333; overflow: hidden; }
  .layout { display: flex; height: 100vh; width: 100vw; }
  
  .map-container { flex: 3; position: relative; background: #a5c4d4; display: flex; flex-direction: column; border-right: 5px solid #222; }
  .interactive-map { width: 100%; height: 85%; filter: drop-shadow(0px 0px 5px rgba(0,0,0,0.3)); }
  
  .province {
    stroke: #333;
    stroke-width: 1.5px;
    cursor: pointer;
    transition: fill 0.3s ease;
  }
  .province:hover { opacity: 0.8; stroke: white; stroke-width: 2px; }

  .label { fill: white; font-family: sans-serif; font-weight: bold; pointer-events: none; text-shadow: 1px 1px 2px black; text-anchor: middle; font-size: 14px; letter-spacing: 1px; }
  .label.sm { font-size: 10px; }

  .timeline-area { background: #222; height: 15%; display: flex; flex-direction: column; justify-content: center; align-items: center; color: #fff; }
  input[type=range] { width: 70%; accent-color: #bf0a30; cursor: pointer; }
  .year-display { font-size: 3rem; font-weight: bold; color: #bf0a30; font-family: 'Courier New', monospace; margin-bottom: 10px; }

  .info-panel { flex: 1; min-width: 350px; background: #f4f1ea; padding: 40px; display: flex; flex-direction: column; border-left: 2px solid #000; box-shadow: -10px 0 30px rgba(0,0,0,0.5); }
  .header h1 { color: #bf0a30; border-bottom: 4px double #bf0a30; margin: 0 0 10px 0; text-transform: uppercase; letter-spacing: 2px; }
  .sub { color: #555; font-style: italic; margin-bottom: 30px; }
  
  .year-title { font-size: 1.8rem; margin-bottom: 15px; color: #203a43; }
  .desc-box { font-size: 1.1rem; line-height: 1.6; min-height: 100px; }
  
  .legend { margin-top: auto; background: #e0e0e0; padding: 20px; border-radius: 5px; border: 1px solid #999; }
  .legend-item { display: flex; align-items: center; margin-bottom: 8px; font-weight: bold; font-family: sans-serif; font-size: 0.9rem; }
  .legend-item span { display: block; width: 20px; height: 20px; margin-right: 10px; border: 1px solid #333; }
  
  .debug { margin-top: 15px; font-size: 0.8rem; color: #777; font-family: monospace; border-top: 1px solid #ccc; padding-top: 5px; }

  @media (max-width: 900px) { .layout { flex-direction: column; } .map-container { height: 50vh; } }
</style>
