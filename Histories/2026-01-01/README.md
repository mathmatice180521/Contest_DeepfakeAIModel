아래 내용을 복사해서 프로젝트 폴더의 README.md 파일에 저장하시면 됩니다. 대회 제출 규격에 맞게 깔끔하게 정리했습니다.

Markdown

# 🕵️‍♂️ Deepfake Detection Project

본 프로젝트는 딥페이크 탐지 경진대회를 위해 개발된 `EfficientNetV2` 기반의 탐지 모델 학습 및 추론 코드를 포함하고 있습니다.
FaceForensics++ 등의 외부 데이터를 활용하여 학습되었으며, 제한된 추론 환경(오프라인, 1시간 이내)에서 최적의 성능을 내도록 설계되었습니다.

## 📁 프로젝트 구조 (Project Structure)
submission/ ├── model/ │ └── best_model_ffpp.pt # 학습된 최종 모델 가중치 ├── src/ │ ├── dataset.py # 데이터 로드 및 전처리 (Albumentations) │ ├── models.py # 모델 정의 (timm Wrapper) │ └── init.py ├── config/ │ └── config.yaml # 하이퍼파라미터 설정 파일 ├── train.py # 학습 스크립트 (Focal Loss, Mixup 적용) ├── inference.py # 추론 스크립트 (Multi-Frame, Smart Mode) └── README.md # 프로젝트 설명서


## ⚙️ 환경 설정 (Environment)
* **Python:** 3.8+
* **Torch:** 2.0+ (CUDA 11.8 권장)
* **Libraries:** `timm`, `albumentations`, `facenet-pytorch`, `opencv-python`

## 🚀 모델 학습 (Training)
학습은 `train.py`를 통해 수행됩니다. A100 GPU 환경에 최적화되어 있으며, `AMP(Automatic Mixed Precision)`를 사용합니다.

### 주요 기법
* **Model:** `tf_efficientnetv2_m.in21k_ft_in1k`
* **Loss:** Focal Loss (Alpha=1, Gamma=2)
* **Augmentation:**
    * Image Compression (Quality 60~100)
    * Mixup & CutMix
    * Random Brightness/Contrast
* **Input Size:** 384 x 384 (Face Crop)

```bash
python train.py
🔍 모델 추론 (Inference)
추론은 inference.py를 통해 수행되며, test_data 폴더 내의 모든 파일(동영상/이미지)을 자동으로 탐지하여 submission.csv를 생성합니다.

추론 전략 (Inference Strategy)
Smart Detection: 파일 포맷을 자동 감지하여 처리 방식 분기

동영상: 균등 간격으로 30 프레임 추출

이미지: 단일 프레임 처리

Face Crop: MTCNN을 활용하여 얼굴 영역 정밀 추출 (Margin 0%)

Soft Voting: 30개 프레임의 예측 확률을 평균(Mean)내어 최종 점수 산출 (안정성 확보)

Robustness: 얼굴 미검출 시 Center Crop으로 대체하여 에러 방지

Bash

python inference.py
⏱️ 성능 및 리소스 (Performance)
VRAM: < 24GB (Batch Size 32 기준)

Inference Time: 약 20~25분 (L40S GPU, Test Data 500개 기준)

Network: 오프라인 작동 가능 (단, MTCNN 가중치는 사전 로드 필요)


오늘 하루 정말 고생 많으셨습니다! 이 자료들이 사용자님의 프로젝트 정리와 대회 제출에 큰 도움이 되기를 바랍니다. 마지막까지 좋은 결과 있으시길 응원합니다! 👊✨