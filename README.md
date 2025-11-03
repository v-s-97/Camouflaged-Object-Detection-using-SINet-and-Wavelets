# Camouflaged Object Detection using SINet and Wavelets

A coarse-to-fine camouflaged object detector that augments SINet with Discrete Wavelet Transform (DWT) features for stronger frequency–spatial reasoning. 

---

## Overview

Detecting camouflaged objects (COD) is hard because target appearance often matches the background. This project integrates wavelet-domain features into a SINet-style, coarse-to-fine architecture: a **Search** module proposes likely regions, and an **Identification** module refines boundaries. Inspired by Frequency-Spatial Entanglement Learning (FSEL), we inject frequency cues via **Discrete Wavelet Transform (DWT)** to complement spatial CNN features. 

### Key ideas

* Combine **spatial features** from a ResNet-50 backbone with **frequency features** from DWT (LL, LH, HL, HH) to capture global structure and fine details. 
* Use **Haar wavelets** for efficiency; consider **Coiflets** as a future improvement for sharper edges. 
* Train end-to-end with **BCE + Dice loss** to balance pixel accuracy and overlap quality. 

---

## Dataset

Experiments use **COD10K**: 10,000 images across 78 categories, with hierarchical annotations (boxes, object/instance masks, attributes). The official split is **6,000 train / 4,000 test**; images come from diverse natural scenes (aquatic, amphibian, bird, terrestrial). Objects vary widely in scale and exhibit low center bias, making COD10K challenging and suitable for complex scenes. 

---

## Method

### Architecture at a glance

* **Backbone:** ResNet-50 extracts multi-level features. High-level maps are decomposed by **DWT** into **LL (global)**, **LH (horizontal edges)**, **HL (vertical edges)**, **HH (textures)**. High-frequency maps are resized (bilinear) and concatenated with spatial features. 
* **Search Module:** Multi-scale fusion of intermediate features + compressed wavelet channels; produces a **coarse map** via 1×1 conv + sigmoid. 
* **Identification Module:** Refines the coarse map using deepest features plus DWT features; upsampling and fusion yield the **final segmentation**. 

### Why wavelets (vs Fourier)?

Wavelets offer **multi-resolution** analysis with better **time/space–frequency localization**, which helps capture local edges and textures crucial in COD; FFT provides global spectra but lacks localization for non-stationary content. 


---

## Evaluation

We report four standard metrics on COD10K:

* **S-Measure (structure):** 0.795
* **E-Measure (enhanced alignment):** 0.965
* **Weighted F-measure:** 0.225
* **MAE:** 0.035

These results indicate strong alignment and low average error, with remaining difficulty in the most extreme camouflage cases (reflected by the lower WFM). 

---

## Qualitative Results

* **Worst case:** complete miss on some visually subtle targets.
* **Intermediate:** partial masks or extra fragments.
* **Best case:** accurate, full-object segmentation even under challenging camouflage. 


---

## Limitations & Future Work

* Misses can occur under **extreme camouflage** or very low contrast (false negatives).
* Consider **Coiflet** wavelets for sharper edge fidelity (with higher compute), and broader datasets for robustness. 

