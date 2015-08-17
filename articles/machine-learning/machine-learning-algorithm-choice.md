<properties 
	pageTitle="기계 학습 알고리즘 선택 방법 | Microsoft Azure" 
	description="클러스터링, 분류 또는 회귀 실험에서 감독 및 자율 학습에 대한 Azure 기계 학습 알고리즘을 선택하는 방법입니다." 
	services="machine-learning"
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="bradsev;garye" />


# 클러스터링, 분류 또는 회귀를 위한 Azure 기계 학습 알고리즘을 선택하는 방법

이 항목에서는 기계 학습 방법의 기본적인 내용을 설명합니다. 특히, 적절한 기계 학습 알고리즘을 선택하여 주어진 형식의 데이터를 분석하고, 게시한 질문에 대답하고, 지정된 작업을 수행하거나 결정하기 위한 기준을 제공하는 방법에 대해 배웁니다.

> [AZURE.TIP][Microsoft Azure 기계 학습 알고리즘 치트 시트](machine-learning-algorithm-cheat-sheet.md)는 이 기사와 함께 제공되는 편리한 참조입니다.

기계 학습을 사용하여 분석을 수행할 때 일반적으로 다음 두 가지 질문에 직면합니다.

* 사용 가능한 데이터로 목표를 달성하기 위해 수행해야 하는 분석은 무엇입니까? 
* 이러한 분석을 수행하는 데 가장 적합한 알고리즘 또는 모델은 무엇입니까?

세 가지 유형의 기계 학습 분석이 설명되며 해당 사용 사례가 비교됩니다.

* **클러스터링**
* **분류** 
* **회귀** 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<a name="anchor-1"></a>
## 데이터에서의 기계 학습 알고리즘 학습

기계 학습은 미리 결정된 특정 모델을 데이터와 비교해 테스트하는 것이 아니라 데이터에서 학습하도록 설계된 알고리즘 클래스를 연구하는 분야입니다. 그 개념은 먼저 전체 데이터 집합에 적합한 모델을 추측한 다음 이를 경험적으로 테스트하는 *가설 연역법*을 사용하는 대신 데이터 집합에서 패턴을 조사하여 보다 귀납적으로 지식을 습득하는 것입니다.

데이터에서 학습으로 생각할 수도 있는 기계 학습 유형은 두가지 종류, *감독 학습* 및 *자율 학습*으로 제공됩니다.

<a name="anchor-2"></a>
## 감독 학습  

감독 학습을 사용하려면 대상 변수가 잘 정의되며 충분한 값의 수가 지정되어야 합니다.

감독 학습은 입력될 교육 인스턴스에 대한 올바른 출력 결과(또는 대상 변수)가 알려진 경우 발생하는 기계 학습 유형입니다. 기계 학습 알고리즘을 교육하는 목적은 입력을 알려진 출력 값에 매핑되는 모델 (즉, 규칙 또는 함수)를 찾는 것입니다. 입력을 출력에 올바르게 맵핑했는 지 여부를 알고리즘 에이전트에 알릴 수 있는 감독자가 있는 것과 유사합니다. 학습 프로세스가 완료되고 실행 가능한 모델이 있는 경우 새로운 입력 데이터에 적용하여 예상 출력을 예측할 수 있습니다. 이 경우 학습 데이터 집합과 달리 대상 값이 미리 알려지지 않습니다.

모델의 유형은 대상 변수의 특성으로 결정됩니다.

![감독 학습의 다이어그램: 레이블된 데이터에서 모델링하여 새 데이터의 결과를 예측하는 데 사용합니다.](./media/machine-learning-algorithm-choice/supervised-learning-using-known-data-to-model-solution.png)

감독 학습을 사용하는 광범위한 분석에는 *분류* 및 *회귀*.의 두 종류가 있습니다. 감독 학습은 분류 문제에서 매우 보편적인 것으로 이 범주의 목적은 우리가 만든 분류 시스템을 컴퓨터에서 학습할 수 있도록 하는 것이기 때문입니다. 응답은 일반적으로 'true' 또는 'false' 또는 '높음', '보통' 또는 '낮음'과 같은 알려진 몇 개의 값(레이블)입니다. 분류 알고리즘은 서수 응답 값이 아니라 명목 응답 값에 적용됩니다. 숫자 인식은 분류 학습의 일반적인 예입니다. 보다 일반적으로, 분류 학습은 모두 유용하며 분류를 쉽게 결정할 수 있는 문제에 적합합니다.

