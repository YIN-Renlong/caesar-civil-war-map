<script>
  let year = -50; 
  
  // --- REAL HISTORICAL DATA (Caesar's Civil War) ---
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Standoff",
      desc: "Tensions rise. The Senate, led by Pompey, demands Caesar disband his legions in Gaul. Caesar refuses unless Pompey does the same. Rome is on the brink.",
      factions: {
        "gaul": "caesar",
        "italy": "senate",
        "spain": "senate",
        "greece": "senate",
        "asia": "senate",
        "africa": "senate",
        "egypt": "neutral"
      }
    },
    {
      year: -49,
      title: "49 BC: Crossing the Rubicon",
      desc: "Caesar crosses the Rubicon (Jan). Pompey and the Senate flee Rome for Greece to gather an army. Caesar marches south, taking Italy, then pivots west to conquer Spain in a lightning campaign (Ilerda).",
      factions: {
        "gaul": "caesar",
        "italy": "caesar", // Caesar takes Italy
        "spain": "caesar", // Caesar takes Spain
        "greece": "senate",
        "asia": "senate",
        "africa": "senate",
        "egypt": "neutral"
      }
    },
    {
      year: -48,
      title: "48 BC: Pharsalus",
      desc: "Caesar crosses the Adriatic. Despite a setback at Dyrrachium, he crushes Pompey at the Battle of Pharsalus. Pompey flees to Egypt and is murdered by King Ptolemy XIII.",
      factions: {
        "gaul": "caesar",
        "italy": "caesar",
        "spain": "caesar",
        "greece": "caesar", // Caesar wins Greece
        "asia": "senate",
        "africa": "senate",
        "egypt": "contest" // Caesar arrives in Alexandria
      }
    },
    {
      year: -47,
      title: "47 BC: The East & Cleopatra",
      desc: "Caesar wins the Alexandrian War, installing Cleopatra as Queen. He marches through Asia Minor, defeating Pharnaces at Zela ('Veni, vidi, vici').",
      factions: {
        "gaul": "caesar",
        "italy": "caesar",
        "spain": "caesar",
        "greece": "caesar",
        "asia": "caesar", // Caesar secures Asia
        "africa": "senate", // Senate regroups here
        "egypt": "client" // Cleopatra allied to Caesar
      }
    },
    {
      year: -46,
      title: "46 BC: Thapsus",
      desc: "Caesar invades Africa. He destroys the Optimates' army at Thapsus. Cato the Younger commits suicide. Caesar returns to Rome for a quadruple triumph.",
      factions: {
        "gaul": "caesar",
        "italy": "caesar",
        "spain": "senate", // Pompey's sons revolt in Spain
        "greece": "caesar",
        "asia": "caesar",
        "africa": "caesar", // Africa taken
        "egypt": "client"
      }
    },
    {
      year: -45,
      title: "45 BC: Munda & The End",
      desc: "The final battle. Caesar defeats the sons of Pompey at Munda in Spain. The Civil War is effectively over. Caesar is made Dictator Perpetuo.",
      factions: {
        "gaul": "caesar",
        "italy": "caesar",
        "spain": "caesar", // Revolt crushed
        "greece": "caesar",
        "asia": "caesar",
        "africa": "caesar",
        "egypt": "client"
      }
    },
    {
      year: -44,
      title: "44 BC: The Ides of March",
      desc: "Caesar prepares for a campaign against Parthia, but is assassinated on March 15th by Brutus, Cassius, and others. Chaos returns.",
      factions: {
        "gaul": "caesar",
        "italy": "neutral", // Chaos
        "spain": "caesar",
        "greece": "neutral",
        "asia": "neutral",
        "africa": "caesar",
        "egypt": "client"
      }
    }
  ];

  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];
  
  const colors = {
    caesar: "#C8102E", // Roman Red
    senate: "#2D5054", // Pompey Blue/Green
    neutral: "#d4d4bc", // Map/Land color
    contest: "#E87EA1", // Conflict pink
    client:  "#98586c"  // Purple for Cleopatra
  };

  function getFill(provinceId) {
    const owner = currentData.factions[provinceId];
    return colors[owner] || "#d4d4bc";
  }
</script>

