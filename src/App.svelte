<script>
  // We use negative numbers for BC dates (-49 = 49 BC)
  let year = -50; 
  
  // --- REAL DATA EXTRACTED FROM WIKIPEDIA ---
  const historicalData = [
    {
      year: -50,
      title: "50 BC: The Standoff",
      desc: "Background: Caesar finishes the Gallic Wars. The Senate (Optimates), fearful of his power, demands he disband his army. Pompey aligns with the Senate.",
      factions: {
        "gallia": "caesar",
        "hispania": "senate",
        "italia": "senate",
        "graecia": "senate",
        "asia": "senate",
        "africa": "senate",
        "aegyptus": "neutral"
      }
    },
    {
      year: -49,
      title: "49 BC: Rubicon & Ilerda",
      desc: "Jan: Caesar crosses the Rubicon. Pompey flees Italy for Greece. Aug: Caesar marches to Spain (Battle of Ilerda) and defeats Pompey's legates.",
      factions: {
        "gallia": "caesar",
        "hispania": "caesar", // Won at Ilerda
        "italia": "caesar",   // Taken after Pompey fled
        "graecia": "senate",  // Pompey regroups here
        "asia": "senate",
        "africa": "senate",   // Curio defeated by Juba I
        "aegyptus": "neutral"
      }
    },
    {
      year: -48,
      title: "48 BC: Pharsalus",
      desc: "The Decisive Battle. Caesar invades Greece. Despite a setback at Dyrrachium, he crushes Pompey at Pharsalus. Pompey flees to Egypt and is assassinated.",
      factions: {
        "gallia": "caesar",
        "hispania": "caesar",
        "italia": "caesar",
        "graecia": "caesar",  // Won at Pharsalus
        "asia": "senate",
        "africa": "senate",
        "aegyptus": "contest" // Caesar arrives, intervenes in dynastic war
      }
    },
    {
      year: -47,
      title: "47 BC: The East (Zela)",
      desc: "Caesar secures Egypt for Cleopatra. He marches north to Asia Minor and defeats Pharnaces at Zela ('Veni, Vidi, Vici'). The Senate regroups in Africa.",
      factions: {
        "gallia": "caesar",
        "hispania": "caesar", // Governor Cassius Longinus unpopular
        "italia": "caesar",
        "graecia": "caesar",
        "asia": "caesar",     // Won at Zela
        "africa": "senate",   // Metellus Scipio & Cato
        "aegyptus": "client"  // Cleopatra secured
      }
    },
    {
      year: -46,
      title: "46 BC: Thapsus",
      desc: "Caesar invades Africa. He destroys the Optimates' army at Thapsus. Cato the Younger commits suicide at Utica. Juba's Numidia is annexed.",
      factions: {
        "gallia": "caesar",
        "hispania": "senate", // Revolt by Pompey's sons
        "italia": "caesar",
        "graecia": "caesar",
        "asia": "caesar",
        "africa": "caesar",   // Won at Thapsus
        "aegyptus": "client"
      }
    },
    {
      year: -45,
      title: "45 BC: Munda",
      desc: "The Final Battle. Caesar returns to Spain and defeats Gnaeus Pompey at Munda. The Civil War is effectively over. Caesar is Dictator Perpetuo.",
      factions: {
        "gallia": "caesar",
        "hispania": "caesar", // Won at Munda
        "italia": "caesar",
        "graecia": "caesar",
        "asia": "caesar",
        "africa": "caesar",
        "aegyptus": "client"
      }
    },
    {
      year: -44,
      title: "44 BC: The Ides of March",
      desc: "Caesar is assassinated on March 15th by Brutus, Cassius, and others. The Republic falls into chaos once more.",
      factions: {
        "gallia": "caesar",
        "hispania": "caesar",
        "italia": "neutral", // Chaos
        "graecia": "neutral",
        "asia": "neutral",
        "africa": "caesar",
        "aegyptus": "client"
      }
    }
  ];

  // Reactive Logic: Updates immediately when 'year' changes
  $: currentData = historicalData.find(d => d.year === year) || historicalData[0];
  
  // COLOR PALETTE
  const colors = {
    caesar: "#991b1b",  // Deep Roman Red
    senate: "#1e3a8a",  // Pompeian Blue
    neutral: "#d4d4d8", // Grey
    contest: "#f59e0b", // Warning Orange
    client:  "#701a75"  // Royal Purple (Cleopatra)
  };

  function getFill(provinceId) {
    const owner = currentData.factions[provinceId];
    return colors[owner] || "#cccccc";
  }
</script>

