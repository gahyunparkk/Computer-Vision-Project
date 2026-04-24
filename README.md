# 👁️ Computer-Vision-Project

본 레포지토리는 **국민대학교 전자공학부 2025-1학기 컴퓨터비전 (이성원 교수님)** 수업의 프로젝트 및 실습 내용을 정리한 공간입니다. 이미지 처리의 기초부터 딥러닝 기반의 객체 인식까지, 단계별 컴퓨터 비전 알고리즘을 탐구하고 구현합니다.

> **Notice & Credit**
> - 본 프로젝트의 코드들은 교수님이 제공하신 **Skeleton Code 템플릿**을 기반으로 합니다.
> - 학습 및 포트폴리오 용도로 공개하며, 해당 수업을 수강하는 타 학생의 무단 복제 및 과제 제출을 금지합니다.

---

## 📂 Projects List

### 1️⃣ Image Filtering & Frequency Domain Analysis
이미지 필터링의 기초와 주파수 영역 해석을 통한 이미지 합성 기법을 다룹니다.

#### 🛠️ Key Implementations
- **Spatial Filtering:** $3 \times 3 \sim 15 \times 15$ 커널 크기별 Average 및 Gaussian Filter 구현 및 블러링 효과 분석
- **Noise Reduction:** Salt & Pepper 노이즈 제거를 위한 Mean Filter와 Median Filter 비교 분석
- **Frequency Domain:** FFT를 활용한 2D DFT 변환, 주파수 로그 스펙트럼 시각화 및 역변환 구현
- **Ideal LPF & HPF:** 주파수 대역 차단 마스크를 이용한 저주파/고주파 성분 추출
- **Hybrid Images:** 서로 다른 이미지의 고주파/저주파 성분을 결합하여 관찰 거리에 따라 다르게 보이는 하이브리드 이미지 생성

#### 📝 Analysis & Insights
* **Average vs Gaussian:** 동일 커널 크기에서 가우시안 필터가 가중치 적용 방식 덕분에 경계선을 더 잘 보존하며 부드러운 결과를 냄을 확인했습니다.
* **Salt & Pepper Noise:** 평균 필터는 노이즈를 뭉개지만, **Median Filter**는 주변 픽셀의 중간값을 선택하여 원본 디테일을 유지하면서도 노이즈를 탁월하게 제거함을 실증했습니다.
* **주파수 성분의 특성:** Low-pass는 이미지의 전체적인 형태를, High-pass는 급격한 밝기 변화가 일어나는 Edge 성분을 포함한다는 점을 시각적으로 분석했습니다.
* **Hybrid Image:** 이미지 크기를 줄일수록 고주파 성분(디테일)은 사라지고 저주파 성분(윤곽)이 강조되는 시각 인지 특성을 확인했습니다.

---

### 2️⃣ Feature Matching & Panoramic Image Stitching
이미지의 고유한 특징점을 추출하고 이를 매칭하여 여러 장의 사진을 하나의 광각 파노라마로 합성하는 End-to-End 파이프라인을 구축했습니다.

#### 🛠️ Key Implementations
- **Harris Corner Detection from Scratch:** Sobel 필터를 이용한 Gradient 계산부터 Harris Response($R$) 산출, 로컬 맥시멈을 찾는 NMS(Non-Maximum Suppression)까지 전 과정을 라이브러리 없이 직접 구현했습니다.
- **SIFT-like Descriptor:** 특징점 주변의 기울기 Magnitude와 Orientation 히스토그램을 생성하여 조명이나 미세한 변화에도 강인한 128차원 기술자를 추출하는 로직을 설계했습니다.
- **Robust Matching & RANSAC:** NNDR(Nearest Neighbor Distance Ratio) 매칭을 통해 1, 2순위 후보 간의 모호성을 제거하고, RANSAC 알고리즘으로 잘못 매칭된 점, Outliers을 걸러내어 최적의 투영 변환 행렬을 추정했습니다.
- **Multi-Scale Feature Extraction:** 고정 해상도의 한계를 극복하기 위해 이미지 피라미드를 활용하여 다양한 스케일에서 특징점을 검출하고, 이에 대응하는 Scale-Aware Descriptor를 구현했습니다.
- **Sequential Image Stitching:** 4장의 이미지를 순차적으로 결합(`1+2` → `(12)+3` → `(123)+4`)하고, Warping 및 Blending 기법을 적용하여 이질감 없는 파노라마 결과물을 생성했습니다.

