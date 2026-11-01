# Patent Figures — Mermaid Diagrams

---

## FIG. 1 — Overall System Architecture

```mermaid
graph TB
    subgraph LAYER1["Data Acquisition Layer"]
        API["Open-Meteo Weather API<br/>(Dindigul 10.36°N, 77.97°E)"]
        FALLBACK["Diurnal Solar Cycle<br/>Fallback Model"]
        CAM["Smartphone / Camera<br/>Leaf Image Capture"]
    end

    subgraph LAYER2["Pillar 1: Physics Engine"]
        PHYSICS["Psychrometric Thermodynamic<br/>Physics Engine"]
        MAGNUS["Magnus-Tetens Es Equation"]
        VPD_CALC["VPD / Dew Point Calculator"]
        BUFFER["Solar Radiation<br/>Thermal Buffer Model"]
        RISK_PHYS["Multi-Pathogen<br/>Epidemiological Risk Models"]
    end

    subgraph LAYER3["Pillar 2: Temporal Forecasting"]
        LSTM["Bi-LSTM + Multi-Head<br/>Self-Attention Network"]
        FEAT["9-Feature Engineering<br/>(168h Rolling Window)"]
        FORECAST["24h / 48h / 72h<br/>Risk Probability Output"]
    end

    subgraph LAYER4["Pillar 3: Dual Vision Engine"]
        YOLO["YOLOv11m Disease Classifier<br/>(6 classes, 99.74% acc)"]
        EFFNET["EfficientNet Growth Classifier<br/>(6 stages, 99.45% acc)"]
        GATE["Phenology-Gated<br/>Chemical Safety Mechanism"]
    end

    subgraph LAYER5["Pillar 4: Edge SLM Advisory"]
        RAG["ChromaDB Vector RAG<br/>(TNAU/ICAR Guidelines)"]
        SLM["Qwen2.5-3B GGUF<br/>Dr. Thangavelu SLM"]
        PROTOCOL["4-Part Mitigation Protocol<br/>(EN + Tamil)"]
    end

    subgraph LAYER6["Pillar 5: Closed-Loop Simulator"]
        ACTUATORS["Virtual Actuator Controls<br/>(Vents, Fans, Foggers,<br/>Shade, Heating)"]
        SIM["Thermodynamic<br/>Response Simulator"]
        VERIFY["Risk Reduction<br/>Verification"]
    end

    subgraph BACKEND["Backend & Frontend"]
        FASTAPI["FastAPI REST Server<br/>(Port 8000, 8 Endpoints)"]
        REACT["React 18 + Vite Dashboard<br/>(Port 3000)"]
        THREEJS["Three.js 3D WebGL<br/>Plant Digital Twin"]
    end

    API --> PHYSICS
    FALLBACK -.->|Offline| PHYSICS
    PHYSICS --> MAGNUS --> VPD_CALC --> BUFFER --> RISK_PHYS

    PHYSICS -->|Microclimate State| FEAT --> LSTM --> FORECAST
    CAM --> YOLO & EFFNET
    YOLO --> GATE
    EFFNET --> GATE

    GATE -->|Constrained Disease + Stage| RAG --> SLM --> PROTOCOL
    PHYSICS -->|Current T, RH, VPD| SIM
    ACTUATORS --> SIM --> VERIFY

    RISK_PHYS --> FASTAPI
    FORECAST --> FASTAPI
    GATE --> FASTAPI
    PROTOCOL --> FASTAPI
    VERIFY --> FASTAPI

    FASTAPI --> REACT --> THREEJS

    style LAYER1 fill:#1a1a2e,stroke:#e94560,color:#fff
    style LAYER2 fill:#16213e,stroke:#0f3460,color:#fff
    style LAYER3 fill:#1a1a2e,stroke:#e94560,color:#fff
    style LAYER4 fill:#16213e,stroke:#0f3460,color:#fff
    style LAYER5 fill:#1a1a2e,stroke:#e94560,color:#fff
    style LAYER6 fill:#16213e,stroke:#0f3460,color:#fff
    style BACKEND fill:#0f3460,stroke:#e94560,color:#fff
```

---

## FIG. 2 — Psychrometric Physics Engine Data Flow

