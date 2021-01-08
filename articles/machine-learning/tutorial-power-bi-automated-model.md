---
title: '자습서: 자동화된 Machine Learning을 사용하여 예측 모델 만들기(1/2부)'
titleSuffix: Azure Machine Learning
description: 자동화된 Machine Learning 모델을 빌드하고 배포하는 방법을 알아보고, Microsoft Power BI에서 최상의 모델을 사용하여 결과를 예측할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814808"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>자습서: Power BI 통합 - 자동화된 Machine Learning을 사용하여 예측 모델 만들기(1/2부)

이 자습서의 1부에서는 기계 학습 예측 모델을 학습시키고 배포합니다. Azure Machine Learning 스튜디오에서 자동화된 Machine Learning을 사용합니다.  2부에서는 Microsoft Power BI에서 최상의 성능을 제공하는 모델을 사용하여 결과를 예측합니다.

이 자습서에서는 다음과 같은 작업을 수행했습니다.

> [!div class="checklist"]
> * Azure Machine Learning 컴퓨팅 클러스터를 만듭니다.
> * 데이터 세트를 만듭니다.
> * 자동화된 Machine Learning 실행을 만듭니다.
> * 실시간 채점 엔드포인트에 최상의 모델을 배포합니다.


Power BI에서 사용할 모델을 만들고 배포하는 방법은 세 가지가 있습니다.  이 문서에서는 "옵션 C: 스튜디오에서 자동화된 Machine Learning을 사용하여 모델 학습 및 배포"를 설명합니다.  이 옵션은 코드 없음 작성 환경입니다. 데이터 준비 및 모델 학습을 완전히 자동화합니다. 

그러나 다른 옵션 중 하나를 대신 사용할 수 있습니다.

