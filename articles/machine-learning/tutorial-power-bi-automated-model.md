---
title: '자습서: 자동화된 ML을 사용하여 예측 모델 만들기(2-1부)'
titleSuffix: Azure Machine Learning
description: 자동화된 ML 모델을 작성하고 배포하는 방법을 알아보고, Microsoft Power BI에서 최상의 모델을 사용하여 결과를 예측할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370876"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>자습서: Power BI 통합 - 자동화된 ML을 사용하여 예측 모델 만들기(2-1부)

이 자습서의 1부에서는 자동화된 ML을 사용하여 Azure Machine Learning 스튜디오에서 기계 학습 예측 모델을 학습시키고 배포합니다.  2부에서는 Microsoft Power BI에서 최상의 성능을 제공하는 모델을 사용하여 결과를 예측합니다.

이 자습서에서는 다음과 같은 작업을 수행했습니다.

> [!div class="checklist"]
> * Azure Machine Learning 컴퓨팅 클러스터 만들기
> * 데이터 세트 만들기
> * 자동화된 ML 실행 만들기
> * 실시간 채점 엔드포인트에 최상의 모델 배포


Power BI에서 사용할 모델을 만들고 배포하는 세 가지 방법이 있습니다.  이 문서에서는 '옵션 C: 스튜디오에서 자동화된 ML을 사용하여 모델 학습 및 배포'에 대해 설명합니다.  이 옵션은 데이터 준비 및 모델 학습을 완전히 자동화하는 코드 없음 제작 환경을 보여 줍니다. 

대신 다음을 사용할 수 있습니다.