```mermaid
graph LR
    subgraph INPUT["Outdoor Weather Input"]
        T_OUT["Outdoor Temp<br/>T_out (°C)"]
        RH_OUT["Outdoor RH<br/>RH_out (%)"]
        RAD["Solar Radiation<br/>R (W/m²)"]
        WIND["Wind Speed<br/>(km/h)"]
    end

    subgraph PSYCHRO["Psychrometric Calculations"]
        ES["Saturated Vapor Pressure<br/>Es = 0.61078 × exp(17.27T / (T+237.3))"]
        EA["Actual Vapor Pressure<br/>Ea = Es × (RH/100)"]
        VPD["Vapor Pressure Deficit<br/>VPD = Es - Ea (kPa)"]
        DEW["Dew Point Temperature<br/>Tdew = 237.7α / (17.27 - α)"]
    end

    subgraph THERMAL["Thermal Buffer Model"]
        SOLAR["Solar Factor<br/>f = min(1.0, R/1000)"]
        T_IN["Indoor Temperature<br/>T_in = T_out + 4.5 × f"]
        RH_IN["Indoor Humidity<br/>RH_in = RH_out + δ_RH"]
        CO2["CO₂ Dynamics<br/>Day: 450 ppm / Night: 650 ppm"]
        PAR["PAR Conversion<br/>PAR = R × 2.1 μmol/m²/s"]
        WETNESS["Leaf Wetness<br/>W = min(8, max(0, (RH-80)×0.3))"]
    end

    subgraph ASSESSMENT["Assessment & Output"]
        VPD_ZONE["VPD 5-Zone Classification"]
        ZONE1["< 0.40 kPa: CRITICAL<br/>Condensation / Disease"]
        ZONE2["0.40–0.79: WARNING<br/>Under-Transpiration"]
        ZONE3["0.80–1.20: OPTIMAL<br/>Peak Efficiency"]
        ZONE4["1.21–1.60: MODERATE<br/>Mild Stress"]
        ZONE5["> 1.60: CRITICAL<br/>Severe Desiccation"]
        PATHOGENS["6-Pathogen Risk Scoring<br/>Late Blight | Early Blight<br/>Leaf Mold | Powdery Mildew<br/>Septoria | Spider Mites"]
    end

    T_OUT & RH_OUT --> ES --> EA --> VPD & DEW
    RAD --> SOLAR --> T_IN & PAR
    T_OUT --> T_IN
    RH_OUT --> RH_IN
    T_IN & RH_IN --> VPD
    VPD --> VPD_ZONE
    VPD_ZONE --> ZONE1 & ZONE2 & ZONE3 & ZONE4 & ZONE5
    T_IN & RH_IN & WETNESS --> PATHOGENS

    style INPUT fill:#0d1b2a,stroke:#1b263b,color:#fff
    style PSYCHRO fill:#1b263b,stroke:#415a77,color:#fff
    style THERMAL fill:#1b263b,stroke:#415a77,color:#fff
    style ASSESSMENT fill:#0d1b2a,stroke:#e0e1dd,color:#fff
```

---

## FIG. 3 — Operational Method Flowchart