<main>
  <div class="layout">
    <div class="map-container">
      <!-- 
        IMPROVED MAP:
        This SVG uses stylized paths that actually resemble the Mediterranean.
        ViewBox 0 0 1000 600
      -->
      <svg viewBox="0 0 1000 600" class="interactive-map">
        <!-- Sea Background -->
        <rect width="1000" height="600" fill="#7FAac0" />

        <!-- 1. HISPANIA (Spain) -->
        <path 
          d="M 50,350 L 180,330 L 220,400 L 150,480 L 80,450 Z" 
          fill={getFill('spain')} 
          class="province"
        />
        <text x="100" y="400" class="label">HISPANIA</text>

        <!-- 2. GALLIA (France) -->
        <path 
          d="M 180,330 L 220,180 L 380,160 L 400,280 L 320,320 L 220,400 Z" 
          fill={getFill('gaul')} 
          class="province"
        />
        <text x="280" y="240" class="label">GALLIA</text>

        <!-- 3. ITALIA (The Boot) -->
        <path 
          d="M 320,320 L 400,280 L 450,280 L 520,380 L 480,450 L 420,350 Z" 
          fill={getFill('italy')} 
          class="province"
        />
        <text x="440" y="340" class="label">ITALIA</text>

        <!-- 4. GRAECIA (Greece/Macedonia) -->
        <path 
          d="M 500,300 L 600,290 L 620,400 L 520,420 L 520,380 Z" 
          fill={getFill('greece')} 
          class="province"
        />
        <text x="540" y="350" class="label">GRAECIA</text>

        <!-- 5. ASIA (Turkey) -->
        <path 
          d="M 620,290 L 800,280 L 820,380 L 650,400 L 620,400 Z" 
          fill={getFill('asia')} 
          class="province"
        />
        <text x="700" y="340" class="label">ASIA</text>

        <!-- 6. AFRICA (Tunisia/Libya) -->
        <path 
          d="M 200,500 L 550,480 L 550,600 L 200,600 Z" 
          fill={getFill('africa')} 
          class="province"
        />
        <text x="350" y="550" class="label">AFRICA</text>

        <!-- 7. AEGYPTUS (Egypt) -->
        <path 
          d="M 600,450 L 800,450 L 800,600 L 600,600 Z" 
          fill={getFill('egypt')} 
          class="province"
        />
        <text x="680" y="520" class="label">AEGYPTUS</text>
      </svg>
      
      <div class="timeline-area">
        <div class="timeline-controls">
           <button on:click={() => year = Math.max(-50, year - 1)}>&larr;</button>
           <input type="range" min="-50" max="-44" step="1" bind:value={year} />
           <button on:click={() => year = Math.min(-44, year + 1)}>&rarr;</button>
        </div>
        <div class="year-display">{Math.abs(year)} BC</div>
      </div>
    </div>

    <div class="info-panel">
      <div class="header">
        <h1>CAESAR'S CIVIL WAR</h1>
        <h3>Mapping the Republic's Fall</h3>
      </div>
      
      <div class="content">
        <h2 class="year-title">{currentData.title}</h2>
        <p>{currentData.desc}</p>
        
        <div class="legend">
          <h4>Key Factions</h4>
          <div class="legend-item"><span style="background:{colors.caesar}"></span> Caesar</div>
          <div class="legend-item"><span style="background:{colors.senate}"></span> Pompey / Senate</div>
          <div class="legend-item"><span style="background:{colors.client}"></span> Egyptian Client</div>
          <div class="legend-item"><span style="background:{colors.neutral}"></span> Neutral / Contested</div>
        </div>
      </div>

      <div class="footer">
        <p>Built for Prof. Westall | 2026</p>
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: "Garamond", "Georgia", serif; background-color: #2b2b2b; color: #f4f4e8; }
  
  .layout { display: flex; height: 100vh; width: 100vw; overflow: hidden; }
  
  .map-container { 
    flex: 3; 
    position: relative; 
    background: #7FAac0; /* Sea Color */
    display: flex; 
    flex-direction: column; 
    border-right: 4px solid #1a1a1a;
  }

  .interactive-map { width: 100%; height: 100%; }
  
  .province {
    stroke: #3e3e3e;
    stroke-width: 2px;
    transition: fill 0.8s ease; /* Smooth color transition */
    cursor: pointer;
  }
  .province:hover { opacity: 0.9; stroke: white; }

  .label { 
    fill: rgba(255,255,255,0.8); 
    font-family: "Trajan Pro", "Cinzel", serif; 
    font-size: 16px; 
    font-weight: bold; 
    pointer-events: none; 
    text-shadow: 1px 1px 4px black;
    text-anchor: middle;
  }

  .timeline-area { 
    background: #1a1a1a; 
    padding: 15px; 
    display: flex; 
    flex-direction: column;
    align-items: center; 
    color: #f4f4e8; 
    height: 15%;
    border-top: 2px solid #444;
  }

  .timeline-controls {
    display: flex;
    align-items: center;
    width: 80%;
    margin-bottom: 10px;
  }

  button {
    background: none;
    border: 1px solid #f4f4e8;
    color: #f4f4e8;
    cursor: pointer;
    padding: 5px 10px;
    border-radius: 4px;
  }
  button:hover { background: #f4f4e8; color: #1a1a1a; }

  input[type=range] { flex-grow: 1; margin: 0 15px; cursor: pointer; accent-color: #C8102E; }
  
  .year-display { 
    font-size: 2.5rem; 
    font-weight: bold; 
    color: #C8102E;
    letter-spacing: 2px;
  }

  .info-panel { 
    flex: 1; 
    background: #f4f4e8; /* Paper color */
    color: #2b2b2b;
    padding: 40px; 
    display: flex; 
    flex-direction: column; 
    box-shadow: -10px 0 20px rgba(0,0,0,0.5);
    overflow-y: auto;
  }

  .header h1 { color: #C8102E; margin: 0; font-size: 2rem; border-bottom: 2px solid #C8102E; padding-bottom: 10px; }
  .header h3 { margin-top: 5px; color: #555; font-style: italic; }

  .content { margin-top: 30px; flex-grow: 1; }
  .year-title { font-size: 1.8rem; margin-bottom: 10px; color: #2D5054; }
  p { font-size: 1.1rem; line-height: 1.6; }

  .legend { margin-top: 40px; background: #e0e0d0; padding: 20px; border-radius: 4px; border: 1px solid #ccc; }
  .legend h4 { margin-top: 0; text-transform: uppercase; font-size: 0.9rem; color: #666; }
  .legend-item { display: flex; align-items: center; margin-bottom: 8px; font-weight: bold; font-size: 0.9rem; }
  .legend-item span { display: block; width: 15px; height: 15px; margin-right: 10px; border: 1px solid #555; }

  .footer { margin-top: auto; font-size: 0.8rem; color: #888; text-align: center; border-top: 1px solid #ccc; padding-top: 20px; }

  @media (max-width: 900px) { .layout { flex-direction: column; } .timeline-area { height: auto; } .map-container { height: 50vh; } }
</style>
