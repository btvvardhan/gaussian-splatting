# Apollo 17 View Synthesis with Gaussian Splatting and Photogrammetry

This Assignment-4 reconstructs 3D scenes from Apollo 17 lunar imagery using COLMAP and enhances them using **Gaussian Splatting** to synthesize novel views. We perform photogrammetry-based modeling with and without augmented views and assess reconstruction quality using both **quantitative metrics** and mesh comparisons.

---

## 📁 Folder Structure

```
.
├── data/
│   └── apollo17/
│       ├── images/                # Original 15 Apollo HR images
│       ├── sparse/                # COLMAP sparse reconstruction (0/)
│       │   ├── cameras.bin
│       │   ├── images.bin
│       │   └── points3D.bin
├── output/
│   └── 000fab2b-8/
│       ├── cfg_args              # Training config
│       ├── point_cloud/          # Learned point cloud (iteration_30000/)
           ├──ours_30000/
              ├──point_cloud.ply. #this is the trained  model
        ├── cameras.json
│       └──train
            ├──ours_30000/
│           ├── renders/          # Rendered 15 views recovered
│           └── gt/               # Ground truth views
```

---

## 🔧 Step-by-Step Process

### Step 1: Apollo Image Preprocessing

* Downloaded 15 Apollo 17 high-resolution `.png` images.
* Resized or normalized filenames as needed for COLMAP.

### Step 2: Feature Extraction using COLMAP

### Step 3: Feature Matching & Sparse Mapping

### Step 4: Export COLMAP Output for Gaussian Splatting

### Step 5 : Generated textured model and dense point cloud.

Then copied:

* `images/`, `cameras.txt`, `images.txt`, `points3D.txt` into `data/apollo17/`

---

## 🚀 Gaussian Splatting Training

### Environment Setup

```bash
conda create -n splatter310 python=3.10
conda activate splatter310
pip install -r requirements.txt
```

**Issues Faced:**

* ❌ `plyfile` mismatch: fixed via `pip install plyfile==0.8.1`
* ❌ NumPy >= 2.0 crash with PyTorch: fixed via `pip install numpy==1.24.4`

### Training

```bash
python train.py -s data/apollo17
```

* Used 30000 iterations (`point_cloud/iteration_30000/point_cloud.ply`)
* Output in `output/000fab2b-8/`point\_cloud/

---

## 🖼️ Novel View Rendering

### Rendering Ground Truth Views and recovered original view

```bash
python render.py -m output/000fab2b-8 -s data/apollo17 --skip_train --iteration 30000
```

* Rendered 15 original views using `images.txt` poses.
* recovered images are  in `output/000fab2b-8/train/ours_30000/renders/` and `gt/` is the original view
* Both of them look almost similar.Only when you compare them by constantly switching can you observe some difference.


### Original Image
 ![00000](https://github.com/user-attachments/assets/3cafce5b-d5b4-48fa-a3e6-0cedf72f0672)

### Recovered image
 ![00000](https://github.com/user-attachments/assets/d3379bdc-178f-4944-8c17-f24e0fa0ffd3)



### PSNR / SSIM Evaluation

Used `scikit-image` for computing metrics:

```python
Run Assignment4_partA.py (in main directory) to view the evaluation results
```
<img width="1552" alt="Screenshot 2025-05-04 at 3 07 36 PM" src="https://github.com/user-attachments/assets/ad263588-dceb-4f55-850e-90993012f359" />

---

## ✨ Novel View Generation

* Extracted 15 camera translations from `images.txt`
* Performed PCA to find 3 principal directions using nerf_json.py
* Generated 10 novel camera poses around mean position
* Saved poses in:

  * `novel_poses.json` (for structured input to NeRF-like renderers)

### Rendering Novel Views using **nerfstudio**

```bash
python nerfstudio/nerfstudio/scripts/gaussian_splatting/render.py camera-path     --model-path output/000fab2b-8     --camera-path-filename novel_poses.json     --output-path output/000fab2b-8/novel_renders/pca_poses/     --output-format images
```

* Rendered novel views to `output/000fab2b-8/novel_renders/`

---

## 📷 NeRFStudio / Meshroom Photogrammetry Comparison

* Used `Meshroom` and `colmap model_converter` to perform photogrammetric modeling.
* Compared:

  * Original model (15 Apollo views)
  * Augmented model (15 original + 10 novel views)

---

## 📊 Evaluation

### Quantitative

* **PSNR / SSIM** metrics between:

  * Rendered image vs ground truth
  * Augmented photogrammetry model vs baseline

### Qualitative

* Visual comparison of Meshroom-generated textured mesh.
* Improved geometry coverage with novel poses.

---

## 📌 Dependencies

```bash
python==3.10
torch==2.0+
numpy==1.24.4
plyfile==0.8.1
scikit-image
PIL
opencv-python
```

---

## 👎 Citation

This work was adapted from:

* [Inria Gaussian Splatting](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)
* [COLMAP](https://colmap.github.io/)
* [Apollo Image Archive](https://www.hq.nasa.gov/alsj/a17/images17.html)

---

## 🙌 Name 

Teja Vishnu Vardhan Boddu
M.S. Robotics & Autonomous Systems
Arizona State University