<main>
  <div class="layout">
    <!-- MAP SECTION -->
    <div class="map-container">
      <!-- 
         Vector Map of the Mediterranean.
         Coordinates are simplified (Low Poly) to look like a real map without needing big files.
      -->
      <svg viewBox="0 0 1000 600" class="interactive-map">
        <!-- Sea -->
        <rect width="1000" height="600" fill="#93c5fd" />

        <!-- 1. HISPANIA (Spain/Portugal) -->
        <path 
          d="M 50,350 L 180,330 L 220,400 L 150,480 L 80,450 Z" 
          fill={getFill('hispania')} 
          class="province"
        />
        <text x="110" y="410" class="label">HISPANIA</text>

        <!-- 2. GALLIA (France/Belgium) -->
        <path 
          d="M 180,330 L 220,180 L 380,160 L 400,280 L 320,320 L 220,400 Z" 
          fill={getFill('gallia')} 
          class="province"
        />
        <text x="280" y="260" class="label">GALLIA</text>

        <!-- 3. ITALIA (The Boot) -->
        <path 
          d="M 320,320 L 400,280 L 450,280 L 520,380 L 480,450 L 420,350 Z" 
          fill={getFill('italia')} 
          class="province"
        />
        <text x="440" y="340" class="label">ITALIA</text>

        <!-- 4. GRAECIA (Greece/Macedonia) -->
        <path 
          d="M 500,300 L 600,290 L 630,400 L 520,430 L 520,380 Z" 
          fill={getFill('graecia')} 
          class="province"
        />
        <text x="560" y="360" class="label">GRAECIA</text>

        <!-- 5. ASIA (Turkey/Syria) -->
        <path 
          d="M 630,290 L 850,280 L 870,400 L 660,420 L 630,400 Z" 
          fill={getFill('asia')} 
          class="province"
        />
        <text x="750" y="350" class="label">ASIA</text>

        <!-- 6. AFRICA (Tunisia/Libya) -->
        <path 
          d="M 180,500 L 550,480 L 550,600 L 180,600 Z" 
          fill={getFill('africa')} 
          class="province"
        />
        <text x="350" y="550" class="label">AFRICA</text>

        <!-- 7. AEGYPTUS (Egypt) -->
        <path 
          d="M 600,450 L 870,450 L 870,600 L 600,600 Z" 
          fill={getFill('aegyptus')} 
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
        <div class="subtitle">Mapping the Republic's Fall</div>
      </div>
      
      <div class="content">
        <h2 class="year-title">{currentData.title}</h2>
        <p>{currentData.desc}</p>
        
        <div class="legend">
          <h3>Factions</h3>
          <div class="legend-item"><span style="background:#991b1b"></span> Caesar</div>
          <div class="legend-item"><span style="background:#1e3a8a"></span> Senate / Pompey</div>
          <div class="legend-item"><span style="background:#701a75"></span> Client Kingdom</div>
          <div class="legend-item"><span style="background:#f59e0b"></span> Contested Zone</div>
        </div>
      </div>

      <div class="footer">
        <p>Built with Svelte | Data from Wikipedia</p>
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) { margin: 0; padding: 0; font-family: 'Georgia', serif; background-color: #f3f4f6; color: #1f2937; }
  
  .layout { display: flex; height: 100vh; width: 100vw; overflow: hidden; }
  
  .map-container { 
    flex: 3; 
    position: relative; 
    background: #93c5fd; 
    display: flex; 
    flex-direction: column; 
    border-right: 5px solid #1f2937;
  }

  .interactive-map { width: 100%; height: 85%; }
  
  .province {
    stroke: #ffffff;
    stroke-width: 2px;
    transition: fill 0.8s ease-in-out; /* SMOOTH COLOR CHANGE */
    cursor: pointer;
  }
  .province:hover { opacity: 0.8; stroke-width: 4px; }

  .label { 
    fill: white; 
    font-family: sans-serif; 
    font-size: 14px; 
    font-weight: 900; 
    pointer-events: none; 
    text-shadow: 0px 0px 4px rgba(0,0,0,0.8);
    text-anchor: middle;
    letter-spacing: 2px;
  }

  .timeline-area { 
    background: #1f2937; 
    height: 15%;
    padding: 0 40px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    color: white;
  }

  input[type=range] { width: 100%; max-width: 600px; margin: 10px 0; accent-color: #991b1b; cursor: pointer; }
  
  .year-display { font-size: 2rem; font-weight: bold; color: #fca5a5; font-family: monospace; }
  
  .ticks { display: flex; justify-content: space-between; width: 100%; max-width: 600px; font-size: 0.8rem; color: #9ca3af; }

  .info-panel { 
    flex: 1; 
    min-width: 300px;
    background: #fdfbf7; /* Paper texture color */
    padding: 40px; 
    display: flex; 
    flex-direction: column; 
    box-shadow: -10px 0 25px rgba(0,0,0,0.1);
    overflow-y: auto;
  }

  .header h1 { color: #991b1b; margin: 0; font-size: 2.2rem; border-bottom: 4px solid #991b1b; padding-bottom: 15px; }
  .subtitle { margin-top: 10px; font-style: italic; color: #4b5563; }

  .content { margin-top: 40px; flex-grow: 1; }
  .year-title { font-size: 1.5rem; color: #1f2937; margin-bottom: 15px; }
  p { line-height: 1.8; font-size: 1.1rem; }

  .legend { margin-top: 40px; background: #e5e7eb; padding: 20px; border-radius: 8px; }
  .legend h3 { margin-top: 0; font-size: 1rem; text-transform: uppercase; color: #374151; }
  .legend-item { display: flex; align-items: center; margin-bottom: 8px; font-weight: bold; font-size: 0.9rem; }
  .legend-item span { display: block; width: 20px; height: 20px; margin-right: 12px; border: 1px solid #9ca3af; border-radius: 3px; }

  .footer { margin-top: auto; font-size: 0.8rem; color: #9ca3af; text-align: center; border-top: 1px solid #e5e7eb; padding-top: 20px; }

  @media (max-width: 1024px) { 
    .layout { flex-direction: column; overflow: auto; } 
    .map-container { height: 60vh; width: 100%; }
    .info-panel { width: auto; height: auto; }
  }
</style>
