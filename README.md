# Dental Cavity Detection and Segmentation 

## Approach Overview

The final implemented approach involves (main-yolo.ipynb) :

1.  **Centroid Cropping and Sampling (CCS):** A preprocessing step where square patches are extracted around the centroid of cavity regions identified from the accurate ground truth masks. This focuses the model on relevant areas.
   
      Reference: https://pubmed.ncbi.nlm.nih.gov/36639724/
3.  **CLAHE Enhancement:** Contrast Limited Adaptive Histogram Equalization (CLAHE) was added as an optional preprocessing step before cropping to potentially enhance the visibility of cavities in the X-rays.
4.  **YOLOv8 Segmentation Model:** The `yolo-seg` models were fine-tuned on the generated cropped image patches. YOLO-seg predicts both bounding boxes and segmentation masks simultaneously.
5.  **Training:** The model was trained using the Ultralytics framework, with hyperparameters like learning rate, batch size, and epochs tuned through experimentation. Early stopping was used to prevent severe overfitting.
6.  **Evaluation:**
    * Standard YOLOv8 validation metrics (mAP50, mAP50-95 for box and mask) were calculated on the *cropped* validation set.
7.  **Experiment Tracking:** Weights & Biases (`wandb`) was integrated to log hyperparameters, track metrics across runs, and visualize results.

## Model Report: https://api.wandb.ai/links/pirajesh1/o386zzk2

## Challenges Faced

Several challenges were encountered during the task:

1.  **Initial Poor Performance:** Training standard YOLO models directly on the full X-ray images yielded low mAP scores, indicating that a more tailored approach was needed.
2.  **Small Dataset Size:** Working with ~300 images made the models prone to overfitting and sensitive to parameter changes.
3.  **Overfitting & Performance Plateau:** Even with CCS and larger models, the validation performance plateaued relatively early during training (e.g., around epoch 150). While early stopping prevented severe overfitting, it indicated a limit in the model's ability to generalize further with the given data and configuration.
4.  **Low Precision (mAP50-95 / Boundary Accuracy):** While the CCS approach significantly improved detection (mAP50 reaching ~0.5), achieving high precision in segmenting the exact cavity boundaries remained difficult, reflected in persistently low mAP50-95 scores (~0.20-0.25). This could be due to subtle cavity features or limitations in mask annotation precision. The main bottlenecck was the small dataset size
5.  **Inaccurate Initial Labels:** The project had access to pre-existing `.txt` bounding box labels which were found to be inaccurate upon visual inspection during EDA, necessitating the use of the masks as the primary source of ground truth and deriving training data from them.

Performance varied between the two approaches (main-yolo.ipynb and mask-rcnn.ipynb).

* **YOLOv8-seg (Nano) with CCS (512x512 Crops):** 
    * Box mAP50-95: 0.2590
    * Box mAP50: 0.4452
    * Mask mAP50-95: 0.1907
    * Mask mAP50: 0.4578
* **Mask R-CNN (ResNet-50 FPN V2) on Full Images (Resized 256x256):**
    * Segmentation AP @ IoU=0.50: 0.357
    * Segmentation AP @ IoU=0.50:0.95: 0.165
    * Bounding Box AP @ IoU=0.50: 0.389
    * Bounding Box AP @ IoU=0.50:0.95: 0.188

While the CCS approach significantly improved performance compared to baseline models on full images (especially mAP50), achieving highly precise segmentation (high mAP50-95, IoU, Dice) remains challenging with the current dataset.

yolo8n: 
![compare_image_5635](https://github.com/user-attachments/assets/7d7ef85e-ea47-4389-ac77-a746153c9e58)
![compare_image_5648](https://github.com/user-attachments/assets/7041cfa6-2413-48ac-8f12-ebefceb3213d)
![compare_image_5786](https://github.com/user-attachments/assets/c2584764-d944-4f8f-a29a-4d83b5ac47e8)
![compare_image_5896](https://github.com/user-attachments/assets/d5dd1124-ab4d-4269-9bdc-6648c7bb8dd0)
![compare_image_5900](https://github.com/user-attachments/assets/cb607966-d889-4033-9dcb-4b79da57798d)


