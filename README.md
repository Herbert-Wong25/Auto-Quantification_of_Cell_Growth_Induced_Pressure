# Auto-Quantification of Bacterial Growth-Induced Pressure

A Python bio-image informatics pipeline that converts fluorescence microscopy of PDMS microfluidic chambers into time-resolved, absolute mechanical pressure measurements — quantifying the Growth-Induced Pressure (GIP) exerted by confined *E. coli* populations in **kPa**.

---

## 🔬 Scientific Context

When bacteria such as *E. coli* proliferate within confined spaces — host tissues, medical implants, or microfluidic chambers — they exert measurable physical forces on their surroundings. Quantifying this force *in situ* is technically challenging.

This pipeline overcomes this by using a PDMS microfluidic chamber as a **physical force sensor**. Fluorescently labelled chamber walls are segmented in Fiji and then analysed in Python: wall-to-wall separation is tracked over time, calibrated against known pneumatic pressures, and converted into absolute GIP values via an effective Young's modulus model — replicating the analysis of Figure 1D of Le Blanc *et al.* (2024).

---

## 🛠 Tech Stack & Dependencies

| Domain | Tool |
|---|---|
| Image I/O & segmentation | `scikit-image`, `Pillow` |
| Robust wall metrology | Linear resampling + one-to-one distance mapping |
| Morphological refinement | `scipy.ndimage` (`binary_closing`, `binary_dilation`) |
| Data processing & visualisation | `pandas`, `numpy`, `matplotlib`, `natsort` |

---

## 🚀 Pipeline Overview

### Part 1: Pressure-to-Deformation Calibration (`01_Calibration_Model.ipynb`)

Establishes the empirical relationship between applied pneumatic pressure (0–2000 mbar, 8 levels) and chamber-wall separation (µm) across 4 calibration datasets.

**Method:**
- Segmented masks exported from Fiji are pre-processed with morphological operations (`binary_closing` + `binary_dilation`, disk kernel radius = 3 px) to fill gaps and reduce noise in detected wall boundaries.
- Wall pixel coordinates are **linearly resampled** to equal point counts on both walls, enabling memory-efficient one-to-one distance mapping — avoiding the O(n²) pairwise distance matrix of a full `cdist` approach.
- Deformation (µm) and area change (%) are computed relative to the 0 mbar baseline and exported to CSV.

**Output:** Calibration curve (deformation vs pressure) + wall-resampling verification plots for all 4 datasets.

---

### Part 2: Growth-Induced Pressure Analysis (`02_Growth_Pressure_Analysis.ipynb`)

Applies the same wall-tracking pipeline to time-lapse chamber images of *E. coli* growth (30-min frame interval, 5 chambers), quantifying GIP over ~20 hours.

**Method:**
- **X-axis analysis:** Images are cropped to the central region (y = [100, 300] px) to focus on horizontal wall separation; size filtering (≥ 700 px²) suppresses small segmentation artefacts.
- **Y-axis analysis (Chamber-1):** Crops the left region (x = [100, 200] px) to isolate the top and bottom walls, independently measuring vertical separation. This verifies the **isotropic deformation assumption**: αy = ΔLy/ΔLx = **0.98**, confirming near-equal deformation in both lateral directions.
- **GIP calculation:** Horizontal strain is multiplied directly by the effective Young's modulus. Because the proportionality between 1D and 3D strain is encoded in Eeff itself, no separate 3D conversion is required (Report §3.3):

$$\text{GIP} = E_{\text{eff}} \cdot \frac{\Delta L_x}{L_{x,0}}$$

**Output:** GIP vs Time plots for all 5 chambers; CSV export.

---

## 📊 Key Quantitative Results

### Effective Young's Modulus

Calibration strain data (σ = Eeff · ΔLx/Lx) yield a linear fit with R² = 0.9887:

$$E_{\text{eff}} = 4.337 \text{ MPa}$$

### Full 3D Young's Modulus (for reference)

Using chamber dimensions (Lx = 31.21 µm, Ly = 19.12 µm, Lz = 2.3 µm) and deformation ratios (αy = 0.98, αz = 1, αNL = 0.92):

$$E = \alpha_{NL} \cdot \frac{E_{\text{eff}}}{\dfrac{1}{2} + \dfrac{1}{2} \cdot \alpha_{y} \cdot \dfrac{L_{y}}{L_{x}} + \alpha_{z} \cdot \dfrac{L_{x}}{L_{z}}} = 268.34 \text{ kPa}$$

*(Comparable to 282.6 kPa reported in Le Blanc et al. Supp. Fig. 1G.)*

### GIP Kinetics — Biological Interpretation

- **Two-phase growth profile:** Chambers 1, 4, and 5 show near-zero GIP for ~5 hours (free growth phase), followed by a rapid pressure increase. Chambers 2 and 3 show a shorter lag (~3 h) before rising.
- **Plateau:** All five chambers converge to a maximum GIP of approximately **250–300 kPa**, suggesting the colony actively regulates growth rate as pressure approaches levels that compromise bacterial membrane integrity.
- **Reproducibility:** Final GIP values are within ~50 kPa across all five independent chambers, confirming the robustness of the measurement pipeline and the consistency of the mechanobiological response.

---

## 📂 Project Structure

```
.
├── notebooks/
│   ├── 01_Calibration_Model.ipynb          # Pressure calibration pipeline
│   └── 02_Growth_Pressure_Analysis.ipynb   # GIP quantification & plots
├── data/
│   ├── processed/masks/
│   │   ├── calibration/   # Cal-1 to Cal-4 segmented masks (8 frames each)
│   │   └── chambers/      # Chamber-1 to Chamber-5 segmented masks
│   └── results/           # Output CSVs and GIP plots
└── requirements.txt
```

---

## ⚙️ Setup & Installation

```bash
git clone https://github.com/Herbert-Wong25/Auto-Quantification_of_Cell_Growth_Induced_Pressure.git
cd Auto-Quantification_of_Cell_Growth_Induced_Pressure

conda create -n gip_analysis python=3.10
conda activate gip_analysis

pip install numpy pandas scipy scikit-image Pillow matplotlib natsort
```

Then run the notebooks in order: `01_Calibration_Model.ipynb` → `02_Growth_Pressure_Analysis.ipynb`.

---

## ✉️ Contact

For questions regarding the bio-image informatics pipeline or mechanobiological models, please contact **(Herbert) Siu-Ho Wong** at [herbert.wong150@gmail.com].