```mermaid
flowchart TD
    START(["System Start"]) --> S101

    S101["S101: Meteorological Data Acquisition<br/>Fetch outdoor T, RH, R, Wind from<br/>Open-Meteo API (15-min cache)<br/>OR activate diurnal fallback"]

    S101 --> S102["S102: Physics-Based Indoor Computation<br/>Apply Magnus-Tetens, solar buffer model,<br/>transpiration dynamics to compute:<br/>Indoor T, RH, VPD, Dew Point,<br/>CO₂, PAR, Leaf Wetness"]

    S102 --> S103["S103: VPD Assessment & Risk Scoring<br/>Classify VPD into 5 physiological zones<br/>Run 6-pathogen epidemiological models<br/>Generate real-time risk alerts"]

    S103 --> S104["S104: Temporal Deep Learning Forecasting<br/>Extract 9 features from 168h buffer<br/>Standardise with fitted scaler<br/>Feed X ∈ R^(168×9) through Bi-LSTM<br/>Emit p_24h, p_48h, p_72h probabilities"]

    S104 --> CHECK{Leaf Image<br/>Available?}

    CHECK -->|Yes| S105["S105: Dual Vision + Phenology Gating<br/>YOLOv11m → Disease class + confidence<br/>EfficientNet → Growth stage<br/>Apply PHI safety gate:<br/>Block long-PHI chemicals if Harvest stage"]
    CHECK -->|No| S107

    S105 --> S106["S106: RAG-Augmented SLM Prescription<br/>Construct semantic query from diagnosis<br/>Retrieve TNAU/ICAR passages (ChromaDB)<br/>Generate Dr. Thangavelu 4-part protocol:<br/>① Actuator ② Chemical ③ Bio ④ Tamil"]

    S106 --> S107["S107: Closed-Loop Actuator Simulation<br/>Input virtual actuator states<br/>Calculate thermodynamic response:<br/>ΔT, ΔRH, ΔVPD<br/>Verify risk reduction before activation"]

    S107 --> S108["S108: 3D Twin Visualisation Sync<br/>Update Three.js mesh materials<br/>HSL interpolation: Green → Yellow → Brown<br/>Activate bioluminescent pulse if risk > 65%"]

    S108 --> LOOP(["Continuous Loop<br/>(Poll every 60s)"])
    LOOP --> S101

    style START fill:#2d6a4f,stroke:#40916c,color:#fff
    style LOOP fill:#2d6a4f,stroke:#40916c,color:#fff
    style CHECK fill:#9b2226,stroke:#ae2012,color:#fff
    style S101 fill:#003049,stroke:#669bbc,color:#fff
    style S102 fill:#003049,stroke:#669bbc,color:#fff
    style S103 fill:#003049,stroke:#669bbc,color:#fff
    style S104 fill:#003049,stroke:#669bbc,color:#fff
    style S105 fill:#780000,stroke:#c1121f,color:#fff
    style S106 fill:#780000,stroke:#c1121f,color:#fff
    style S107 fill:#003049,stroke:#669bbc,color:#fff
    style S108 fill:#003049,stroke:#669bbc,color:#fff
```

---

## FIG. 4 — Bi-LSTM + Multi-Head Self-Attention Architecture

```mermaid
graph TD
    subgraph INPUT_LAYER["Input Layer"]
        INPUT["Input Tensor<br/>X ∈ R^(1 × 168 × 9)<br/><br/>9 Features:<br/>temp, humidity, vpd, vpd_risk,<br/>leaf_wetness, rolling_wet_24h,<br/>temp_swing_24h, dew_exceedance,<br/>co2_ppm_norm"]
    end

    subgraph SCALER["Preprocessing"]
        STD["StandardScaler<br/>(Pre-fitted feature_scaler.pkl)"]
    end

    subgraph BILSTM["Bidirectional LSTM (2 Layers)"]
        FWD["Forward LSTM<br/>hidden_size = 128<br/>2 layers, dropout = 0.2"]
        BWD["Backward LSTM<br/>hidden_size = 128<br/>2 layers, dropout = 0.2"]
        CONCAT["Concatenate<br/>H ∈ R^(168 × 256)"]
    end

    subgraph ATTENTION["Multi-Head Self-Attention"]
        MHA["MultiheadAttention<br/>embed_dim = 256<br/>num_heads = 4<br/>dropout = 0.2<br/><br/>Q = K = V = H"]
        LAST["Extract Last<br/>Timestep t=168"]
        LN["LayerNorm(256)"]
    end

    subgraph FFN["Feed-Forward Projection Head"]
        FC1["Linear(256 → 128)"]
        RELU["ReLU Activation"]
        DROP["Dropout(0.2)"]
        FC2["Linear(128 → 3)"]
        SIGMOID["Sigmoid Activation"]
    end

    subgraph OUTPUT_LAYER["Output"]
        OUT["Risk Probabilities<br/>y = [p_24h, p_48h, p_72h]<br/>Each ∈ [0.0, 1.0]<br/><br/>Total params: 4,884,483"]
    end

    INPUT --> STD --> FWD & BWD
    FWD --> CONCAT
    BWD --> CONCAT
    CONCAT --> MHA --> LAST --> LN
    LN --> FC1 --> RELU --> DROP --> FC2 --> SIGMOID --> OUT

    style INPUT_LAYER fill:#14213d,stroke:#fca311,color:#fff
    style SCALER fill:#14213d,stroke:#e5e5e5,color:#fff
    style BILSTM fill:#003566,stroke:#ffc300,color:#fff
    style ATTENTION fill:#003566,stroke:#ffc300,color:#fff
    style FFN fill:#14213d,stroke:#fca311,color:#fff
    style OUTPUT_LAYER fill:#14213d,stroke:#fca311,color:#fff
```

