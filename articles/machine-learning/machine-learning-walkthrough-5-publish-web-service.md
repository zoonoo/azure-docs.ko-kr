<properties
	pageTitle="5단계: 기계 학습 웹 서비스 배포 | Microsoft Azure"
	description="예측 솔루션 개발 연습 5단계: Azure 기계 학습 스튜디오에서 예측 실험을 웹 서비스로 배포합니다."
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
	ms.date="09/08/2015"
	ms.author="garye"/>


# 연습 5단계: Azure 기계 학습 웹 서비스 배포

[Azure 기계 학습을 사용한 예측 솔루션 개발](machine-learning-walkthrough-develop-predictive-solution.md) 자습서의 5번째 단계입니다.


1.	[기계 학습 작업 영역 만들기](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[기존 데이터 업로드](machine-learning-walkthrough-2-upload-data.md)
3.	[새 실험 만들기](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[모델 학습 및 평가](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**웹 서비스 배포**
6.	[웹 서비스 액세스](machine-learning-walkthrough-6-access-web-service.md)

----------

이 예측 모델을 다른 사용자가 사용할 수 있도록 Azure에 웹 서비스로 배포합니다.

지금까지는 모델을 학습하는 실험을 진행했습니다. 하지만 배포된 서비스는 더 이상 학습을 수행하지 않고 사용자 입력에 따라 예측을 생성합니다. 따라서 몇 가지 준비를 수행한 다음 이 실험을 사용자가 액세스할 수 있는 작업 웹 서비스로 배포하겠습니다. 사용자는 신용 확인서 데이터 집합을 서비스에 보낼 수 있고 서비스에서는 신용 위험 예측을 반환합니다.

이 작업을 하려면 다음이 필요합니다.

- 만든 *학습 실험*을 *예측 실험*으로 변환
- 예측 실험을 웹 서비스로 배포

하지만 먼저 이 실험을 약간 간단히 줄여야 합니다. 현재 실험에는 두 개의 다른 모델이 있지만 배포할 모델을 하나만 선택해야 합니다.

향상된 트리 모델을 사용할 더 효율적인 모델로 결정했다고 가정합니다. 가장 먼저 수행할 작업은 [2클래스 Support Vector Machine][two-class-support-vector-machine] 모듈과 학습에 사용된 모듈을 제거하는 것입니다. 먼저 실험 캔버스 아래쪽에 있는 **다른 이름으로 저장**을 클릭하여 실험 복사본을 만들 수 있습니다.

다음 모듈을 삭제해야 합니다.

1.	[2클래스 Support Vector Machine][two-class-support-vector-machine]
2.	연결된 [모델 학습][train-model] 및 [모델 점수 매기기][score-model] 모듈
3.	[데이터 표준화][normalize-data](둘 다)
4.	[모델 평가][evaluate-model]

이제 이 모델을 배포할 준비가 되었습니다.

## 학습 실험에서 예측 실험으로 변환

예측 실험으로 변환하는 프로세스는 다음 세 단계로 구성됩니다.

1. 학습한 모델을 저장하고 우리의 교육 모듈과 바꾸기
2. 학습에만 필요한 모듈을 제거하여 실험 축소
3. 웹 서비스의 입력 및 출력 노드 위치 정의

다행히도 실험 캔버스의 맨 아래에 있는 **웹 서비스 배포**를 클릭하여 세 단계를 모두 수행할 수 있습니다(**예측 웹 서비스** 옵션 선택).

**웹 서비스 배포**를 클릭하면 다음과 같은 결과가 발생합니다.

- 학습한 모델이 실험 캔버스 왼쪽의 모듈 팔레트에 **학습된 모델** 모듈로 저장됩니다(**학습된 모델** 아래에서 이 팔레트를 찾을 수 있음).
- 학습에 사용된 모듈은 제거됩니다. 구체적으로 살펴보면 다음과 같습니다.
  - [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree]
  - [모델 학습][train-model]
  - [분할][split]
  - 테스트 데이터에 사용된 두 번째 [R 스크립트 실행][execute-r-script] 모듈
- 저장된 학습된 모델이 실험에 추가됩니다.
- **웹 서비스 입력** 및 **웹 서비스 출력** 모듈이 추가됩니다.

> [AZURE.NOTE]실험은 원래 학습 실험과 새로운 예측 실험의 두 부분으로 저장되었습니다. 실험 캔버스의 위쪽에 있는 탭을 사용하여 실험에 액세스할 수 있습니다.

우리의 실험에서는 추가 단계를 수행해야 합니다. 기계 학습 스튜디오는 [분할][split] 모듈을 제거할 때 [R 스크립트 실행][execute-r-script] 모듈을 하나 제거했지만 다른 [R 스크립트 실행][execute-r-script] 모듈은 남겨 두었습니다. 이 모듈은 학습 및 테스트(샘플 데이터에서 가중치 기능 제공)에만 사용되었으므로 이제 제거한 다음 [메타데이터 편집기][metadata-editor]를 [모델 점수 매기기][score-model]에 연결할 수 있습니다.

이제 실험은 다음과 같이 표시됩니다.

![학습된 모델 점수 매기기][4]


예측 실험에서 UCI 독일어 신용 카드 데이터의 데이터 집합을 남겨둔 이유가 궁금할 것입니다. 서비스에서는 원래 데이터 집합이 아니라 사용자 데이터를 사용하려고 하는데 연결된 상태로 두는 이유는 무엇인가요?

서비스에 원래 신용 카드 데이터가 필요하지 않은 것은 사실입니다. 하지만 열 수 및 숫자인 열 같은 정보가 포함된 해당 데이터에 대한 스키마는 필요하지 않습니다. 이 스키마 정보는 사용자 데이터를 해석하기 위해 필요합니다. 서비스가 실행 중일 때 점수 매기기 모듈에 데이터 집합 스키마가 포함되도록 이러한 구성 요소를 연결된 상태로 유지합니다. 데이터는 사용되지 않고 스키마만 사용됩니다.

마지막으로 실험을 한 번 실행합니다(**실행** 클릭). 모델이 계속 작동 중인지 확인하려면 [모델 점수 매기기][score-model] 모듈의 출력을 클릭하고 **결과 보기**를 선택합니다. 원래 데이터가 신용 위험 값("점수를 매긴 레이블") 및 점수 매기기 확률 값("점수를 매긴 확률")과 함께 표시됩니다.

## 웹 서비스 배포

실험에서 파생된 웹 서비스를 배포하려면 캔버스 아래에서 **웹 서비스 배포**를 클릭합니다. 기계 학습 스튜디오에서 실험을 웹 서비스로 배포하고 서비스 대시보드로 이동합니다.

> [AZURE.TIP]웹 서비스를 배포하고 나서 업데이트할 수 있습니다. 예를 들어 모델을 변경하려면 학습 실험을 편집하고, 모델 매개 변수를 조정하고, **웹 서비스 배포**를 클릭합니다. 실험을 다시 배포하면 이 실험이 웹 서비스를 대체하고 이제 업데이트된 모델을 사용합니다.

**구성** 탭을 클릭하여 서비스를 구성할 수 있습니다. 여기서 서비스 이름(기본적으로 실험 이름이 지정됨)을 수정하고 설명을 제공할 수 있습니다. 입력 및 출력 열에 대해 더 친숙한 레이블을 제공할 수도 있습니다.

## 웹 서비스 테스트
**대시보드** 페이지에서 **기본 끝점** 아래의 **테스트** 링크를 클릭합니다. 대화 상자가 팝업되고 서비스에 대한 입력 데이터를 요구합니다. 이는 원래 독일 신용 위험 데이터 집합에 나타난 열과 같습니다.

데이터 집합을 입력하고 **확인**을 클릭합니다.

웹 서비스에서 생성된 결과가 대시보드 아래쪽에 표시됩니다. 서비스를 구성한 대로 표시된 결과는 점수 매기기 모듈에서 생성됩니다.


----------

**다음: [웹 서비스 액세스](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/

<!---HONumber=Oct15_HO3-->