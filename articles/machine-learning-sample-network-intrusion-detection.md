<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning Sample: Network intrusion detection | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure 기계 학습 샘플: 네트워크 침입 검색

*기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에서 이 모델과 연결된 샘플 실험을 찾을 수 있습니다. 실험 이름은 다음과 같습니다.*

    Sample Experiment - Network Intrusion Detection - Development

## 문제 설명

데모에서는 다양한 네트워크 기능을 사용하여 침입/공격에 속하는 네트워크 활동을 검색합니다. 기능이 클래스를 상당히 잘 예측하고 클래스가 충분히 균형이 잡혀 있으므로 이 이진 분류 문제는 너무 어렵지 않습니다. 여러 가지 기능은 레이블의 부분이므로 분류자를 학습하기 전에 레이블의 모든 부분을 제거해야 합니다.

## 데이터

1.  KDD Cup 1999의 데이터에는 학습 및 테스트 데이터 집합이 둘 다 포함됩니다. 새 네트워크 침입이 테스트 집합에 도입되므로 학습 데이터 집합에 대한 분류 정확도가 기록상 테스트 데이터 집합보다 더 높습니다. 학습 데이터 집합에는 레이블을 포함하여 약 126,000개 행과 43개 열이 있습니다. 열 3개가 레이블 정보의 부분이므로 모델 학습에 사용할 수 있는 열 40개가 있습니다. 해당 열 40개는 주로 숫자이고 몇몇 문자열/범주 기능이 있습니다. 테스트 데이터에는 22,500개 정도 테스트 예제가 있고 학습 데이터에는 같은 열 43개가 있습니다.
2.  쉬운 액세스를 위해 데이터가 공개 Blob에 업로드되었습니다. Azure 기계 학습에서는 코드를 작성할 필요 없이 몇 번의 단순 클릭으로 Blob 같은 다른 저장소에서 데이터를 읽기 위한 판독기 모듈을 제공합니다.

## 모델

1.  모델의 첫 단계에서는 나중에 분류하기 위해 데이터에 조건을 지정합니다. "Class" 열에는 예측할 레이블이 포함됩니다. 이러한 레이블은 "정상"(침입 없음) 또는 공격 이름입니다. 몇몇 공격은 학습 데이터에서 많은 예제가 없지만 테스트 데이터 집합에 새로운 공격이 있으므로 문제를 실현할 수 있도록 이러한 다중 클래스 레이블을 이진 클래스 레이블로 변환합니다. 스튜디오에서는 이 전처리 단계를 간소화하는 기본 제공 모듈을 제공합니다. 표시기 값 모듈을 사용하여 클래스 레이블을 이진화하고 프로젝트 열 모듈을 사용하여 이 이진화된 클래스 레이블 열을 선택합니다(원래 클래스 레이블 열 제외).
2.  이 실험을 개발하는 동안 기능 선택/상관 관계를 통해 많은 기능이 레이블과 밀접하게 상호 관련되어 있음을 확인합니다. 이는 합성 데이터 집합에서 일반적입니다. 클라우드 기계 학습에서는 몇 번의 단순 클릭으로 이를 시각화할 수 있습니다. 이 작업을 하려면 프로젝트 모듈 출력에 대한 시각화 옵션을 선택하고 기능에 대한 헤더를 클릭하여 시각화 도우미를 시작하고 비교값 드롭다운에서 클래스 레이블을 선택합니다.
3.  레이블과 밀접하게 상호 관련된 기능 수는 적으므로 최적 모델을 결정하기 위한 기능 선택 사용에 관계없이 선형 및 비선형 분류자의 조합을 테스트합니다. 이 비교는 모델 평가 모듈을 통해 수행합니다.

## 결과

1.  비선형 분류자(향상된 의사 결정 트리)가 선형 분류자(로지스틱 회귀 분석)보다 성능이 약간 더 우수합니다.

![][0]

이제 기능 선택을 사용하여 향상된 의사결정 트리에 비교하려고 합니다. 분류 성능은 매우 비슷하지만 모든 기능에서 향상된 의사 결정 트리가 약간 더 우수하므로 점수 매기기 워크플로에 사용됩니다. 이 데이터 집합의 경우 이 높은 분류 AUC가 일반적입니다.

![][1]

<!-- Removed until this part is fixed ## Operationalization ##   We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio:   1. First step is to save the learned model (by right clicking on the classifier module output)  1. Now create a new experiment and search for saved model and drop it in the panel for new experiment  1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment  1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service  1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio    -->

  [0]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
  [1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
