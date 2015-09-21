<properties
	pageTitle="기계 학습 스튜디오에서 간단한 실험 만들기 | Microsoft Azure"
	description="Azure 기계 학습 스튜디오에서 선형 회귀 모델을 테스트할 간단한 실험을 만들기 위한 첫번째 기계 학습 자습서입니다."
	keywords="experiment,linear regression,machine learning algorithms,machine learning tutorial,predictive modeling techniques"
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
	ms.topic="hero-article"
	ms.date="09/09/2015"
	ms.author="garye"/>

#기계 학습 자습서: Azure 기계 학습 스튜디오에서 첫 번째 실험 만들기

이 첫 번째 기계 학습 자습서에서는 제조업체 및 기술 사양과 같은 여러 변수에 따라 자동차 가격을 예측하는 선형 회귀 모델을 만듭니다. 이를 위해 Azure 기계 학습 스튜디오를 사용하여 간단한 예측 분석 실험을 개발하고 반복하겠습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

기계 학습 스튜디오 실험은 *모델을 만들고*, *모델을 학습하고*, *모델의 점수를 매기고 테스트*하기 위해 캔버스에 구성 요소를 끌어서 놓기와 연결하기로 구성됩니다. 실험은 데이터를 수집하고 모델의 성향을 습득하고 새 데이터에 모델을 적용하는 기계 학습 스튜디오 모듈의 형태로 예측 모델링 기술을 사용합니다. 데이터 전처리 모듈을 추가하고, 기능을 선택하고, 학습 및 테스트 집합으로 데이터를 분리하고, 모델 품질을 평가하거나 교차 검사할 수도 있습니다.