* [옵션 A: Notebook을 사용하여 모델 학습 및 배포](tutorial-power-bi-custom-model.md) - Azure Machine Learning 스튜디오에서 호스팅되는 Jupyter Notebook을 사용하는 코드 우선 제작 환경입니다.
* [옵션 B: 디자이너를 사용하여 모델 학습 및 배포](tutorial-power-bi-designer-model.md) - 디자이너를 사용하는 로우 코드 제작 환경(끌어서 놓기 사용자 인터페이스)입니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독([평가판 사용 가능](https://aka.ms/AMLFree)) 
- Azure Machine Learning 작업 영역 작업 영역이 아직 없는 경우 [Azure Machine Learning 작업 영역을 만드는 방법](./how-to-manage-workspace.md#create-a-workspace)을 따르세요.

## <a name="create-compute-cluster"></a>컴퓨팅 클러스터 만들기

자동화된 ML은 "최상의" 알고리즘과 매개 변수를 찾기 위해 다양한 기계 학습 모델을 자동으로 학습시킵니다. Azure Machine Learning은 컴퓨팅 클러스터를 통해 모델 학습 실행을 병렬화합니다.

[Azure Machine Learning Studio](https://ml.azure.com)의 왼쪽 메뉴에서 **컴퓨팅** 을 선택한 다음, **컴퓨팅 클러스터** 탭을 선택합니다. **새로 만들기** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="컴퓨팅 클러스터를 만드는 방법을 보여 주는 스크린샷":::

**컴퓨팅 클러스터 만들기** 화면에서 다음을 수행합니다.

1. VM 크기를 선택합니다(이 자습서에서는 `Standard_D11_v2` 컴퓨터가 적절함).
1. **다음** 을 선택합니다.
1. 올바른 컴퓨팅 이름을 제공합니다.
1. **최소 노드 수** 를 0으로 유지합니다.
1. **최대 노드 수** 를 4로 변경합니다.
1. **만들기** 를 선택합니다.

클러스터 상태가 **만드는 중** 으로 변경되었음을 확인할 수 있습니다.

>[!NOTE]
> 클러스터가 만들어지면 노드가 0개입니다. 이 경우 컴퓨팅 비용이 발생하지 않습니다. 자동화된 ML 작업이 실행될 때만 비용이 발생합니다. 120초의 유휴 시간이 지나면 클러스터 크기가 자동으로 0으로 다시 조정됩니다.


## <a name="create-dataset"></a>데이터 세트 만들기

이 자습서에서는 [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)에서 제공되는 [당뇨병 데이터 세트](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)를 사용합니다.

데이터 세트를 만들려면 **데이터 세트** 왼쪽 메뉴를 선택한 다음, **데이터 세트 만들기** 를 선택합니다. 다음 옵션이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="데이터 세트를 만드는 방법을 보여 주는 스크린샷":::

**Open Datasets에서** 를 선택한 다음, **Open Datasets에서 데이터 세트 만들기** 화면에서 다음을 수행합니다.

1. 검색 창을 사용하여 *당뇨병* 을 검색합니다.
1. **샘플: 당뇨병** 을 선택합니다.
1. **다음** 을 선택합니다.
1. 데이터 세트 이름(*당뇨병*)을 지정합니다.
1. **만들기** 를 선택합니다.

데이터는 데이터 세트, **검색** 을 차례로 선택하여 검색할 수 있습니다.

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="데이터 세트를 검색하는 방법을 보여 주는 스크린샷":::

데이터에는 10개의 기준 입력 변수(예: 연령, 성별, 체질량 지수, 평균 혈압 및 6개 혈청 측정값) 및 **Y** 라는 하나의 대상 변수(기준 이후 1년간의 당뇨병 진행에 대한 정량 측정값)가 있습니다.

## <a name="create-automated-ml-run"></a>자동화된 ML 실행 만들기

[Azure Machine Learning Studio](https://ml.azure.com)의 왼쪽 메뉴에서 **자동화된 ML** 을 선택한 다음, **새 자동화된 ML 실행** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="새 자동화된 ML 실행을 만드는 방법을 보여 주는 스크린샷":::

다음으로, 이전에 만든 **당뇨병** 데이터 세트를 선택하고, **다음** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="데이터 세트를 선택하는 방법을 보여 주는 스크린샷":::
 
**실행 구성** 화면에서 다음을 수행합니다.

1. **실험 이름** 아래에서 **새로 만들기** 를 선택합니다.
1. 실험 이름 제공
1. [대상 열] 필드에서 **Y** 를 선택합니다.
1. **컴퓨팅 클러스터 선택** 필드에서 이전에 만든 컴퓨팅 클러스터를 선택합니다. 

완성된 양식이 다음과 같이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="자동화된 ML을 구성하는 방법을 보여 주는 스크린샷":::

마지막으로, 수행할 기계 학습 작업(**회귀**)을 선택해야 합니다.

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="작업을 구성하는 방법을 보여 주는 스크린샷":::

**마침** 을 선택합니다.

> [!IMPORTANT]
> 자동화된 ML에서 100개의 다른 모델에 대한 학습을 완료하는 데 30분 정도 걸립니다.

## <a name="deploy-the-best-model"></a>최적의 모델 배포

자동화된 ML 실행이 완료되면 **모델** 탭을 선택하여 시도된 다른 모든 기계 학습 모델의 목록을 확인할 수 있습니다. 모델은 성능 순서대로 정렬됩니다. 즉, 성능이 가장 좋은 모델이 먼저 표시됩니다. 최상의 모델을 선택하면 **배포** 단추가 사용하도록 설정됩니다.

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="모델 목록을 보여 주는 스크린샷":::

**배포** 를 선택하면 **모델 배포** 화면이 표시됩니다.

1. 모델 서비스 이름을 제공합니다(**diabetes-model** 사용).
1. **Azure Container Service** 를 선택합니다.
1. **배포** 를 선택합니다.

모델이 성공적으로 배포되었음을 알리는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 자동화된 ML을 사용하여 기계 학습 모델을 학습시키고 배포하는 방법을 살펴보았습니다. 다음 자습서에서는 Power BI에서 이 모델을 사용(채점)하는 방법을 보여 줍니다.

> [!div class="nextstepaction"]
> [자습서: Power BI에서 모델 사용](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
