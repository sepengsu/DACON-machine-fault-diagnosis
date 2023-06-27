# DACON-machine-fault diagnosis
월간 데이콘 기계 고장 진단 AI 경진대회

프로젝트 기간: 2022.12.05 ~ 2023.01.16

참가자: 서재원

데이터와 전처리 후 데이터는 대회 규정상 삭제 

## 0. 데이터, 모델 구조 및 파일 위치
```
${PROJECT}
├── data: 대회에서 제공해준 기본 데이터 
│     ├── sample_submission.csv -> 삭제 완료
│     ├── test.csv -> 삭제 완료
│     └── train.csv -> 삭제 완료
├── Feature
│     ├── test.csv  : test의 모든 음원들을 165개의 Feature로 추출한 데이터 -> 삭제 완료
│     └── train.csv : train의 모든 음원들을 165개의 Feature로 추출한 데이터 -> 삭제 완료
├── model
│     ├── Auto
│     │     ├── model_0_1~5.h5 : 모드 0에서의 k-fold autoencoder
│     │     ├── model_2_1~5.h5 : 모드 2에서의 k-fold autoencoder
│     │     └── threshold.csv  : 각 모델별의 thresold를 저장해놓은 것
│     ├── IsolationForest_0.pkl: 모드 0에서의 IsolationForest 모델
│     ├── IsolationForest_2.pkl: 모드 2에서의 IsolationForest 모델
│     ├── OneClassSVM_0.pkl    : 모드 0에서의 OneClassSVM 모델
│     └── OneClassSVM_2.pkl    : 모드 2에서의 OneClassSVM 모델
├── preprocessed data
│          ├── scaler_0.pkl : 모드 0에서의 MinMaxScaler
│          ├── scaler_2.pkl : 모드 2에서의 MinMaxScaler
│          ├── test_feature_0.csv  : pre-processing을 한 후의 test_feature중 모드0
│          ├── test_feature_2.csv  : pre-processing을 한 후의 test_feature중 모드2
│          ├── train_feature_0.csv : pre-processing을 한 후의 train_feature중 모드0
│          └── train_feature_2.csv : pre-processing을 한 후의 train_feature중 모드2
└── Final_Dacon.ipynb
```

## 1. 데이터 전처리 설명
### 1-1. 데이터 특징 추출
```
1. MFCC vs 통계적 feature 추출
MFCC로 2D Feature map으로 추출할 수도, 이를 평균으로 하여 feature vector로 할 수도 있다.
하지만 MFCC로 하기에는 어려움이 많고 통계적 Feature 추출이 그래픞 파악하기 더 유용하다고 판단함 
```
```
2. 통계적 Feature
15개의 통계적 Feature를 사용하였다.
Max,Min,Mean,Rms,Std,Skew,Kurt,CF,IF,SF,Median,Mode,Q1,Q3,Iqr이다.
MAx,Min,Mean,Rms,Std,Median,Mode, Q1,Q3,Iqr은 전통적인 통계치이다.
Skew는 왜도(skewness)로 데이터의 비대칭성을 나타내는 지표이다.
kurt는 첨도(kurtosis)로 데이터가 뾰족한 정도를 나타내는 지표이다. 
CF는 파고율(crest factor)로 솟아로은 모양의 요소가 있는 정도를 나타내는 지표이다.
IF는 Impulse factor이다.
SF는 shape factor이다.
```
```
3. Time Domain과 Freqency Domain
Time Domain은 시간에 따른 음파 그래프를 분석한다는 것이다.
Freqency Domain은 음파 그래프를 10 level의 wave transform을 한 후 각각 분석한다.
```
## 2. 모델 설명

### 2-1. Isolation Forest
```
2008년에 발표된 의사결정나무(decision tree)를 이용한 앙상블 기반의 이상탐지 기법
```
### 2-2. One Class SVM(Support Vector Machine)
```
1999년에 나온 알고리즘으로 데이터의 한 클래스(일반적으로 정상적인 데이터)만을 사용하여 모델을 학습시킨다.
그 후 이 클래스에 속하지 않는 이상치를 식별하는 알고리즘이다.
데이터셋이 거의 모두(혹은 모두) normal 데이터일 때 많이 사용한다.
```
### 2-3. Autoencoder
```
2006년 이후 널리 사용한 기법으로, 먼저 데이터셋이 인코더와 디코더를 거치면서 변하는 정도로 이상치를 탐지하는 일고리즘이다.
인코더에 데이터를 넣으면 디코더에 output이 나오는데 이 둘을 비교하여 다른 정도가 threshold보다 크면 이상치로 탐지한다.
```



### 3. 결과
순위로 다음과 같이 나왔다.
![image](https://github.com/sepengsu/DACON-machine-fault-diagnosis/assets/111292354/e592f9f3-ebfa-426b-8ee8-c42c6f9e3c8e)

### 4. 문제점 
1. 데이터셋 불균형이 아닌 고장 데이터의 부재로 인한 데이터 분석의 어려움
2. 데이터셋에 대한 지식 부족
### 5. 참고문헌
* 참고 *
```
Local Outlier Factor(LOF)
2000년에 발표된 전통적인 이상탐지 기법이다.
전체 데이터 분포에서 밀집도를 고려하여 이상탐지한다.
즉, 이웃 그룹의 밀도를 고려하여 객체의 이상치를 판별한다.
이 데이터에서는 모든 데이터가 정상데이터이므로 하기에는 너무 부적절하다고 판단하였다.  
```
```
(1): https://brightwon.tistory.com/11
(3): https://leedakyeong.tistory.com/entry/Anomaly-Detection-by-Auto-Encoder
https://eatchu.tistory.com/entry/%EC%9D%B4%EC%83%81%EC%B9%98%ED%83%90%EC%A7%80-%EA%B8%B0%EB%B0%98%EC%9D%98-Local-Outlier-FactorLOF-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%ED%8C%8C%ED%97%A4%EC%B9%98%EA%B8%B0
```