기계 학습 스튜디오 [https://studio.azureml.net](https://studio.azureml.net)으로 들어가서 **Get started** 단추를 클릭합니다. 게스트 액세스를 선택하거나 Microsoft 계정을 사용하여 로그인할 수 있습니다.

기계 학습 스튜디오에 대한 일반적인 정보는 [기계 학습 스튜디오란 무엇인가요?](machine-learning-what-is-ml-studio.md)를 참조하세요.


##실험 만들기 5단계

이 기계 학습 자습서에서 모델을 만들고 학습하고 점수를 매기도록 기계 학습 스튜디오에서 실험을 빌드하기 위해 따르는 5가지 기본 단계를 따릅니다.

- 모델 만들기
	- [1단계: 데이터 가져오기]
	- [2단계: 데이터 전처리]
	- [3단계: 기능 정의]
- 모델 학습
	- [4단계: 학습 알고리즘 선택 및 적용]
- 모델 점수 매기기 및 테스트
	- [5단계: 새 자동차 가격 예측]

[1단계: 데이터 가져오기]: #step-1-get-data
[2단계: 데이터 전처리]: #step-2-preprocess-data
[3단계: 기능 정의]: #step-3-define-features
[4단계: 학습 알고리즘 선택 및 적용]: #step-4-choose-and-apply-a-learning-algorithm
[5단계: 새 자동차 가격 예측]: #step-5-predict-new-automobile-prices


## 1단계: 데이터 가져오기

선택할 수 있는 여러 샘플 데이터 집합이 기계 학습 스튜디오에 포함되어 있으며, 다양한 원본에서 데이터를 가져올 수 있습니다. 이 예제에서는 포함된 샘플 데이터 집합 **Automobile price data (Raw)**를 사용합니다. 이 데이터 집합에는 제조업체, 모델, 기술 사양 및 가격과 같은 정보를 포함하여 여러 개별 자동차에 대한 항목이 포함되어 있습니다.

1. 기계 학습 스튜디오 창의 아래쪽에서 **+NEW**를 클릭하여 새 실험을 시작한 다음 **EXPERIMENT**, **Blank Experiment**를 차례로 선택합니다. 캔버스 위쪽에서 기본 실험 이름을 선택하고 이를 의미 있는 이름(예: **Automobile price prediction**)으로 바꿉니다.

2. 실험 캔버스 왼쪽에는 데이터 집합과 모듈의 팔레트가 있습니다. 이 팔레트 맨 위에 있는 검색 상자에 **automobile**을 입력하여 레이블이 **Automobile price data (Raw)**인 데이터 집합을 찾습니다.

	![팔레트 검색][screen1a]

3. 실험 캔버스에 데이터 집합을 끌어 놓습니다.

	![데이터 집합][screen1]

이 데이터의 모양을 확인하려면 자동차 데이터 집합 아래에서 출력 포트를 두 번 클릭한 다음 **Visualize**를 선택합니다. 데이터 집합에서 변수는 열로 나타나고, 각 자동차 인스턴스는 행으로 나타납니다. 맨 오른쪽 열인 "price"(26열)가 예측할 대상 변수입니다.

![데이터 집합 시각화][screen1b]

오른쪽 위 모서리에서 "**x**"를 클릭하여 시각화 창을 닫습니다.

## 2단계: 데이터 전처리

데이터 집합은 일반적으로 전처리를 거쳐야 분석할 수 있습니다. 여러 행의 열에 누락된 값이 있는 것을 볼 수 있습니다. 모델에서 데이터를 올바르게 분석할 수 있도록 이러한 누락된 값을 정리해야 합니다. 지금은 누락된 값이 있는 행을 모두 제거하겠습니다. 또한 **normalized-losses** 열에 값이 누락된 비율이 크므로 여기서는 해당 열을 모델에서 완전히 제외하겠습니다.

> [AZURE.TIP]입력 데이터에서 누락 값을 정리하는 것은 대부분의 모듈을 사용하기 위한 필수 조건입니다.

먼저 **normalized-losses** 열을 제거한 다음 누락된 데이터가 있는 행을 제거하겠습니다.

1. 모듈 팔레트의 맨 위에 있는 검색 상자에 **project columns**를 입력하여 [Project Columns][project-columns] 모듈을 찾은 다음 이를 실험 캔버스로 끌어와 **Automobile price data (Raw)** 데이터 집합의 출력 포트에 연결합니다. 이 모듈을 사용하면 모델에서 포함하거나 제외할 데이터 열을 선택할 수 있습니다.

2. [Project Columns][project-columns] 모듈을 선택하고 **Properties** 창에서 **Launch column selector**을 클릭합니다.

	- 필터 드롭다운 **Begin With** 목록에서 **All columns**를 선택합니다. 그러면 모든 열을 전달하는 [Project Columns][project-columns]로 리디렉션됩니다(제외할 열은 예외임).
	- 다음 행에서 **제외** 및 **열 이름**을 선택한 후 텍스트 상자 내부를 클릭합니다. 열 목록이 표시됩니다. **normalized-losses**를 선택하면 텍스트 상자에 추가됩니다.
	- 확인 표시(확인) 단추를 클릭하여 열 선택기를 닫습니다.

    ![열 선택][screen3]

	**Project Columns**의 속성 창에 **normalized-losses**를 제외하고 데이터 집합의 모든 열이 전달된다는 것이 나타납니다.

    ![프로젝트 열 속성][screen4]

    > [AZURE.TIP]모듈을 두 번 클릭하고 텍스트를 입력하여 모듈에 주석을 추가할 수 있습니다. 그러면 모듈이 실험에서 수행하는 내용을 한눈에 볼 수 있습니다. 이 경우 [Project Columns][project-columns] 모듈을 두 번 클릭하고 주석 "Exclude normalized-losses"를 입력합니다.

3. [Clean Missing Data][clean-missing-data] 모듈을 실험 캔버스로 끌어 놓고 [Project Columns][project-columns] 모듈과 연결합니다. **Properties** 창에서 **Cleaning mode** 아래의 **Remove entire row**를 선택하여 누락 값이 있는 행을 제거하는 방법으로 데이터를 정리합니다. 모듈을 두 번 클릭하고 주석 "Remove missing value rows"를 입력합니다.

	![Clean Missing Data 속성][screen4a]

4. 실험 캔버스 아래에서 **RUN**을 클릭하여 실험을 실행합니다.

실험이 완료되면 모든 모듈에 성공적으로 완료되었음을 나타내는 녹색 확인 표시가 표시됩니다. 오른쪽 위 모서리에서 **Finished running** 상태도 확인됩니다.

![첫 번째 실험 실행][screen5]

이 시점까지 완료된 모든 실험은 데이터를 정리합니다. 정리된 데이터 집합을 보려면 [Clean Missing Data][clean-missing-data] 모듈의 왼쪽 출력 포트("정리된 데이터 집합")를 클릭하고 **Visualize**를 선택합니다. **normalized-losses** 열이 더 이상 포함되지 않으며, 누락된 값이 없습니다.

데이터가 정리되었으며, 이제 예측 모델에서 사용할 기능을 지정할 수 있습니다.

## 3단계: 기능 정의

기계 학습에서 *기능*은 관심 있는 부분에 대한 측정 가능한 개별 속성입니다 여기서는 데이터 집합의 각 행이 하나의 자동차를 나타내고 각 열은 해당 자동차의 기능입니다. 예측 모델을 만들기에 적절한 기능 집합을 찾으려면 해결하려는 문제에 대한 실험과 지식이 있어야 합니다. 일부 기능은 다른 기능에 비해 대상 예측에 더 유용합니다. 또한 일부 기능은 다른 기능과 강력한 상관 관계를 가지기도 합니다(예: city-mpg와 highway-mpg). 이런 경우 해당 기능은 모델에 많은 새 정보를 추가하지 않으며 제거할 수 있습니다.

데이터 집합에서 기능 하위 집합을 사용하는 모델을 빌드하겠습니다. 다시 돌아와서 다양한 특성을 선택하고 실험을 다시 실행하여 더 나은 결과가 나오는지 확인할 수 있습니다. 첫 번째 추측으로서, [프로젝트 열][project-columns] 모듈을 사용하여 다음 특성(열)을 선택합니다. 모델 학습을 위해서는 예측하려는 *가격* 값을 포함해야 합니다.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. 다른 [Project Columns][project-columns] 모듈을 실험 캔버스로 끌어 놓고 [Clean Missing Data][clean-missing-data] 모듈의 왼쪽 출력 포트에 연결합니다. 모듈을 두 번 클릭하고 "Select features for prediction"을 입력합니다.

2. **Properties** 창에서 **Launch column selector**를 클릭합니다.

3. 열 선택기에서 **Begin With**로 **No columns**를 선택한 후 필터 행에서 **Include** 및 **column names**를 선택합니다. 열 이름 목록을 입력합니다. 그러면 지정한 열만 전달하는 모듈로 리디렉션됩니다.

	> [AZURE.TIP]실험을 실행했기 때문에 원본 데이터 집합에서 가져온 데이터에 대한 열 정의가 [Clean Missing Data][clean-missing-data] 모듈을 통해 전달되었습니다. [Project Columns][project-columns]를 [Clean Missing Data][clean-missing-data]에 연결하면 [Project Columns][project-columns] 모듈이 데이터의 열 정의를 인식하게 됩니다. **column names** 상자를 클릭하면 열 목록이 표시되고, 목록에 추가하려는 열을 선택할 수 있습니다.

4. 확인 표시(확인) 단추를 클릭합니다.

![열 선택][screen6]

그러면 다음 단계의 학습 알고리즘에서 사용될 데이터 집합이 생성됩니다. 나중에 돌아와서 다른 선택 기능을 사용하여 다시 시도할 수 있습니다.

## 4단계: 학습 알고리즘 선택 및 적용

데이터가 준비되었으며, 예측 모델 생성은 학습과 테스트로 구성됩니다. 데이터를 사용하여 모델을 학습한 다음 모델을 테스트하여 어느 정도 근접한 가격을 예측할 수 있는지 확인합니다.

*분류*와 *회귀*가 관리 기계 학습 기술의 두 형식입니다. 분류는 색(빨강, 파랑 또는 녹색)과 같이 정의된 값 집합에서 예측하는 데 사용됩니다. 회귀는 사람의 나이와 같이 연속된 값 집합에서 예측하는 데 사용됩니다.

임의의 값일 수 있는 자동차 가격을 예측하려고 하므로 회귀 모델을 사용하겠습니다. 이 예제에서는 간단한 *선형 회귀* 모델을 학습하고 다음 단계에서 이를 테스트합니다.

1. 데이터를 별도의 학습 및 테스트 집합으로 분할하여 학습과 테스트 모두에 데이터를 사용할 수 있습니다. [분할][split] 모듈을 선택하여 실험 캔버스로 끌어 놓고 마지막 [프로젝트 열][project-columns] 모듈의 출력과 연결합니다. **첫 번째 출력 데이터 집합의 행 분수**를 0.75로 설정합니다. 그러면 데이터의 75%를 모델 학습에 사용하고 25%는 테스트용으로 보유합니다.

	> [AZURE.TIP]**Random seed** 매개 변수를 변경하면 학습 및 테스트용으로 서로 다른 무작위 샘플을 생성할 수 있습니다. 이 매개 변수는 난수 발생기의 시드를 제어합니다.

2. 실험을 실행합니다. 그러면 [Project Columns][project-columns] 및 [Split][split] 모듈이 다음에 추가할 모듈에 열 정의를 전달할 수 있습니다.

3. 학습 알고리즘을 선택하려면 캔버스 왼쪽의 모듈 팔레트에서 **Machine Learning** 범주를 확장한 후 **Initialize Model**을 확장합니다. 기계 학습 알고리즘을 초기화하는 데 사용할 수 있는 몇 가지 범주의 모듈이 표시됩니다.

	이 예제 학습에서는 **Regression** 범주 아래에 있는 [Linear Regression][linear-regression] 모듈을 선택하여(팔레트 검색 상자에 "linear regression"을 입력하여 모듈을 찾을 수도 있음) 실험 캔버스로 끌어 놓습니다.

4. [Train Model][train-model] 모듈을 찾아 실험 캔버스로 끌어 놓습니다. 왼쪽 입력 포트를 [Linear Regression][linear-regression] 모듈의 출력에 연결합니다. 오른쪽 입력 포트를 [Split][split] 모듈의 학습 데이터 출력(왼쪽 포트)에 연결합니다.

5. [Train Model][train-model] 모듈을 선택하고 **Properties** 창에서 **Launch column selector**를 클릭한 다음 **price** 열을 선택합니다. 모델이 예측할 값입니다.

	!["price" 열 선택][screen7]

6. 실험을 실행합니다.

결과는 새 샘플의 점수를 매기고 예측하는 데 사용할 수 있는 학습된 회귀 모델입니다.

![기계 학습 알고리즘 적용][screen8]

## 5단계: 새 자동차 가격 예측

데이터의 75%를 사용하여 모델을 학습했으며, 이제 모델을 사용하여 나머지 25% 데이터의 점수를 매겨 모델 기능이 얼마나 좋은지 확인할 수 있습니다.

1. [Score Model][score-model] 모듈을 찾아서 실험 캔버스로 끌어온 후 왼쪽 입력 포트를 [Train Model][train-model] 모듈의 출력에 연결합니다. 오른쪽 입력 포트를 [Split][split] 모듈의 테스트 데이터 출력(오른쪽 포트)에 연결합니다.  

	![모델 점수 매기기 모듈][screen8a]

2. 실험을 실행하고 [Score Model][score-model] 모듈의 출력을 보려면 출력 포트를 클릭한 다음 **Visualize**를 선택합니다. 출력에 테스트 데이터에서 가져온 알려진 값과 함께 가격 예측 값이 표시됩니다.

3. 마지막으로 결과의 품질을 테스트하기 위해 [Evaluate Model][evaluate-model] 모듈을 선택하고 실험 캔버스로 끌어온 후 왼쪽 입력 포트를 [Score Model][score-model] 모듈의 출력에 연결합니다. [Evaluate Model][evaluate-model] 모듈은 두 개의 모델을 비교하는 데 사용될 수 있으므로 두 개의 입력 포트가 있습니다.

4. 실험을 실행합니다.

[Evaluate Model][evaluate-model] 모듈의 출력을 보려면 출력 포트를 클릭한 다음 **Visualize**를 선택합니다. 모델에 대한 다음 통계가 표시됩니다.

- MAE(**Mean Absolute Error**): 절대 평균 오차입니다(*error*는 예측 값과 실제 값 사이의 차이).
- RMSE(**Root Mean Squared Error**): 테스트 데이터 집합에 대해 예측한 평균 제곱 오차의 제곱근입니다.
- **Relative Absolute Error**: 실제 값과 모든 실제 값 평균 사이의 절대값 차에 대해 상대적인 절대 오차의 평균입니다.
- **Relative Squared Error**: 실제 값과 모든 실제 값 평균 사이의 제곱 차에 대해 상대적인 평균 제곱 오차입니다.
- **Coefficient of Determination**: **R 제곱 값**이라고도 하며, 모델이 데이터에 얼마나 적합한지 나타내는 통계 메트릭입니다.

각 오차 통계는 작을수록 좋습니다. 값이 작을수록 예측이 실제 값과 더 근접하게 일치함을 나타냅니다. **결정 계수**의 경우 값이 1(1.0)에 가까울수록 더 잘 예측한 것입니다.

![평가 결과][screen9]

최종 실험은 다음과 같습니다.

![기계 학습 자습서: 예측 모델링 기술을 사용하는 선형 회귀 실험을 완료합니다.][screen10]

## 다음 단계

이제 첫번째 기계 학습 자습서를 완료하고 실험을 설정했으므로 반복하여 모델을 향상시킬 수 있습니다. 예를 들어 예측에 사용하는 특성을 변경할 수 있습니다. 또는 [선형 회귀][linear-regression] 알고리즘의 속성을 수정하거나 다른 알고리즘을 시도해 볼 수 있습니다. 한 번에 여러 기계 학습 알고리즘을 실험에 추가하고 [Evaluate Model][evaluate-model] 모듈을 사용하여 둘을 비교할 수도 있습니다.

> [AZURE.TIP]실험 반복의 복사본을 만들려면 실험 캔버스 아래에 있는 **SAVE AS** 단추를 사용하세요. 캔버스 아래에서 **VIEW RUN HISTORY**를 클릭하여 실험의 모든 반복을 볼 수 있습니다. 자세한 내용은 [Azure 기계 학습 스튜디오에서 실험 반복 관리][runhistory]를 참조하세요.

[runhistory]: machine-learning-manage-experiment-iterations.md

모델이 마음에 들면 모델을 새 데이터를 사용한 자동차 가격 예측에 사용할 웹 서비스로 배포할 수 있습니다. 자세한 내용은 [Azure 기계 학습 웹 서비스 배포][publish]를 참조하세요.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

모델 만들기, 학습, 점수 매기기 및 배포를 위한 예측 모델링 기술에 대한 보다 광범위하고 자세한 연습은 [Azure 기계 학습을 사용하여 예측 솔루션 개발][walkthrough]을 참조하세요.

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]: ./media/machine-learning-create-experiment/screen1.png
[screen1a]: ./media/machine-learning-create-experiment/screen1a.png
[screen1b]: ./media/machine-learning-create-experiment/screen1b.png
[screen2]: ./media/machine-learning-create-experiment/screen2.png
[screen3]: ./media/machine-learning-create-experiment/screen3.png
[screen4]: ./media/machine-learning-create-experiment/screen4.png
[screen4a]: ./media/machine-learning-create-experiment/screen4a.png
[screen5]: ./media/machine-learning-create-experiment/screen5.png
[screen6]: ./media/machine-learning-create-experiment/screen6.png
[screen7]: ./media/machine-learning-create-experiment/screen7.png
[screen8]: ./media/machine-learning-create-experiment/screen8.png
[screen8a]: ./media/machine-learning-create-experiment/screen8a.png
[screen9]: ./media/machine-learning-create-experiment/screen9.png
[screen10]: ./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=Sept15_HO2-->