#### 📝 Analysis & Insights
* **Harris Corner Sensitivity:** 감도 계수($k$)가 작을수록 많은 특징점이 추출되나 노이즈에 취약해지고, $k$가 클수록 확실한 코너만 남는 트레이드오프(Trade-off)를 확인했습니다. 실험을 통해 디테일 보존과 노이즈 억제 사이의 최적 윈도우 크기를 도출했습니다.
* **NNDR Matching의 효율성:** 단순 Nearest Neighbor 방식보다 거리 비(Ratio)를 이용한 NNDR 방식이 매칭의 정확도를 획기적으로 높임을 확인했습니다. Ratio 임계값을 조절하며 Inlier의 신뢰도와 매칭 개수 사이의 균형을 분석했습니다.
* **RANSAC Threshold 최적화:** RANSAC의 임계값을 1.5로 설정하여 오류 허용 범위를 정밀하게 관리했습니다. NMS 임계값을 낮춰 코너 후보를 충분히 확보한 뒤, RANSAC 단계에서 강력하게 필터링하는 전략이 파노라마 합성의 안정성을 높임을 실증했습니다.
* **실제 데이터 적용 시의 한계:** 직접 촬영한 사진 중 오버랩(Overlap) 영역이 부족한 경우 매칭 포인트 결여로 스티칭이 실패하는 사례를 분석했습니다. 이를 통해 특징점 기반 합성에는 충분한 중첩 영역과 고유한 텍스처(Texture)가 필수적임을 기술적으로 이해했습니다.
* **Algorithm Comparison (SIFT vs SURF):** 가우시안 블러 기반의 SIFT와 적분 영상을 활용한 SURF의 원리를 비교하며, 연산 속도와 회전/스케일 불변성 사이의 기술적 차이점을 학습했습니다.

---

### 3️⃣ Bag-of-Visual-Words Image Classification
전통적인 컴퓨터 비전 기법인 Bag-of-Visual-Words 파이프라인을 구축하여 비행기와 자동차 객체를 분류하는 이진 분류기를 구현했습니다.

#### 🛠️ Key Implementations
- **Feature Extraction (ORB):** SIFT/SURF의 효율적인 대안인 ORB(Oriented FAST and Rotated BRIEF) 알고리즘을 사용하여 이미지의 주요 특징점과 기술자를 추출했습니다.
- **Custom K-Means Clustering:** `sklearn` 등 외부 라이브러리를 사용하지 않고, 거리 행렬 계산부터 중심점 업데이트(Centroid Update), 수렴 판정(`tol`)까지 **K-Means 알고리즘 전 과정을 NumPy로 직접 구현**했습니다.
- **BoVW Encoding:** 추출된 특징점들을 생성된 Visual Vocabulary(100 clusters)에 매핑하여, 각 이미지를 고차원 히스토그램 벡터로 변환하는 Quantization 로직을 설계했습니다.
- **SVM Classification:** RBF 커널 기반의 Support Vector Machine을 활용하여 비선형 결정 경계를 학습시키고 최적의 분류 모델을 도출했습니다.
- **Manual Performance Metrics:** 모델의 신뢰도 평가를 위해 TP, TN, FP, FN 카운트 및 Accuracy, Precision, Recall 지표를 계산하는 로직을 직접 코드로 구현하여 성능을 분석했습니다.

#### 📝 Analysis & Insights
* **Cluster Count($K$)의 영향력:** $K$값에 따른 모델의 표현력을 분석했습니다. $K=20$일 때는 패턴 구분 능력이 부족해 Underfitting이 발생하고, $K=200$ 이상일 때는 히스토그램이 희소해지며 Overfitting 경향을 보임을 확인했습니다. 실험 결과 $K=100$에서 가장 안정적인 성능을 확보했습니다.
* **알고리즘 수렴 최적화:** K-Means의 반복 횟수(Max Iters)와 허용 오차(`tol`) 값을 조절하며 중심점 수렴의 안정성을 실험했습니다. 반복 횟수를 1000회로 충분히 확보하고 `tol` 기반 조기 종료 로직을 통해 연산 효율성과 정확도의 균형을 맞췄습니다.
* **SVM Kernel 비교 분석:** * **Linear**: 연산은 빠르나 복잡한 이미지 패턴 분류에는 표현력이 부족함.
    * **Polynomial**: 고차원 매핑을 통해 비선형 경계를 형성하지만 파라미터 민감도가 높음.
    * **RBF (Gaussian)**: 가장 유연한 결정 경계를 형성하여 **최종 Accuracy 0.86**의 최고 성능을 달성했습니다.
