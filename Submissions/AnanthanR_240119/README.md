# Project Report: Comparative Analysis of OCR Pipelines

## 1. Introduction
The objective of this assignment was to implement and compare two distinct Optical Character Recognition (OCR) systems: a traditional rule-based engine (**PyTesseract**) and a deep learning-based framework (**EasyOCR**). The goal was to evaluate their performance on a multi-type document dataset using standard quantitative metrics (CER and WER) and qualitative observation.

## 2. Methodology

### Part A: Traditional OCR (PyTesseract)
PyTesseract serves as a wrapper for Google’s Tesseract-OCR engine. Since Tesseract is highly sensitive to image quality, a robust preprocessing pipeline was implemented before passing images to the engine.

**2.1 Image Preprocessing Pipeline**
To improve character recognition accuracy, the following operations were applied to raw images:
* **Grayscale Conversion:** Reduced color complexity by converting images to single-channel grayscale.
* **Otsu’s Binarization:** Applied automatic thresholding to separate foreground text from the background. This was crucial for handling varying lighting conditions.
* **Noise Removal:** Utilized morphological operations (Opening) with a $1\times1$ kernel to remove salt-and-pepper noise without degrading character strokes.

**2.2 Configuration**
Different Page Segmentation Modes (PSM) were tested. The final evaluation utilized **PSM 6** ("Assume a single uniform block of text"), as it provided the most consistent results for the document layouts found in the dataset.

### Part B: Deep Learning OCR (EasyOCR)
EasyOCR is a ready-to-use OCR package that utilizes a deep learning pipeline consisting of a **CRAFT** (Character Region Awareness for Text Detection) detector and a **CRNN** (Convolutional Recurrent Neural Network) recognition model.

**2.3 Strategy Adjustment**
* **Original Requirement:** Fine-tune the EasyOCR model using transfer learning.
* **Adjusted Methodology:** Due to hardware constraints and dependency updates in the EasyOCR training modules, the scope was adjusted to **Inference-Only evaluation**. Instead of retraining, the pre-trained `english_g2` model was utilized to test generalization capabilities on the provided dataset. This allows for a direct comparison of a "stock" deep learning model against a "stock" traditional engine.

## 3. Evaluation Metrics
To quantify performance, two standard metrics were calculated for every test image:

1.  **Character Error Rate (CER):** The percentage of characters that were incorrectly predicted.
    $$CER = \frac{S + D + I}{N}$$
    *(Where $S, D, I$ are Substituted, Deleted, and Inserted characters, and $N$ is the total characters in ground truth).*
2.  **Word Error Rate (WER):** The percentage of words that were incorrectly predicted.

## 4. Results

*Below is a summary of the performance on 3 random test samples from the dataset.*

| Image ID | Ground Truth (Snippet) | PyTesseract (CER / WER) | EasyOCR (CER / WER) |
| :--- | :--- | :--- | :--- |
| **n02-037** | Sentence Database N02-037 He g... | 0.3679 / 0.5581 | 0.0251 / 0.062 |
| **m04-100** | Sentence Database M04-100 And ... | 0.5061 / 0.8092 | 0.0061 / 0.0382 |
| **m01-136** | Sentence Database M01-136 His ... | 0.3607 / 0.6711 | 0.0068 / 0.0336 |

*(Note: The values above are derived from the automated evaluation scripts run in the Colab notebooks.)*

## 5. Comparative Analysis & Observations

Based on the testing phase, the following observations were made regarding the two pipelines:

### 5.1 Handwriting vs. Printed Text
* **PyTesseract:** Struggled significantly with handwritten samples. It often failed to recognize cursive connectors, resulting in "garbage" output (random symbols) or extremely high error rates.
* **EasyOCR:** Demonstrated superior performance on handwriting. The deep learning backbone successfully inferred characters even with irregular strokes and slant, resulting in a much lower CER for handwritten documents.

### 5.2 Noise Tolerance
* **PyTesseract:** Required explicit preprocessing (Otsu thresholding) to work effectively. Without it, the engine was easily confused by background noise or paper texture.
* **EasyOCR:** Showed high robustness to noise. It was able to detect text regions accurately even on raw, noisy images without extensive preprocessing steps.

### 5.3 Computational Efficiency
* **PyTesseract:** Extremely fast and lightweight. It processed images in milliseconds, making it suitable for real-time CPU-based applications.
* **EasyOCR:** Significantly heavier. Inference took longer (seconds vs milliseconds) and required GPU acceleration for optimal speed.

## 6. Conclusion
The evaluation confirms that while **PyTesseract** remains a viable, high-speed option for clean, machine-printed documents, **EasyOCR** provides a substantial accuracy advantage for complex scenarios, particularly involving handwriting and noisy backgrounds. For this specific multi-type dataset, EasyOCR is the recommended pipeline despite the higher computational cost.