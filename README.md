# SECOM 반도체 공정 불량 예측 모델링

이 저장소는 SECOM 데이터셋(반도체 제조 공정 센서 데이터)을 활용하여 웨이퍼의 Pass/Fail 불량을 조기에 감지하기 위한 머신러닝 모델링 프로젝트를 담고 있습니다.

## 1. 프로젝트 개요
- **목표**: 590개의 센서 데이터와 1,567개의 웨이퍼 데이터를 분석하여 불량(Fail)을 예측하는 분류 모델 구축
- **핵심 성과**: ROC-AUC 0.75 이상 달성을 목표로 다양한 알고리즘과 앙상블 기법 적용
- **특징**: 불균형 데이터 처리를 위한 SMOTE 적용, 하이퍼파라미터 자동 최적화(PSO, RandomizedSearch) 구현

## 2. 데이터셋 및 전처리
- **데이터 소스**: UCI SECOM Dataset (`uci-secom.csv`, `secom.names`)
- **전처리 과정**:
  - 결측치 보간 (SimpleImputer - Median)
  - 분산이 0에 가까운 불필요한 특성 제거 (VarianceThreshold)
  - 데이터 누수 방지를 위한 파이프라인 구축 (SMOTE → StandardScaler → Model)

## 3. 사용된 모델 및 방법론
본 프로젝트에서는 다음과 같은 고급 부스팅 알고리즘과 최적화 기법을 사용했습니다.

*   **XGBoost**: Particle Swarm Optimization(PSO)을 사용하여 학습률, 깊이 등 주요 하이퍼파라미터를 정밀 탐색하고, 5개의 시드(Seed)를 사용한 앙상블 모델을 구축했습니다.
*   **LightGBM**: RandomizedSearchCV를 통해 상위 파라미터를 선별하고, 이를 기반으로 9개의 모델을 앙상블하여 안정성을 높였습니다.
*   **Threshold Tuning**: 불량(Fail) 클래스의 F1-Score를 최대화하기 위해 예측 임계값(Threshold)을 0.05~0.95 범위에서 최적화했습니다.
*   **Model Blending**: XGBoost와 LightGBM의 예측 확률을 가중 평균하여 성능을 극대화했습니다.

## 4. 실험 결과 요약
| 모델 | ROC-AUC | 설명 |
|------|---------|------|
| **XGBoost 앙상블** | 0.7179 | 최적 임계값 적용 시 Accuracy 약 89% |
| **LightGBM 앙상블** | 0.7377 | 재현율(Recall) 측면에서 더 우수한 성능 보임 |
| **Blending (Best)** | **0.7463** | LightGBM 단독 또는 혼합 모델에서 가장 높은 성능 기록 |

> 자세한 분석 내용은 `reports/secom_model_report.txt` 및 `reports/secom_model_report.pdf`를 참고하세요.

## 5. 파일 구조
*   `model2.ipynb`: 데이터 로드, 전처리, 모델 학습 및 평가가 포함된 메인 주피터 노트북
*   `reports/`: 모델링 결과 보고서 및 성능 요약 이미지 폴더
*   `secom.data`, `secom.names`: 원본 데이터셋 파일

## 6. 향후 개선 사항
*   스태킹(Stacking) 메타 모델 도입을 통한 성능 향상 시도
*   HistGradientBoosting 등 추가 알고리즘 검토
*   재현율과 정밀도의 균형을 위한 정밀한 임계값 탐색

---
*작성일: 2025년 11월 23일*
