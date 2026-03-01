# Automated Quantification of Bacterial Growth-Induced Pressure

This repository contains a Python-based bio-image informatics pipeline designed to characterize the mechanics of bacterial growth under physical confinement. By automating the detection of microfluidic wall deformations, this tool quantifies Growth-Induced Pressure (GIP) in millibars (mbar), bridging the gap between high-resolution microscopy and quantitative mechanobiology.

## 🔬 Scientific Context

When bacteria such as *E. coli* proliferate within confined spaces (e.g., host tissues, medical implants, or microfluidic chambers), they exert physical forces on their surroundings. Measuring this force is technically challenging.

This project overcomes this limitation by utilizing a PDMS microfluidic chamber as a **physical force sensor**. By calculating the structural deformation of the chamber walls as the bacterial colony reaches confluency, we can map pixel-level structural changes to absolute physical pressure (GIP).

## 🛠 Tech Stack & Dependencies

* **Bio-Image Informatics:** Python 3.10+
* **Advanced Metrology:** `scipy.spatial.distance` (`cdist`)
* **Computer Vision / Segmentation:** `scikit-image` (`skimage.measure`, `regionprops`)
* **Data Processing & Visualization:** `pandas`, `numpy`, `matplotlib`, `natsort`

---

## 🚀 The Pipeline & Script Evolution

### Part 1: Pressure-to-Deformation Calibration

Before biological analysis, the microfluidic device must be calibrated using known pneumatic pressures (0–2000 mbar) to establish a mathematical baseline.

* **Notebook:** `01_Calibration_Model.ipynb`
* **Methodology:** Implements robust pairwise distance calculations (`cdist`) between non-parallel segmented wall boundaries to compute average separation with high sub-pixel precision.
* **Output:** Generates the empirical calibration model linking applied pressure to structural displacement.

### Part 2: Growth-Induced Pressure (GIP) Analysis

Applies the calibration model to 30-minute interval timelapse imagery of *E. coli* proliferation.

* **Notebook:** `02_Growth_Pressure_Analysis.ipynb`
* **Methodology:** Implements spatial masking (cropping to the central uniform region) and morphological filtering to track wall separation over time dynamically.
* **Output:** Time-resolved quantification of GIP, mapping the transition from free growth to mechanical confinement.

---

## 📊 Key Quantitative Metrics

The analytical pipeline translates measured pixel deformations into absolute mechanical units (mbar) by integrating empirical calibration data with a three-dimensional mechanical model of the micro-chamber.

### 1. Young's Modulus of the Chamber ($E$)

To quantify the Growth-Induced Pressure (GIP), the pipeline first determines the absolute Young's Modulus of the microfluidic device. This is achieved by converting the effective horizontal modulus ($E_{eff}$), derived from pneumatic calibration, into a 3D structural value (Equation 2):

$$E = \alpha_{NL} \cdot \frac{E^{eff}}{\frac{1}{2} + \frac{1}{2} \cdot \alpha_{y} \cdot \frac{L_{y}}{L_{z}} + \alpha_{z} \cdot \frac{L_{x}}{L_{z}}}$$

* **$E_{eff}$**: Effective horizontal modulus calculated from the calibration strain data.
* **$L_{x}, L_{y}, L_{z}$**: Initial micro-chamber dimensions.
* **$\alpha_{y}, \alpha_{z}$**: Proportionality constants for strain ($\alpha_{y} = 0.98, \alpha_{z} = 1$).
* **$\alpha_{NL}$**: Non-linear correction coefficient (0.92).

### 2. Volumetric Strain Transformation

During the growth phase, the pipeline measures the horizontal strain ($\Delta L_x / L_x$) via wall detection. This horizontal displacement is then mapped to the total volumetric strain ($\Delta V / V$) of the chamber (Equation 3):

$$\frac{\Delta L_{x}}{L_{x}} \approx \frac{\frac{\Delta V}{V}}{\frac{1}{2} + \frac{1}{2} \cdot \alpha_{y} \cdot \frac{L_{y}}{L_{x}} + \alpha_{z} \cdot \frac{L_{x}}{L_{z}}}$$

### 3. Growth-Induced Pressure (GIP) Calculation

The final GIP (in mbar) is calculated as the product of the derived volumetric strain and the calibrated Young’s Modulus:

$$P_{GIP} = E \cdot \left( \frac{\Delta V}{V} \right)$$

---

## 📈 Results & Biological Summary

* **Robust Sensor Calibration:** The `cdist`-based metrology pipeline successfully generated a highly correlated calibration curve, proving that automated image analysis can convert standard PDMS micro-chambers into precise mechanobiological sensors.
* **GIP Kinetics:** Time-lapse analysis revealed a distinct two-phase growth profile. Initially, bacteria grow freely with near-zero pressure. Upon reaching confluency, GIP spikes rapidly, demonstrating the immediate mechanical load exerted on the confining environment.
* **Adaptation to Confinement:** The ability of the bacterial population to sustain and increase pressure against the confining walls highlights a significant mechanobiological adaptation, likely requiring transcriptional changes to resist metabolite-induced turgor pressure build-up.

---

## 📂 Project Structure

* **`/notebooks`**: Jupyter notebooks for Calibration Modeling and GIP Analysis.
* **`/data/raw`**: Standardized directory for `.tif` image sequences (Calibration masks and Growth timelapse masks).
* **`/data/results`**: Output directory for calibration CSVs and temporal pressure arrays.
* **`/assets`**: Contains the generated calibration curve and reassembling chamber (x-axis & y-axis).

---

## ⚙️ Setup & Installation

```bash
# Clone the repository
git clone https://github.com/Herbert-Wong25/Automated-Quantification-of-Bacterial-Growth-Induced-Pressure.git
cd Automated-Quantification-of-Bacterial-Growth-Induced-Pressure

# Create and activate environment
conda create -n mechanobiology python=3.10
conda activate mechanobiology

# Install requirements
pip install numpy pandas scipy scikit-image matplotlib natsort

```

## ✉️ Contact

For questions regarding the bio-image informatics pipeline or mechanobiological models, please contact **Siu Ho (Herbert) Wong** at [herbert.wong150@gmail.com].

