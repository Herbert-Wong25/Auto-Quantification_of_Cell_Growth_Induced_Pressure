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