---

## FIG. 5 — Phenology-Gated Dual Vision Pipeline

```mermaid
flowchart TD
    IMG["RGB Tomato Leaf Image<br/>(Upload / Camera Capture)"] --> RESIZE["Resize to 224 × 224<br/>Convert to RGB Tensor"]

    RESIZE --> STREAM1 & STREAM2

    subgraph STREAM1["Stream 1: Disease Classification"]
        YOLO["YOLOv11m Classifier<br/>(H200 Trained, 20.9 MB)"]
        DISEASE["Disease Prediction<br/>+ Confidence Score"]
        DIST["Full Probability Distribution<br/>over 6 Classes"]
    end

    subgraph STREAM2["Stream 2: Growth Stage Classification"]
        NORM["ImageNet Normalisation<br/>Mean=[0.485, 0.456, 0.406]<br/>Std=[0.229, 0.224, 0.225]"]
        EFFNET["EfficientNet-B0/B4<br/>(H200 Trained, 16.4 MB)"]
        STAGE["Growth Stage Prediction<br/>+ Confidence Score"]
    end

    YOLO --> DISEASE --> DIST
    NORM --> EFFNET --> STAGE

    DISEASE --> GATE_LOGIC
    STAGE --> GATE_LOGIC

    subgraph GATE_LOGIC["Phenology-Gated Safety Mechanism"]
        CHECK_FLOWER{"Stage =<br/>Flowering?"}
        CHECK_HARVEST{"Stage =<br/>Harvest / Ripe?"}

        WARN_FLOWER["⚠️ FLOWER SAFETY<br/>Block daytime spraying<br/>Ban Cu-based phytotoxic sprays<br/>Mandate dawn/dusk application only"]
        BLOCK_CHEM["🚫 HARVEST SAFETY<br/>Block ALL chemicals<br/>with PHI > 3 days<br/>(Metalaxyl, Mancozeb blocked)"]
        ALLOW["✅ STANDARD MODE<br/>Full chemical + bio-control<br/>recommendations permitted"]
        SUBSTITUTE["♻️ SUBSTITUTE<br/>Zero-PHI alternatives:<br/>• Trichoderma viride @ 5g/L<br/>• K-bicarbonate @ 3g/L<br/>• Cu-hydroxide (PHI 1 day)"]
    end

    CHECK_FLOWER -->|Yes| WARN_FLOWER
    CHECK_FLOWER -->|No| CHECK_HARVEST
    CHECK_HARVEST -->|Yes| BLOCK_CHEM --> SUBSTITUTE
    CHECK_HARVEST -->|No| ALLOW

    WARN_FLOWER --> OUTPUT_VISION
    SUBSTITUTE --> OUTPUT_VISION
    ALLOW --> OUTPUT_VISION

    OUTPUT_VISION["Gated Output → SLM Advisory Engine"]

    style IMG fill:#264653,stroke:#2a9d8f,color:#fff
    style STREAM1 fill:#e76f51,stroke:#f4a261,color:#fff
    style STREAM2 fill:#264653,stroke:#2a9d8f,color:#fff
    style GATE_LOGIC fill:#6d2e46,stroke:#d5573b,color:#fff
    style OUTPUT_VISION fill:#2a9d8f,stroke:#264653,color:#fff
    style WARN_FLOWER fill:#e9c46a,stroke:#f4a261,color:#000
    style BLOCK_CHEM fill:#e76f51,stroke:#264653,color:#fff
    style ALLOW fill:#2a9d8f,stroke:#264653,color:#fff
    style SUBSTITUTE fill:#2a9d8f,stroke:#264653,color:#fff
```

---

## FIG. 6 — Edge SLM + RAG Advisory Architecture