* **모델 성능 평가:** Accuracy 0.86, Precision 0.88, Recall 0.83의 결과를 얻었습니다. 높은 Precision을 통해 모델이 예측한 결과의 신뢰도를 확인했으며, Recall 수치를 통해 실제 객체를 놓치지 않고 검출하는 성능을 정량적으로 증명했습니다.

---

### 4️⃣ CIFAR-10 Image Classification with Optimized CNN
PyTorch를 활용하여 10가지 카테고리의 사물 이미지(CIFAR-10)를 분류하는 CNN 모델을 설계하고, 다양한 최적화 기법을 통해 성능을 베이스라인 대비 약 40%p 향상시켰습니다.

#### 🛠️ Key Implementations
- **Custom CNN Architecture:** 기본적인 Conv2d, ReLU, MaxPool2d 레이어부터 전역 평균 풀링(AdaptiveAvgPool2d)과 분류기(Classifier)까지 엔드투엔드 모델을 설계했습니다.
- **Advanced Optimization:** 단순 SGD를 넘어 **AdamW 옵티마이저**와 Weight Decay를 적용하여 수렴 속도와 일반화 성능을 동시에 확보했습니다.
- **Deepening & Blocks:** 2-레이어 베이스라인 모델에서 시작하여, 연산 효율을 위한 **VGG 스타일의 6-레이어 블록 구조**로 고도화하여 모델의 표현력을 확장했습니다.
- **Regularization Techniques:** 학습 안정성을 위한 **Batch Normalization**과 Overfitting 방지를 위한 **Dropout(0.5)**을 도입하여 모델의 신뢰도를 높였습니다.
- **Data Augmentation Pipeline:** RandomCrop(with padding), RandomHorizontalFlip, ColorJitter, RandomAffine 등 강력한 데이터 증강 파이프라인을 구축하여 데이터 다양성을 확보했습니다.

#### 📝 Analysis & Insights (딥러닝 최적화 기록)
* **Architecture Evolution (47% → 86%):** 얕은 모델에서는 복잡한 패턴 학습의 한계로 인해 낮은 정확도를 보였으나, 레이어를 깊게 쌓고 채널 수를 512까지 확장하며 비선형 활성화 함수를 적재적소에 배치하여 성능을 획기적으로 개선했습니다.
* **Learning Rate 전략 분석:** LR이 0.01 이상일 때는 손실함수가 발산하고, 0.0001 이하일 때는 수렴 속도가 지나치게 느려지는 현상을 실험적으로 확인했습니다. 최종적으로 **LR=0.001**이 안정적인 수렴과 높은 정확도를 위한 최적점임을 도출했습니다.
* **Augmentation의 일반화 효과:** 증강 기법 적용 시 초기 학습 속도는 느려지지만, 에폭(Epoch)이 진행될수록 검증 데이터에 대한 정확도가 꾸준히 상승하는 것을 확인했습니다. 이는 모델이 이미지의 위치나 색상 변화에 강인한 특징을 학습했음을 보여줍니다.
* **클래스별 불균형 해소:** 초기 모델에서 혼동이 잦았던 Cat와 Dog 클래스의 정확도를 높이기 위해 모델의 깊이를 늘리고 BatchNorm을 추가하여, 미세한 특징 차이를 잡아낼 수 있도록 개선했습니다.
* **최종 성능 리포트:** 100 Epoch 학습 결과, **Test Accuracy 86.74%** 를 달성했으며 모든 클래스에서 70% 이상의 고른 정확도를 기록하며 안정적인 분류 성능을 입증했습니다.

---

## 💻 Environment
- **Language:** Python 3.x
- **Libraries:** `NumPy`, `Matplotlib`, `OpenCV`
- **Tools:** Jupyter Notebook, VS Code
