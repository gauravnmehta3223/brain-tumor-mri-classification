# Brain Tumor Classification from MRI

This project classifies brain MRI scans into **four classes**: glioma, meningioma, pituitary tumor and no tumor. It compares a small CNN built from scratch with a fine-tuned **EfficientNetB3** (transfer learning from ImageNet).

## Results

| Model | Validation accuracy (263 held-out images) |
| --- | --- |
| Fine-tuned EfficientNetB3 | **91.6%** |

The baseline CNN reached 79.9% on the test split, but that number has the same overlap problem described below.

> **Evaluation note:** the notebook trains on a split of the `Testing` folder and then reports test accuracy on the whole `Testing` folder. The test images therefore include the training images. Because of this, the 98.2% test accuracy in the notebook output is not an independent result. The validation accuracy above is the reliable figure, because those images were held out of training. The fix is to train on `Dataset/Training` (5,712 images) and test on `Dataset/Testing` (1,311 images).

## Approach

1. **Data:** images are resized to 224 × 224 RGB. Class weights balance the four classes.
2. **Baseline:** a 3-block CNN (Conv2D → MaxPool → BatchNorm) trained for 15 epochs.
3. **Final model:** EfficientNetB3 with ImageNet weights.
   - The first 300 layers are frozen and the rest are fine-tuned.
   - A new head is added: BatchNorm → Dense(256, L2) → Dropout(0.4) → softmax.
   - Trained with the Adamax optimizer, plus a custom callback that lowers the learning rate and stops early when validation loss stops improving.
4. **Evaluation:** accuracy, a classification report for each class, and a confusion matrix.

## Dataset

This project uses the [Brain Tumor MRI Dataset](https://www.kaggle.com/datasets/masoudnickparvar/brain-tumor-mri-dataset) by Masoud Nickparvar on Kaggle. Check the dataset page for its license and original sources before reusing the images.

| Class | Training | Testing |
| --- | --- | --- |
| glioma | 1,321 | 300 |
| meningioma | 1,339 | 306 |
| no tumor | 1,595 | 405 |
| pituitary | 1,457 | 300 |

## Run it

The notebook was written for Google Colab with the data on Google Drive.

1. Open `Notebook/Brain_Tumor_Detection.ipynb` in Colab and choose a GPU runtime.
2. Upload the `Dataset` folder to your Drive, or download the dataset from Kaggle.
3. In the "Get Dataframes" cell, set `train_dir` to the `Training` folder and `test_dir` to the `Testing` folder.
4. Run all cells.

To run it locally instead:

```bash
git clone https://github.com/gauravnmehta3223/brain-tumor-mri-classification.git
cd brain-tumor-mri-classification
pip install -r requirements.txt
jupyter notebook Notebook/Brain_Tumor_Detection.ipynb
```

Then skip the Google Drive cell and point the paths at `Dataset/Training` and `Dataset/Testing`.

## Repository contents

- `Notebook/Brain_Tumor_Detection.ipynb`: exploratory analysis, baseline CNN, EfficientNetB3 training and evaluation
- `Dataset/`: the MRI images in `Training/` and `Testing/` folders
- `Brain Tumor Detection Using Deep Learning.pdf`: the project report

## Limitations

- This is a course project, not a medical device. It is not validated for clinical use.
- All results come from a single dataset with one training run.