감독 학습에서는 조사 중인 변수를 설명 변수(예측 변수라고도 함)와 종속 변수(응답 변수라고도 함)로 분할할 수 있습니다. 분석의 목표는 *회귀 분석*에서와 같이 설명 변수 및 종속 변수 간의 관계를 정의하는 것입니다. 데이터 집합의 큰 부분에 대한 종속 변수의 값을 알 수 있어야 합니다. 회귀에서는 특정 차량의 갤런당 마일 수, 사람의 나이 등과 같이 응답 또는 출력 변수가 연속 값을 가집니다.

감독 학습은 교육 신경망 및 의사 결정 트리에 가장 일반적인 기술이기도 합니다.

> 두 기술 모두 미리 결정된 분류에서 제공되는 정보에 대한 의존도가 매우 높습니다. 신경망의 경우, 분류는 네트워크의 오류를 확인한 다음, 최소화하기 위해 네트워크를 조정하는데 사용되고 의사 결정 트리에 있으며, 분류는 분류 퍼즐을 해결하기 위해 사용할 수 있는 대부분의 정보를 제공하는 특성을 결정하기 위해 사용됩니다. ... 이 두 예제는 미리 결정된 분류의 형태로 일부 "감독"을 수행합니다.

>  숨겨진 Markov 모델 및 Bayesian 네트워크를 사용하여 음성 인식은 일반적인 방식으로 지정된 입력 값에 대한 오류를 최소화하려면 매개 변수를 조정하기 위해 감독의 일부 요소에 의존합니다. [[기계 학습, II부: 감독 및 자율 학습](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI 구간](http://www.aihorizon.com/)]


<a name="anchor-3"></a>
##자율 학습

기계 학습에서 자율 학습의 문제는 레이블이 없는 데이터에서 패턴 또는 숨겨진 구조를 찾는 것입니다. 이 모델에는 학습할 데이터 집합에 대한 "올바른 결과"가 제공되지 않습니다. 레이블이 지정되지 않은 예는 학습자가 제공되므로 오류 또는 보상이 아닌 잠재적인 솔루션을 평가하기 위한 사용자 의견은 없습니다. 목표는 작업 방법을 명시적으로 알려 주지 않더라도 컴퓨터에서 이러한 작업을 수행하는 방법을 학습하도록 하는 것입니다. 자율 학습 상황에서는 모든 변수가 동일한 방식으로 처리됩니다. 설명 변수 및 종속 변수 간의 차이가 없습니다. 그러나 달성할 일부 목표가 있으며, 데이터 양을 줄이거나 찾기 클러스터와 같은 보다 구체적인 목표와 같은 일반적인 목표가 될 수 있습니다.

Azure 기계 학습에서 **클러스터링**, **분류** 및 **회귀**를 통해 감독 및 자율 학습 모두를 수행할 수 있습니다.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##클러스터링
클러스터링은 자율 학습의 예입니다. 이 유형의 학습 목표는 학습 데이터에서 유사성을 찾고, 이러한 유사성으로 구분되는 하위 집합으로 데이터 집합을 분할하는 것입니다. 이러한 데이터 기반 절차에서 발견된 가장 유의한 클러스터가 우리의 직관적인 분류와 일치할 것이라는 기대는 항상은 아니지만 충족되는 경우가 많습니다.

클러스터링 알고리즘은 이러한 클러스터에 적합한 이름을 할당하는 것은 아니지만 이를 생성한 다음 사용하여 가장 적합한 클러스터로 분류함으로써 새로운 예에서 예상되는 유사성을 예측할 수 있습니다. 충분한 데이터가 있는 경우 데이터 기반 접근 방식이 유용할 수 있습니다. 예를 들어 Amazon.com에서 도서를 추천하는 데 사용하는 것과 같은 소셜 정보 필터링 알고리즘은 유사한 사용자 그룹을 찾은 다음 추천 사항을 제공하기 위해 이러한 그룹에 새 사용자를 할당하는 것을 기반으로 합니다.

Azure 기계 학습에서 사용할 수 있는 클러스터링 알고리즘은 [K-means 클러스터링][k-means-clustering]입니다.

![K-Means clustering algorithm exeperiment: Screenshot](./media/machine-learning-algorithm-choice/k-means-clustering-algorithm-menu.png)

K-means는 잘 알려진 클러스터링 문제를 해결하는 가장 단순한 자율 클러스터링 알고리즘 중 하나입니다. K-means 알고리즘은 샘플을 N개의 등분산 그룹으로 구분하여 데이터를 클러스터링함으로써 "관성" 또는 "클러스터 내 제곱합"이라는 기준을 최소화합니다. 이 알고리즘에서는 클러스터 수를 지정해야 합니다. K-means는 많은 수의 샘플을 확장할 수있으며 많은 다른 필드에서 다양한 범위의 응용 프로그램 영역에 사용되었습니다.

[K-Means 클러스터링][k-means-clustering] 알고리즘 모듈은 학습을 위해 [클러스터링 모델 학습][train-clustering-model] 모듈로 전달될 수 있는 학습되지 않은 K-means 클러스터링 모델을 반환합니다.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

이 그림에서는 K-Means 클러스터링을 사용할 때 구성할 옵션을 보여 줍니다. K-means 방법에서는 D-차원 데이터 요소 집합에 대한 지정된 개수의 클러스터를 찾습니다. *K 중심의 초기 집합*으로 시작하여, 메서드는 중심의 위치를 반복적으로 구체화 하려면 로이드 알고리즘을 사용합니다. 중심이 안정화되거나 *지정된 반복 수*가 완료되면 알고리즘이 종료합니다. 이 모듈은 N개의 데이터 요소에서 발견된 K개의 클러스터를 정의하는 최종 중심을 사용하여 K-by-D 배열을 초기화합니다. 또한 이 알고리즘은 K개의 클러스터 중 하나에 각 데이터 요소를 할당하여 길이가 N인 벡터를 사용합니다. 찾으려는 특정 개수(K개)의 클러스터가 주어진 경우 이 모듈은 처음 K개의 데이터 요소를 순서대로 K개의 클러스터에 할당합니다.


또한 해당 초기 클러스터 구성을 정의할 초기 요소를 수락하거나 생성합니다. *메트릭*은 데이터 요소와 중심 간의 거리를 측정하는 데 사용되는 방법을 정의합니다. 각 데이터 요소는 중심이 데이터 요소에 가장 가까운 클러스터에 할당됩니다. 기본적으로 이 방법에서는 *유클리드 메트릭*을 사용합니다. 그러나 이 방법의 대체 메트릭으로 *코사인 메트릭*을 지정할 수 있습니다. K-means 방법은 데이터 집합에 대한 로컬에 최적화된 클러스터 구성만 찾을 수 있습니다. 이 방법은 다른 초기 구성이 주어진 경우 다른 우수한 구성을 찾을 수도 있습니다.

<a name="anchor-5"></a>
##분류 
분류 분석에서는 샘플을 클래스로 나누고 이전에 레이블이 지정된 학습된 데이터 집합을 사용합니다. 이 기술은 데이터 인스턴스의 그룹 멤버 자격을 예측하는 데 사용됩니다. Azure 기계 학습에서 사용 가능한 분류 알고리즘은 다음과 같습니다.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

*두 클래스 알고리즘*은 이진 응답 변수(yes 또는 no, 0 또는 1, true 또는 false 등)에 사용되는 반면, *다중 클래스 알고리즘*은 인스턴스를 세 개 이상의 클래스로 분류하는 명목 응답 변수에 사용됩니다.

### 분류 알고리즘을 선택하기 위한 지침
이 알고리즘 목록은 일련의 질문을 발생시킵니다.

* 여러 분류자 중에서 특정 데이터 집합에서 사용하기에 가장 좋은 분류자를 어떻게 알 수 있습니까? 
* 분류자 중 하나가 "자연스러운" 선택인 경우가 있습니까? 
* 선택하는 원칙은 무엇입니까?

한 가지 권장되는 접근 방식은 여러 가지 분류자뿐만 아니라 각 알고리즘 내의 여러 가지 매개 변수 집합을 테스트한 다음 교차 유효성 검사를 사용하여 가장 적합한 것을 선택하는 것입니다.

> [AZURE.TIP] [Azure Machine Learning Studio](https://studio.azureml.net/) 샘플 데이터에 대해 병렬로 여러 알고리즘을 시도하여 결과를 비교할 수 있습니다. 다음은 [Azure 기계 학습 갤러리](http://gallery.azureml.net/): [다중 클래스 분류자 비교: 문자 인식](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92)의 예입니다.

다음은 이러한 작업을 시작하는 데 유용한 일반적인 지침입니다. 사용할 알고리즘을 선택할 때 다음 사항을 고려 하십시오. [[기계 학습 분류자 선택](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen을 선택하여 제안된 개요]

**학습 데이터 크기** 학습 집합이 작고 감독 분류자를 학습하려는 경우, 기계 학습 이론에서는 Naive Bayes와 같은 높은 바이어스/낮은 분산 분류자에 고정해야 한다고 말합니다. kNN과 같은 바이어스가 낮고 분산이 큰 분류자는 과적합 경향이 있기 때문에 이러한 분류자보다 유리합니다. 그러나 더 작은 점근 오류가 있기 때문에 학습 집합이 큰 경우 바이어스가 낮고 분산이 큰 분류자가 더 적합합니다. 이 경우 바이어스가 높은 분류자는 정확한 모델을 제공할 만큼 강력하지는 않습니다. Naive Bayes가 이러한 상황에 적합한 이론적 및 경험적 결과가 있습니다. 하지만 일반적으로 더 나은 데이터 및 좋은 기능을 사용하면 더 나은 알고리즘이 있는 것보다 많은 이점이 있습니다. 또한, 매우 큰 데이터 집합 분류 성능이 사용하는 알고리즘에 많은 영향을 미치지 않는 경우, 경우에 따라서 해당 확장성, 속도 또는 사용 편의성 등의 작업에 기반하여 알고리즘을 선택하는 것이 더 좋습니다.

**증분 방식으로 또는 배치 모드에서 학습해야 합니까?** 많은 양의 데이터가 있거나 데이터를 자주 업데이트하는 경우, 잘 업데이트하는 Bayesian 알고리즘을 사용할 수 있습니다. 신경망 네트 및 SVM 모두는 배치 모드의 학습 데이터에서 작동해야 합니다.

**단독으로 범주 또는 단독으로 숫자 데이터 또는 두 종류의 혼합입니까?** Bayesian은 범주/이항 데이터에 가장 적합합니다. 의사 결정 트리는 숫자 값을 예측할 수 없습니다.

**사용자 또는 청중이 분류자의 작동 방식을 이해해야 합니까?** Bayesian 또는 의사 결정 트리는 보다 쉽게 설명됩니다. 신경망 및 SVMs가 데이터를 분류하는 방법을 알거나 설명하기는 훨씬 어렵습니다.

**얼마나 빨리 해당 분류를 생성해야 합니까?** 트리가 복잡한 경우 의사 결정 트리는 느려질 수 있습니다. 반면, SVMs만 "줄" 중 어느 쪽에 데이터가 있는지 결정하는 데 필요하므로 보다 신속하게 분류합니다.

**문제가 나타나거나 필요한 복잡도는 무엇입니까?** 신경망 네트 및 SVMs는 복잡한 비선형 분류를 처리할 수 있습니다.

### 분류 알고리점의 장점 및 단점
이러한 분류 알고리즘에는 일부 장점과 단점이 각각 있습니다.

<a name="anchor-5a"></a> **로지스틱 회귀의 장점 및 단점:** "로지스틱 회귀 분석은 결과의 양상이 필요가 없는 확률로 나눈 결과의 발생 확률의 비율로 결과의 승산 계산에 기반합니다." [[10-12세 어린이 사이의 천식 유병율과 연관된 평가 요소에서 로지스틱 회귀 및 선형 판별식 분석: 두 통계 방식의 확산 및 유사성](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, et al (International Journal of Pediatrics, 2009) Article ID 952042]
 
로지스틱 모델은 파라메트릭하므로 응답 변수에서 각 예측 변수의 영향에 대한 통찰력을 제공하는 장점이 있습니다.


의사 결정 트리 또는 SVM과 달리 자연 확률적 해석을 사용할 수 있으면, 새 데이터를 통합하도록 모델을 쉽게 업데이트할 수 있습니다. 모델을 조정할 수 있는 다양한 방법이 있으며, Naive Bayes와 달리 상관 관계가 있는 기능에 대해 크게 걱정하지 않아도 됩니다. 다음 경우에 로지스틱 회귀가 유용합니다.

* 분류 임계값을 조정하는 확률 프레임 워크
* 추가 학습 데이터를 신속하게 통합  

고차원 데이터의 경우 로지스틱 회귀가 의사 결정 트리보다 적합합니다. 예를 들어 텍스트 분류에서 50만 개의 단어(기능)로 구성된 10만 개가 넘는 문서가 있을 수 있습니다. 의사 결정 트리는 자유도가 지나치게 많게 과적합 경향이 있기 때문에 선형 초평면(hyperplane) 학습과 같은 단순한 규칙이 더 낫습니다. 텍스트 데이터에 대해 의사 결정 트리를 사용하여 선택 기능을 수행하지만 훨씬 감소된 기능의 하위 집합을 선택하는 경우 텍스트 분류에 대해 유용한 많은 정보가 손실됩니다. 학습 모델이 높은 차원 데이터와 함께 사용되면 분산 기반 오류가 증가하기 쉽습니다. 이 경우 더 높은 바이어스로 간단한 모델을 사용하는 것이 좋습니다.

로지스틱 회귀의 한 가지 단점은 이 변수의 계수가 훨씬 크게 되기 때문에 하나의 예측 요인이 응답 변수를 거의 설명할 수 있을 때 불안정할 수 있다는 점입니다.

<a name="anchor-5b"></a> **의사 결정 트리의 장점 및 단점:** [의사 결정 트리](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf)는 해석 및 설명하기 쉽습니다.

> [의사 결정 트리] 기능 상호 작용을 쉽게 처리하고 비파라메트릭이므로 이상치 또는 데이터의 선형 구분 가능 여부를 걱정하지 않아도 됩니다. 예를 들어 의사 결정 트리는 기능 x의 하단에 클래스 A가 있고 기능 x의 중간 범위에 클래스 B가 있으며, 상단에 다시 A가 있는 경우를 쉽게 처리합니다. 한 가지 단점은 온라인 학습을 지원하지 않으므로 새로운 예제가 있는 경우 트리를 다시 만들어야 한다는 점입니다. 또 다른 단점은 쉽게 overfit하지만 임의의 포리스트(또는 높아진 트리)와 같은 앙상블 메서드 형태입니다. 또한 임의의 포리스트는 많은 분류의 문제에 대해 승자이며(일반적으로 SVMs보다 앞선), 빠르고 확장 가능하므로 SVMs에서와 같이 매개 변수 튜닝에 대해 걱정할 필요가 없습니다. [[기계 학습 분류자 선택](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

의사 결정 트리는 *지지도*, *신뢰도* 및 *향상도*와 같은 메트릭과 함께 출력을 규칙으로 생성합니다.


<a name="anchor-5c"></a> **SVMs의 장점 및 단점:** Support Vector Machines(SVMs)는 높은 차원 공간에 적용됩니다. 차원 수가 샘플 수보다 큰 경우에도 여전히 효과적입니다. 그러나 이 방법은 기능의 수가 샘플 수보다 훨씬 큰 경우 성능이 저하됩니다. 또한 결정 함수의 학습 지점(지원 벡터라고도 함) 하위 집합을 사용하므로 메모리 사용이 효율적입니다. 매우 다양한 기능을 갖추고 있으며, 공통 및 사용자 지정 모두에서 결정 함수에 대해 다른 커널 함수를 지정할 수 있습니다. 커널 함수는 저차원 학습 샘플을 고차원으로 변환하는 데 사용되며, 이는 선형 분리성 문제에 유용합니다.

그러나 SVM은 확률 추정치를 직접 제공하지 않습니다. 이는 광범위한 5겹 교차 유효성 검사를 통해 계산됩니다.

>[사용] 높은 정확도, 과도한 적합화에 대한 멋진 이론적인 보증, 기본 기능 공간에서 데이터를 선형으로 분리될 수 있지 않더라도 잘 작동하는 적절한 커널을 사용합니다. [SVMs] 매우 높은 차원 공백이 정상인 텍스트 분류 문제에 특히 자주 사용 합니다. [[기계 학습 분류자 선택](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

SVM은 최근에 여러 클래스에서 작동하도록 조정되었지만 포리스트와 달리 2클래스 분류자로 시작됩니다. "OVR(One-Vs-Rest)" 학습과 유사한 방법을 사용하여 최적화되지 않을 수 있는 다중 클래스 분류자를 만들 수 있습니다. SVM은 2클래스 출력(즉, 다양성이 2인 범주 출력 변수)만 처리할 수 있으므로 N개의 클래스가 있는 경우 N개의 SVM을 학습합니다(SVM 1은 “Output==1” vs “Output != 1”을 학습하고, SVM 2는 “Output==2” vs “Output != 2”를 학습하며, SVM N은 “Output==N” vs “Output != N”을 학습함). 그런 다음 새로운 입력에 대한 출력을 예측하기 위해 각 SVM으로 예측하여 가장 먼 예측을 긍정 영역에 두는 SVM을 확인합니다. [[SVM(Support Vector Machines)](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore (Carnegie Mellon University 2001)]

<a name="anchor-5d"></a> **Naive Bayes의 장점 및 단점:** [Naive Bayes (NB)](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) 분류자는 일반적으로 분류 문제를 해결하는 데 사용됩니다. 기능이 독립적인 것으로 가정하며, 기술을 '미숙하게' 만듭니다.

> NB 조건부 독립성 가정이 실제로 유지되는 경우 Naive Bayes 분류자는 로지스틱 회귀와 같은 판별 모델보다 더욱 빠르게 수렴되므로 적은 학습 데이터를 필요로 합니다. NB 가정이 유지되지 않는 경우에도 NB 분류자는 실무에서 효율적으로 사용되는 경우가 많습니다. 주요 단점은 기능 간의 상호 작용을 학습할 수 없다는 것입니다. 예를 들어 브래드 피트와 톰 크루즈가 나오는 영화를 좋아하지만 두 사람이 함께 나오는 영화는 싫어하는 경우를 학습할 수 없습니다. [[기계 학습 분류자 선택](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]


<a name="anchor-5e"></a> **One-vs-all:** OVA(One-vs-All)는 다중 클래스 분류 문제를 여러 이진 클래스 문제 집합으로 축소하는 전략입니다. 이 전략에서는 클래스당 단일 분류자를 학습하며, 해당 클래스의 샘플을 양성 샘플로 사용하고 다른 모든 샘플을 음성 샘플로 사용합니다. 이 전략을 수립하려면 클래스 레이블이 아닌 해당 의사 결정에 대한 실수 값의 신뢰성 점수를 생성하고 개별 클래스 레이블이 단독 단일 샘플에 대한 여러 클래스를 예측하는 모호성을 일으킬 수 있습니다. [[Multiclass 분류](http://en.wikipedia.org/wiki/Multiclass_classification) (Wikipedia 2006)]


<a name="anchor-6"></a>
##회귀
 
회귀 분석에서는 지난 유추를 기반으로 새로운 값을 예측합니다. 종속 변수의 새로운 값은 하나 이상의 측정된 특성 값을 기반으로 계산됩니다. Azure 기계 학습에서 사용할 수 있는 여러 회귀 알고리즘은 다음과 같습니다.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

사용 사례 및 데이터에 따라 특정 알고리즘 하나를 선택합니다. 아래에서는 몇 가지 회귀 알고리즘과 해당 주 사용 사례를 설명합니다.

<a name="anchor-6b"></a> **[Bayesian 선형 회귀][bayesian-linear-regression]**는 Bayesian 유추 컨텍스트 내에서 통계 분석이 수행되는 선형 회귀 방식입니다. 회귀 모델에 일반적인 분포 오류가 있고 특정 형태의 사전 분포를 가정할 수 있는 경우 모델의 매개 변수에 대한 사후 확률 분포에 명시적인 결과를 사용할 수 있습니다. [[선형 회귀](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6f"></a> **[향상된 의사 결정 트리 회귀][boosted-decision-tree-regression]** 향상된 의사 결정 트리 회귀는 예측 및 응답 변수 간의 관계를 계산합니다. 회귀 트리 구조는 분류 트리와 유사합니다. 터미널 노드는 예측된 함수(모델) 값입니다. 예측된 값은 터미널 노드의 값으로 제한됩니다. 의사 결정 트리를 사용할 경우의 몇 가지 이점은 다음과 같습니다.

* 의사 결정 규칙을 해석하기 쉽습니다. 
* 비파라메트릭이므로 숫자 또는 범주 데이터 계층 범위를 사용하기 쉽고 단봉형 학습 데이터를 선택하지 않아도 됩니다.
* 학습 데이터의 이상치를 안정적으로 처리합니다. 
* 분류는 규칙이 개발되면 신속하게 수행될 수 있습니다. 

그러나 의사 결정 트리를 사용하면 단점도 있습니다.

* 전체 데이터 집합에 적용된 경우 적절하지 않은 결과가 나타나고 학습 데이터에 과적응될 수 있습니다.
* 학습 데이터에 대한 응답 변수의 최소 및 최대 제한을 초과 예측할 수는 없습니다.


<a name="anchor-6g"></a> **[의사 결정 포리스트 회귀][decision-forest-regression]** 의사 결정 포리스트는 분류(범주 변수) 또는 회귀(연속 변수) 분야에 사용될 수 있습니다. 회귀 포리스트는 독립된 입력이 제공된 경우 종속 변수의 비선형 회귀에 사용될 수 있으며 입력과 출력이 다차원일 수 있습니다. 회귀 포리스트는 해당 분류만큼 자주 사용되지 않습니다. 가장 큰 차이점은 의사 결정 포리스트의 출력 레이블은 입력 데이터(및 학습 레이블)와 연결되므로 학습 레이블이 연속적이어야 한다는 점입니다. 목적 함수를 적절히 조정해야 한다는 것을 의미합니다. 회귀 포리스트는 효율성 및 유연성과 같은 분류 포리스트의 많은 장점을 공유합니다.

<a name="anchor-6a"></a> **[선형 회귀][linear-regression]** 선형 회귀는 스칼라 종속 변수 Y와 하나 이상의 설명 변수 X 간의 관계를 모델링하는 데 널리 사용됩니다. 일반적으로 예측 또는 감소에 적용될 수 있습니다. 선형 회귀를 사용하여 관찰된 Y 및 X 값 데이터 집합에 예측 모델을 적합화할 수 있습니다. 선형 회귀에서는 Y의 기본 구조가 X 변수의 선형적 조합인 것으로 가정합니다. Y 값이 수반되지 않은 추가 X 값이 주어진 경우 적합화된 선형 회귀 모델을 사용하여 Y 값을 예측할 수 있습니다. 선형 회귀 모델은 일반적으로 최소 제곱법을 사용하여 적합화되지만 최상의 적합을 측정할 수 있는 다른 옵션도 제공합니다. [[선형 회귀](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6c"></a> **[신경망 네트워크 회귀][neural-network-regression]** 신경망은 비파라메트릭 회귀에 유용한 통계 도구입니다. 비패라메트릭 회귀는 가능한 설명 변수 X1; : : : ;Xp에 대해서 변수 Y에 대한 모델에 맞게 시도하는 문제를 해결하며, 여기서 X와 Y 간의 관계는 간단한 선형 관계보다 더 복잡할 수 있습니다. [[신경망을 사용한 비패라메트릭 회귀를 위한 프레임워크](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee (ISDS, Duke University)]

<a name="anchor-6d"></a> **[서수 회귀][ordinal-regression]** 서수 회귀는 서수 종속 변수를 모델링하거나 예측하는 데 사용되는 회귀 분석 유형입니다. 서수 종속 변수의 경우 값의 순위를 매길 수 있지만 범주 간의 실제 거리는 알 수 없습니다. 여러 값 간의 상대 순서만 유의합니다. 레이블 또는 대상 값에 자연 순서 또는 순위가 있으므로 모든 숫자 열을 서수 대상으로 사용할 수 있습니다. 숫자의 자연 순서는 숫자의 순위를 매기는 데 사용됩니다. 예를 들어 덜 심각에서 가장 심각까지 질병의 범위가 지정되는 경우, 설문 조사 응답자는 '매우 동의함'에서 '전적으로 부인'까지 응답을 선택합니다. A에서 F까지 학생의 등급이 지정되지만 원래 회귀는 로지스틱 회귀의 확장으로 *비례 승산* 모델을 기반으로 합니다.


<a name="anchor-6e"></a> **[포아송 회귀][poisson-regression]** 포아송 회귀는 모델링 개수 데이터에 자주 사용됩니다. 포아송 회귀에서는 응답 변수에 포아송 분포가 있는 것으로 가정합니다. 포아송 분포 데이터는 기본적으로 개수 데이터에 적용되는 정수 값(불연속 및 양수)입니다. 학습 데이터 집합이 주어진 경우 포아송 회귀에서는 입력이 주어진 매개 변수의 로그 가능성을 최대화하여 최적값을 찾습니다. 매개 변수의 가능성은 학습 데이터가 이러한 매개 변수를 사용하는 분포에서 샘플링된 확률입니다. 예를 들어 포아송 회귀는 다음과 같은 용도에 유용합니다.

* 비행기 항공편과 연관된 감기 횟수 모델링 
* 이벤트 또는 프로모션과 관련된 통화 횟수 예측 
* 대체 테이블 만들기

## 참조

기계 학습 스튜디오에서 사용 가능한 기계 학습 알고리즘의 전체 유형 목록은 [기계 학습 스튜디오 알고리즘 및 모듈 도움말](https://msdn.microsoft.com/library/azure/dn905974.aspx)의 [모델 초기화](https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/)를 참조하세요.

다음 참조에서 모든 유형의 기계 학습 알고리즘에 대한 자세한 정보를 찾을 수 있습니다. 이 문서를 만드는데 여러 정보가 사용되었습니다.

* [기계 학습 분류자 선택](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen.

* [Decision Forests for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf), A. Criminisi1, J. Shotton2 and E. Konukoglu (Microsoft Research, 2011) technical report TR-2011-114.

* [신경망을 사용한 비패라메트릭 회귀를 위한 프레임워크](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee (ISDS, Duke University).

* Handbook of Computational Statistics: Concepts and Methods edited by James E. Gentle, Wolfgang Karl Härdle, Yuichi Mori (Springer-Verlag Berlin Heidelberg New York, 2004).

* [10-12세 어린이 사이의 천식 유병율과 연관된 평가 요소에서 로지스틱 회귀 및 선형 판별식 분석: 두 통계 방식의 확산 및 유사성](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, Demosthenes B. Panagiotakos, Kostas N. Priftis, and Anastasia Tzonou (International Journal of Pediatrics, 2009) Article ID 952042.

* [The Optimality of Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (University of New Brunswick 2004) Harry Zhang.

* [Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore (Carnegie Mellon University 2001).

* [Machine Learning, Part II: Supervised and Unsupervised Learning](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizon](http://www.aihorizon.com/).

* [의사 결정 트리를 통한 로지스틱 회귀의 장점은 무엇입니까?](http://www.quora.com/What-are-the-advantages-of-logistic-regression-over-decision-trees) ([Quora](http://www.quora.com/)).

* [감독 학습 및 자율 학습 간의 차이는 무엇입니까?](http://stackoverflow.com/questions/1832076/what-is-the-difference-between-supervised-learning-and-unsupervised-learning) ([Stackoverflow](http://stackoverflow.com/)).

* [어떤 경우에 어떤 기계 학습 분류자를 선택합니까?](http://stackoverflow.com/questions/2595176/when-to-choose-which-machine-learning-classifier) ([Stackoverflow](http://stackoverflow.com/)).

* [Wikipedia](http://en.wikipedia.org):
	* [Bayesian 선형 회귀](http://en.wikipedia.org/wiki/Bayesian_linear_regression)
	* [선형 회귀](http://en.wikipedia.org/wiki/Linear_regression)
	* [다중 클래스 분류](http://en.wikipedia.org/wiki/Multiclass_classification)
	* [자율 학습](http://en.wikipedia.org/wiki/Unsupervised_learning)

다음도 참조하세요.

* [Microsoft Azure 기계 학습 알고리즘 치트 시트](machine-learning-algorithm-cheat-sheet.md) (Microsoft).

* [오른쪽 평가기 선택](http://scikit-learn.org/stable/tutorial/machine_learning_map/) ([scikit-학습](http://scikit-learn.org/stable/index.html)).


<!-- Module References -->
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[decision-forest-regression]: https://msdn.microsoft.com/library/azure/562988b2-e740-4e3a-8131-358391bad755/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[neural-network-regression]: https://msdn.microsoft.com/library/azure/d7ee222c-669f-4200-a576-a761a9c1a928/
[ordinal-regression]: https://msdn.microsoft.com/library/azure/ffb557f8-dc7f-44bd-8fd0-b25666dd23f1/
[poisson-regression]: https://msdn.microsoft.com/library/azure/80e21b9d-3827-40d8-b733-b53148becbc2/

 

<!---HONumber=August15_HO6-->