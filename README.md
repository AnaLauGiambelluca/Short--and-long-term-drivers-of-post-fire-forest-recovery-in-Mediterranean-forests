# Short- and Long-Term Drivers of Post-Fire Forest Recovery in Mediterranean Forests


This folder has all the code implemented in the article called "Short- and Long-Term Drivers of Post-Fire Forest Recovery in Mediterranean Forests" written by Ana Laura Giambelluca *(1), Txomin Hermosilla (2), María González-Audícana (1), Jesús Álvarez-Mozos (1)

(1) Institute on Innovation and Sustainable Development in Food Chain (IS-FOOD), Dep. Engineering, Public University of Navarre (UPNA), Campus de Arrosadia, 31006 Pamplona, Spain.
(2) Canadian Forest Service (Pacific Forestry Centre), 506 Burnside Rd W, Victoria, BC V8Z 1M6, Canada.

*Corresponding author: analaura.giambelluca@unavarra.es


[Scripts](Code)

[Reference layers used](Layers)

```mermaid
graph TD
    %% Phase 1: Forest Masking & Preprocessing
    subgraph Phase 1: Forest Masking & Preprocessing
        A1[(Pre-fire Forest Map)] & A2[(Post-fire Forest Map)] --> B[Intersection]
        B --> C[Code 00: Homogenize Labels & Filter Stable Forest Cover <br/> <i>Python</i>]
        C --> D[(Stable Forest Map)]
        D --> E[QGIS: Rasterize, Extract Centroids & Filter Pine/Oak Species]
        E --> F[(Target Forest Points)]
    end

    %% Phase 2: Remote Sensing & Trajectories
    subgraph Phase 2: Remote Sensing & Trajectories
        G[(Fire Layer)] --> H[Code 01: CCDC Algorithm Implementation <br/> <i>GEE</i>]
        H --> I[(CCDC Raster: Magnitude, Probability, Coefficients)]
        
        I & F --> J[Code 02: Extract CCDC Coefficients <br/> <i>Python</i>]
        J --> K[(CSV: Pixel Coefficients)]
        
        F --> L[Code 03: Extract Mean Pre-fire Summer NBR <br/> <i>GEE</i>]
        L --> M[(CSV: Pre-fire NBR)]
        
        K & M --> N[Code 04: Filter NBR < 0.4 & Calculate Recovery Rates <br/> <i>Python</i>]
        N --> O[(Points with Recovery Rates)]
        
        O --> P[Code 05: Spatial Filtering - Min 100m Distance <br/> <i>RStudio - spatialEco</i>]
        P --> Q[(Spatially Filtered Points)]
    end

    %% Phase 3: Climate & Topography Integration
    subgraph Phase 3: Environmental Data Integration
        R[(ERA5 Dataset)] --> S[Code 06: Extract Climate Variables <br/> Precip, Tmax, Tmin, SPEI6 - <i>Python</i>]
        S --> T[(9km Climate Points)]
        
        T & Q --> U[QGIS: Cubic Interpolation of Climate Variables]
        U --> V[Code 07: Condition Climate Data <br/> 5yr & 20yr Growing Season Means <br/> <i>Python</i>]
        V --> W[(Conditioned Climate Data)]
        
        X[(ASTER DEM)] --> Y[QGIS: Extract Topographic Variables <br/> Elevation, Slope, Aspect]
        
        W & Y & Q & I & M --> Z[QGIS: Join Layers <br/> Combine Climate, Topography, Magnitude, NBR & Species]
        Z --> AA[(Final Unified Dataset)]
    end

    %% Phase 4: Statistical Analysis
    subgraph Phase 4: Statistical Analysis & Modeling
        AA --> AB[Code 08: Statistical Analysis <br/> <i>Python / R</i>]
        AB --> AC[Correlations & VIF Calculation]
        AB --> AD[10 Random Forest Models <br/> Short/Long-term & Severity-stratified]
        AB --> AE[Partial Dependence Plots PDPs]
    end
