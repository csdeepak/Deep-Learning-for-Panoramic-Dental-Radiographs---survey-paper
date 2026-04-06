# Dental AI Pipeline
### Automated OPG Analysis — Caries & Bone Loss Detection

> **Deep Learning for Panoramic Dental Radiographs**  
> Automated per-tooth diagnosis of caries and periodontal bone loss using FDI numbering

---

## Table of Contents
- [Project Overview](#project-overview)
- [Final Output Format](#final-output-format)
- [System Pipeline](#system-pipeline)
- [Survey Paper](#survey-paper)
- [Project Status](#project-status)
- [Repository Structure](#repository-structure)
- [Dataset](#dataset)
- [Installation](#installation)
- [Usage](#usage)
- [Model Architecture](#model-architecture)
- [Results](#results)
- [Key Findings from Data Exploration](#key-findings-from-data-exploration)
- [Next Steps](#next-steps)
- [References](#references)

---

## Project Overview

This project builds a fully automated system that takes a single **OPG (Orthopantomogram) X-ray** as input and produces a structured **per-tooth dental report** identifying caries and periodontal bone loss for every affected tooth using **FDI numbering**.

The motivation comes directly from our survey paper:
> *"No existing system simultaneously addresses caries detection, severity staging, periodontal bone loss quantification, and explainability within a single unified pipeline for panoramic radiographs."*

This pipeline is designed to close that gap — targeting junior clinicians and dental students who benefit most from AI-assisted diagnostic support.

**Team:** C S Deepak, Chennupati Gunadeep, Archana, AVSN Sai Srujan  
**Guidance:** Dr. Mamatha H R, Director, Bajaj Engineering Skills Training @ PESU  
**Institution:** Department of Computer Science, PES University — RR Campus

---

## Final Output Format

```
Tooth 16
→ Caries: Moderate
→ Bone loss: Present

Tooth 24
→ Caries: Yes (Early)
→ Bone loss: Absent

Tooth 36
→ Caries: No
→ Bone loss: Present
```

> Only teeth where disease is present are included in the report.  
> All tooth identification uses **FDI numbering** (international standard).

---

## System Pipeline

```
OPG X-ray (840×1615 px)
        │
        ▼
┌─────────────────────────┐
│  Phase 3: YOLO          │  → Per-tooth bounding boxes
│  Tooth Detection        │  → FDI number assignment
└─────────────────────────┘  → Left-right sort + jaw separation
        │
        ├──────────────────────────────────────┐
        │                                      │
        ▼                                      ▼
  [Tight Crop 1.0×]                   [Expanded Crop 1.5W × 2.0H]
  (crown-focused)                     (includes alveolar bone)
        │                                      │
        ▼                                      ▼
┌───────────────────┐              ┌──────────────────────┐
│  Phase 1+2:       │              │  Phase 4:            │
│  Caries Model     │              │  Bone Loss Model     │
│  ResNet18         │              │  ResNet18 Binary     │
└───────────────────┘              └──────────────────────┘
        │                                      │
        ▼                                      ▼
  Caries Yes/No                        Bone Loss Present/Absent
  + Severity                                   │
  (mild/mod/severe)                            │
        │                                      │
        └──────────────┬───────────────────────┘
                       ▼
              Filter affected teeth
                       │
                       ▼
           Structured Dental Report (FDI)
```

### Crop Strategy

| Model | Crop Factor | Region | Why |
|---|---|---|---|
| Caries (ResNet18) | 1.0× tight | Crown only | Caries on enamel/dentin |
| Bone Loss (ResNet18) | 1.5× W / 2.0× H | Crown + root + bone | Bone level below root |

> **Critical:** Asymmetric expansion for bone loss — teeth in OPG are ~3× taller than wide (mean aspect ratio 0.35). Horizontal over-expansion bleeds into adjacent teeth.

---

## Survey Paper

The theoretical foundation of this project is a comprehensive survey paper reviewing 47 research papers (2021–2026):

📄 **[Deep Learning for Panoramic Dental Radiographs: A Comprehensive Survey on Caries Detection, Periodontal Bone Loss Assessment and Explainable AI](survey/dental_survey_paper.docx)**

### Survey Scope

| Category | Label | Papers Reviewed |
|---|---|---|
| Caries Detection & Severity Classification | C1 | 15 |
| Periodontal Disease & Bone Loss | C2 | 12 |
| Multi-Task End-to-End OPG Diagnosis | C3 | 7 |
| Explainable AI in Dental Diagnostics | C4 | 4 |
| Datasets, Benchmarks & Clinical Validation | C5 | 5 |
| Tooth Segmentation & Numbering | C6 | 4 |

### Key Findings from Survey

- Best caries models achieve **>98% AUC**; best bone loss detection reaches **99.5% mAP50**
- **6.4% explainability implementation rate** despite universal consensus on its necessity
- All models are single-disease focused — no unified pipeline exists
- Dataset fragmentation: most trained on small, single-center, private collections
- No system addresses severity staging + bone loss + explainability together

### Five Critical Gaps Identified

1. **Single-disease model focus** — no unified multi-disease pipeline
2. **Absence of automated severity staging** for bone loss
3. **6.4% explainability rate** — clinically unacceptable
4. **Dataset fragmentation** from single-center collections
5. **Poor clinical workflow integration** — no real deployment validation

> This project directly addresses gaps 1 and 2.

---

## Project Status

| Phase | Task | Status |
|---|---|---|
| Phase 1 | Caries Detection (ResNet18 binary) | ✅ Complete |
| Phase 2 | Caries Severity (ResNet18 3-class) | ✅ Complete |
| Phase 3 | Tooth Detection (YOLO) | 🔄 In Progress |
| Phase 4 | Bone Loss — Data Exploration | ✅ Complete |
| Phase 4 | Bone Loss — Dataset Construction | 🔄 Next |
| Phase 4 | Bone Loss — Model Training | ⬜ Pending |
| Phase 5 | Full Pipeline Integration | ⬜ Pending |

---

## Repository Structure

```
dental-ai-pipeline/
│
├── README.md                          # This file
├── requirements.txt                   # Python dependencies
├── .gitignore                         # Git ignore rules
│
├── survey/
│   └── dental_survey_paper.docx       # Survey paper (47 papers, 2021–2026)
│
├── docs/
│   └── project_report.pdf             # Full project report with all findings
│
├── configs/
│   ├── caries_config.yaml             # Phase 1+2 training config
│   ├── bone_loss_config.yaml          # Phase 4 training config
│   └── yolo_config.yaml               # Phase 3 YOLO config
│
├── src/
│   ├── data/
│   │   ├── __init__.py
│   │   ├── tufts_parser.py            # Parse Tufts JSON annotations
│   │   ├── dataset_builder.py         # Build processed_dataset_tufts/
│   │   ├── crop_utils.py              # Tight + expanded crop functions
│   │   └── transforms.py             # Augmentation pipeline
│   │
│   ├── models/
│   │   ├── __init__.py
│   │   ├── caries_model.py            # ResNet18 caries detector
│   │   ├── bone_loss_model.py         # ResNet18 bone loss classifier
│   │   └── yolo_detector.py           # YOLO tooth detection wrapper
│   │
│   ├── pipeline/
│   │   ├── __init__.py
│   │   ├── inference.py               # Full end-to-end inference
│   │   ├── fdi_mapper.py              # Universal → FDI conversion
│   │   └── report_generator.py        # Structured report output
│   │
│   └── utils/
│       ├── __init__.py
│       ├── visualization.py           # Draw bboxes, masks, reports
│       ├── metrics.py                 # F1, AUC, confusion matrix
│       └── logger.py                  # Training logger
│
├── notebooks/
│   ├── 01_tufts_data_exploration.ipynb    # Full dataset exploration (Colab)
│   ├── 02_caries_training.ipynb           # Phase 1+2 training notebook
│   ├── 03_bone_loss_dataset_build.ipynb   # Phase 4 dataset construction
│   ├── 04_bone_loss_training.ipynb        # Phase 4 model training
│   └── 05_pipeline_inference.ipynb        # Full pipeline demo
│
├── scripts/
│   ├── build_dataset.py               # CLI: build processed dataset
│   ├── train.py                       # CLI: train any model
│   ├── evaluate.py                    # CLI: evaluate trained model
│   └── run_inference.py               # CLI: run full pipeline on OPG
│
├── results/
│   ├── figures/                       # Training curves, confusion matrices
│   ├── checkpoints/                   # Model weights (gitignored)
│   └── reports/                       # Sample output dental reports
│
└── tests/
    ├── test_crop_utils.py             # Unit tests for crop functions
    ├── test_fdi_mapper.py             # Unit tests for FDI conversion
    └── test_pipeline.py               # Integration tests
```

---

## Dataset

### Tufts Dental OPG Dataset

| Property | Value |
|---|---|
| Total radiographs | 1000 |
| Image size | 840 × 1615 px (uniform) |
| Images with tooth bboxes | 968 |
| Total tooth annotations | 26,005 |
| Avg teeth per image | 26.9 |
| Tooth numbering | Universal (1–32) + deciduous (A–T) |
| Expert labels | 1000 free-text diagnoses |
| Normal cases | 660 (66%) |
| Pathological cases | 340 (34%) |
| Expert pathology masks | 439 (154 with findings) |
| **Working training pool** | **423 images (full annotation overlap)** |

### Dataset Access

The Tufts dataset is available at: [Tufts Dental Database](https://tdd.ece.tufts.edu/)

Place the dataset at the path configured in `configs/bone_loss_config.yaml`:
```yaml
dataset:
  root: /path/to/Tufts/ZIP
  radiographs: Radiographs/Radiographs
  bbox_json: Segmentation/Segmentation/teeth_bbox.json
  expert_json: Expert/Expert/expert.json
  expert_mask: Expert/Expert/mask
```

### Annotation Format Notes

```python
# bbox JSON — IMPORTANT: format is [y1, x1, y2, x2] corner points
# NOT standard [x, y, w, h]
for obj in item["Label"]["objects"]:
    y1, x1, y2, x2 = obj["bounding box"]
    x, y, w, h = x1, y1, x2 - x1, y2 - y1

# expert JSON — join key is nested field, NOT dict key
stem = Path(item["External ID"]).stem   # e.g. "53.JPG" → "53"
desc = item["Description"]
```

---

## Installation

```bash
git clone https://github.com/yourusername/dental-ai-pipeline.git
cd dental-ai-pipeline

pip install -r requirements.txt
```

### Requirements

```
torch>=2.0.0
torchvision>=0.15.0
opencv-python>=4.8.0
scikit-learn>=1.3.0
pandas>=2.0.0
numpy>=1.24.0
matplotlib>=3.7.0
seaborn>=0.12.0
Pillow>=10.0.0
fpdf2>=2.7.0
reportlab>=4.0.0
ultralytics>=8.0.0
tqdm>=4.65.0
PyYAML>=6.0
```

---

## Usage

### Build Bone Loss Dataset

```bash
python scripts/build_dataset.py \
  --config configs/bone_loss_config.yaml \
  --output data/processed_dataset_tufts
```

### Train Bone Loss Model

```bash
python scripts/train.py \
  --config configs/bone_loss_config.yaml \
  --model bone_loss \
  --output results/checkpoints/bone_loss_v1
```

### Run Full Pipeline on OPG

```bash
python scripts/run_inference.py \
  --image path/to/opg.jpg \
  --caries_weights results/checkpoints/caries_best.pth \
  --bone_loss_weights results/checkpoints/bone_loss_best.pth \
  --yolo_weights results/checkpoints/yolo_best.pt \
  --output results/reports/
```

### Key Code Patterns

```python
# Expanded crop for bone loss (asymmetric — teeth are tall)
def expanded_crop(img, x, y, w, h, fw=1.5, fh=2.0):
    cx, cy = x + w/2, y + h/2
    nw, nh = w * fw, h * fh
    x1 = max(0, int(cx - nw/2))
    y1 = max(0, int(cy - nh/2))
    x2 = min(img.shape[1], int(cx + nw/2))
    y2 = min(img.shape[0], int(cy + nh/2))
    return cv2.resize(img[y1:y2, x1:x2], (224, 224))

# Class-weighted loss for imbalanced training
weights = compute_class_weight("balanced",
    classes=np.array([0, 1]), y=train_labels)
criterion = nn.CrossEntropyLoss(
    weight=torch.tensor(weights).float())
```

---

## Model Architecture

### Phase 1+2: Caries Detection & Severity

| Property | Value |
|---|---|
| Architecture | ResNet18 (ImageNet pretrained) |
| Input | 224×224 tight crop (crown) |
| Phase 1 Output | Binary — Caries Yes/No |
| Phase 2 Output | 3-class — Mild/Moderate/Severe |
| Normalization | ImageNet mean/std |

### Phase 4: Bone Loss Detection

| Property | Value |
|---|---|
| Architecture | ResNet18 (ImageNet pretrained) |
| Input | 224×224 expanded crop (1.5W × 2.0H) |
| Output | Binary — Present/Absent |
| Loss | CrossEntropyLoss with class weights [1.85, 1.0] |
| Optimizer | Adam, lr=1e-4 |
| Scheduler | ReduceLROnPlateau (patience=5) |
| Training set | 423 images |
| Target F1 | ≥0.70 per class |

### Phase 3: Tooth Detection (YOLO)

| Property | Value |
|---|---|
| Architecture | YOLOv8/YOLOv11 |
| Input | Full OPG (840×1615) |
| Output | Per-tooth bboxes + position index |
| Training data | Tufts teeth_bbox.json (26,005 annotations) |
| FDI assignment | Left-right sort + upper/lower jaw split |

---

## Results

### Phases 1 & 2 — Caries (Completed)

*(Results to be added after training)*

### Phase 4 — Bone Loss (In Progress)

| Metric | Target | Status |
|---|---|---|
| Class 0 F1 (normal) | ≥ 0.75 | Pending |
| Class 1 F1 (bone loss) | ≥ 0.70 | Pending |
| Macro F1 | ≥ 0.72 | Pending |

---

## Key Findings from Data Exploration

### Dataset Characterization
- All OPGs uniform size: **840×1615 px** — no resizing needed pre-crop
- Michelson contrast mean **0.999** — high quality, consistent radiographs
- Tooth aspect ratio peaks at **0.35–0.40** — teeth are 3× taller than wide
- Degenerate bboxes (w=1px or h=1px): 102 records — filter before training

### Annotation Findings
- Expert masks encode **per-lesion instance IDs**, not severity classes
- Severity labels do not exist in structured form — text only
- **U-Net severity segmentation not viable** — no pixel-level severity GT
- Spatial IoU between tooth crop and expert mask blob = correct label strategy

### Bugs Caught During Exploration

| Bug | Root Cause | Fix |
|---|---|---|
| 0 coverage matches | JSON join on dict keys vs nested `External ID` | Extract `Path(item["External ID"]).stem` |
| Wrong crop locations | Assumed `[x,y,w,h]`; actual `[y1,x1,y2,x2]` | Swap coordinates, compute w/h |
| Wrong mask interpretation | Assumed severity classes; actual instance IDs | Changed to binary classification |

---

## Next Steps

1. **Dataset construction** — build `processed_dataset_tufts/train/{0,1}/` using spatial IoU label assignment
2. **Add ±15% bbox jitter** to training crops to simulate YOLO inference noise
3. **Stratified 80/20 split** — never random split with imbalanced data
4. **Train bone loss model** — ResNet18 binary, weighted loss
5. **Evaluate per-class F1** — target ≥0.70 for both classes
6. **Integrate with YOLO + caries pipeline** — attach FDI to every crop
7. **Universal → FDI conversion** at YOLO output stage

---

## References

Key papers from the survey (full list in `survey/dental_survey_paper.docx`):

[1] Ayhan et al., "Detection of dental caries under fixed dental prostheses," *BMC Oral Health*, 2025.  
[3] Jundaeng et al., "Advanced AI-assisted panoramic radiograph analysis," *Frontiers in Dental Medicine*, 2025.  
[5] Nassiri & Akhloufi, "YOLO-based panoramic dental X-ray image analysis," *Neural Computing and Applications*, 2025.  
[9] Iacob et al., "Automated detection of periodontal bone loss in 2D radiographs," *Dentistry Journal*, 2025.  
[11] Albano et al., "AI for radiographic imaging detection of caries: systematic review," *BMC Oral Health*, 2024.  
[23] Khubrani et al., "Detection of periodontal bone loss from 2D dental radiographs," *Dentomaxillofacial Radiology*, 2025.  
[41] Zhou et al., "Deep learning in dental image analysis: systematic review," *arXiv:2510.20634*, 2025.  

---

## License

This project is for academic and research purposes.  
Survey paper © the respective authors. All rights reserved.

---

*PES University — RR Campus, Bangalore, Karnataka, India*