```mermaid
flowchart TD
    subgraph INPUTS["Diagnostic Inputs"]
        DIS["Disease: Late Blight<br/>Confidence: 98.4%"]
        STG["Growth Stage: Flowering"]
        TELEM["Microclimate Telemetry<br/>T=19°C | RH=94% | VPD=0.08 kPa<br/>Leaf Wetness: 3.0 hrs"]
    end

    DIS & STG & TELEM --> QUERY["Semantic Query Construction<br/>'Late Blight management Flowering stage<br/>temp 19°C humidity 94% VPD 0.08 kPa'"]

    subgraph RAG_PIPELINE["Retrieval-Augmented Generation Pipeline"]
        EMBED["Sentence Transformer Embedding<br/>all-MiniLM-L6-v2<br/>(384-dim dense vectors)"]
        CHROMA["ChromaDB Persistent Vector DB<br/>Collection: tomato_agronomy_knowledge<br/><br/>6 Indexed Documents:<br/>① TNAU Late Blight Protocol<br/>② TNAU Early Blight Protocol<br/>③ ICAR TYLCV Management<br/>④ TNAU Bacterial Spot Protocol<br/>⑤ TNAU Leaf Mold Protocol<br/>⑥ VPD Optimisation Guide"]
        COSINE["Cosine Similarity Search<br/>Top-k (k=2 to 3) passages"]
        PASSAGES["Retrieved Authoritative Passages<br/>'Metalaxyl 8% + Mancozeb 64% WP<br/>@ 2.5 g/L water. PHI: 7 days...'"]
    end

    QUERY --> EMBED --> CHROMA --> COSINE --> PASSAGES

    subgraph FALLBACK["Built-in Fallback"]
        BUILTIN["BUILTIN_KNOWLEDGE Dictionary<br/>(8 disease protocols + dosages)<br/>Activated if ChromaDB offline"]
    end

    CHROMA -.->|If unavailable| BUILTIN

    subgraph SLM_ENGINE["Edge SLM Inference Engine"]
        PROMPT["Structured Prompt Assembly<br/>[TELEMETRY REPORT] + [TNAU/ICAR GUIDELINES]<br/>+ Dr. Thangavelu persona instructions"]
        MODEL["Qwen2.5-3B-Instruct<br/>GGUF Q4_K_M (1.93 GB)<br/>via Ollama localhost:11434<br/>temp=0.2 | top_p=0.9"]
        EXPERT["Expert Deterministic Fallback<br/>EXPERT_PROTOCOLS +<br/>TAMIL_TRANSLATIONS<br/>(Activated if Ollama offline)"]
    end

    PASSAGES --> PROMPT
    QUERY --> PROMPT --> MODEL
    MODEL -.->|If Ollama offline| EXPERT

    subgraph OUTPUT["4-Part Adaptive Mitigation Protocol"]
        P1["① ⚙️ Actuator Automation<br/>Open roof vents 80%, exhaust fans ON<br/>Target VPD > 0.9 kPa"]
        P2["② 🧪 Chemical Intervention<br/>Cymoxanil 8% + Mancozeb 64% WP<br/>@ 2.0 g/L | PHI: 5 days<br/>PPE: N95 mask, nitrile gloves"]
        P3["③ 🍃 Biological Control<br/>Trichoderma viride @ 5 g/L<br/>(Do NOT mix with chemicals)"]
        P4["④ 🌾 Tamil Farmer Summary<br/>தாமதமான இலை கருகல் நோய்<br/>ரிடோமில் 2.5 g/L தெளிக்கவும்<br/>பூக்கள் உள்ளதால் மாலையில் தெளிக்கவும்"]
    end

    MODEL --> P1 & P2 & P3 & P4
    EXPERT --> P1 & P2 & P3 & P4

    style INPUTS fill:#1d3557,stroke:#457b9d,color:#fff
    style RAG_PIPELINE fill:#2b2d42,stroke:#8d99ae,color:#fff
    style FALLBACK fill:#6c584c,stroke:#a98467,color:#fff
    style SLM_ENGINE fill:#2b2d42,stroke:#8d99ae,color:#fff
    style OUTPUT fill:#1d3557,stroke:#e63946,color:#fff
    style P1 fill:#457b9d,stroke:#1d3557,color:#fff
    style P2 fill:#e63946,stroke:#1d3557,color:#fff
    style P3 fill:#2a9d8f,stroke:#1d3557,color:#fff
    style P4 fill:#e9c46a,stroke:#1d3557,color:#000
```
