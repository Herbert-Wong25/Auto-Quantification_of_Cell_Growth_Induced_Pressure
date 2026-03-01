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