* [옵션 A: Jupyter Notebook을 사용하여 모델 학습 및 배포](tutorial-power-bi-custom-model.md). 이는 Azure Machine Learning 스튜디오에서 호스팅되는 Jupyter Notebook을 사용하는 코드 우선 작성 환경입니다.
* [옵션 B: Azure Machine Learning 디자이너를 사용하여 모델 학습 및 배포](tutorial-power-bi-designer-model.md) 이는 끌어서 놓기 사용자 인터페이스를 사용하는 로우 코드 작성 환경입니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 구독이 아직 없는 경우 [평가판](https://aka.ms/AMLFree)을 사용할 수 있습니다. 
- Azure Machine Learning 작업 영역 작업 영역이 아직 없는 경우 [Azure Machine Learning 작업 영역 만들기 및 관리](./how-to-manage-workspace.md#create-a-workspace)를 참조하세요.

## <a name="create-a-compute-cluster"></a>컴퓨팅 클러스터 만들기

자동화된 Machine Learning은 "최상의" 알고리즘과 매개 변수를 찾기 위해 다양한 기계 학습 모델을 학습시킵니다. Azure Machine Learning은 컴퓨팅 클러스터를 통해 모델 학습 실행을 병렬화합니다.

시작하려면 [Azure Machine Learning 스튜디오](https://ml.azure.com)의 왼쪽 메뉴에서 **컴퓨팅** 을 선택합니다. **컴퓨팅 클러스터** 탭을 엽니다. 그런 다음, **새로 만들기** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="컴퓨팅 클러스터를 만드는 방법을 보여 주는 스크린샷":::

**컴퓨팅 클러스터 만들기** 페이지에서 다음을 수행합니다.

1. VM 크기를 선택합니다. 이 자습서에서는 **Standard_D11_v2** 머신이 좋습니다.
1. **다음** 을 선택합니다.
1. 올바른 컴퓨팅 이름을 제공합니다.
1. **최소 노드 수** 를 `0`으로 유지합니다.
1. **최대 노드 수** 를 `4`로 변경합니다.
1. **만들기** 를 선택합니다.

클러스터 상태가 **만드는 중** 으로 변경됩니다.

>[!NOTE]
> 새 클러스터의 노드 수는 0개이므로 컴퓨팅 비용이 발생하지 않습니다. 자동화된 Machine Learning 작업을 실행하는 경우에만 비용이 발생합니다. 120초의 유휴 시간이 지나면 클러스터 크기가 자동으로 0으로 다시 조정됩니다.


## <a name="create-a-dataset"></a>데이터 세트 만들기

이 자습서에서는 [당뇨병 데이터 세트](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)를 사용합니다. 이 데이터 세트는 [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)에 있습니다.

데이터 세트를 만들려면 왼쪽 메뉴에서 **데이터 세트** 를 선택합니다. 그런 다음, **데이터 세트 만들기** 를 선택합니다. 다음 옵션이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="데이터 세트를 만드는 방법을 보여 주는 스크린샷":::

**Open Datasets에서** 를 선택합니다. 그런 다음, **Open Datasets에서 데이터 세트 만들기** 페이지에서 다음을 수행합니다.

1. 검색 창을 사용하여 *당뇨병* 을 찾습니다.
1. **샘플: 당뇨병** 을 선택합니다.
1. **다음** 을 선택합니다.
1. 데이터 세트의 이름을 *당뇨병* 으로 지정합니다.
1. **만들기** 를 선택합니다.

데이터를 검색하려면 데이터 세트를 선택하고 **검색** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="데이터 세트를 검색하는 방법을 보여 주는 스크린샷":::

데이터에는 연령, 성별, 체질량 지수, 평균 혈압 및 6개 혈청 측정값 등 10개의 기준 입력 변수가 있습니다. 또한 **Y** 라는 하나의 대상 변수가 있습니다. 이 대상 변수는 기준 1년 후 당뇨병의 진행 과정을 정량적으로 측정한 것입니다.

## <a name="create-an-automated-machine-learning-run"></a>자동화된 Machine Learning 실행 만들기

[Azure Machine Learning 스튜디오](https://ml.azure.com)의 왼쪽 메뉴에서 **자동화된 Machine Learning** 을 선택합니다. 그런 다음, **새 자동화된 Machine Learning 실행** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="새 자동화된 Machine Learning 실행을 만드는 방법을 보여 주는 스크린샷":::

다음으로, 이전에 만든 **당뇨병** 데이터 세트를 선택합니다. 그리고 **다음** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="데이터 세트를 선택하는 방법을 보여 주는 스크린샷":::
 
**실행 구성** 페이지에서 다음을 수행합니다.

1. **실험 이름** 아래에서 **새로 만들기** 를 선택합니다.
1. 실험의 이름을 지정합니다.
1. **대상 열** 필드에서 **Y** 를 선택합니다.
1. **컴퓨팅 클러스터 선택** 필드에서 이전에 만든 컴퓨팅 클러스터를 선택합니다. 

완성된 양식이 다음과 같이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="자동화된 Machine Learning을 구성하는 방법을 보여 주는 스크린샷":::

마지막으로, 기계 학습 작업을 선택합니다. 이 경우 작업은 **회귀** 입니다.

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="작업을 구성하는 방법을 보여 주는 스크린샷":::

**마침** 을 선택합니다.

> [!IMPORTANT]
> 자동화된 Machine Learning이 100개 모델 학습을 완료하는 데 약 30분이 걸립니다.

## <a name="deploy-the-best-model"></a>최적의 모델 배포

자동화된 Machine Learning이 완료되면 **모델** 탭을 선택하여 시도된 다른 모든 기계 학습 모델을 확인할 수 있습니다. 모델은 성능별로 정렬됩니다. 즉, 성능이 가장 좋은 모델이 먼저 표시됩니다. 최상의 모델을 선택하면 **배포** 단추가 사용하도록 설정됩니다.

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="모델 목록을 보여 주는 스크린샷":::

**배포** 를 선택하여 **모델 배포** 창을 엽니다.

1. 모델 서비스에 *diabetes-model* 이라는 이름을 지정합니다.
1. **Azure Container Service** 를 선택합니다.
1. **배포** 를 선택합니다.

모델이 성공적으로 배포되었음을 알리는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 자동화된 Machine Learning을 사용하여 기계 학습 모델을 학습시키고 배포하는 방법을 살펴보았습니다. 다음 자습서에서는 Power BI에서 이 모델을 사용(채점)하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Power BI에서 모델 사용](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
