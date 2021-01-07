---
title: '자습서: 끌어서 놓기를 사용하여 예측 모델 만들기(2-1부)'
titleSuffix: Azure Machine Learning
description: 디자이너를 사용하여 기계 학습 예측 모델을 작성하고 배포하는 방법을 알아보고, Microsoft Power BI에서 이를 사용하여 결과를 예측할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370788"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>자습서:  Power BI 통합 - 끌어서 놓기를 사용하여 예측 모델 만들기(2-1부)

이 자습서의 1부에서는 로우 코드 끌어서 놓기 사용자 인터페이스인 Azure Machine Learning 디자이너를 사용하여 기계 학습 예측 모델을 학습시키고 배포합니다. 2부에서는 Microsoft Power BI에서 모델을 사용하여 결과를 예측합니다.

이 자습서에서는 다음과 같은 작업을 수행했습니다.

> [!div class="checklist"]
> * Azure Machine Learning 컴퓨팅 인스턴스 만들기
> * Azure Machine Learning 유추 클러스터 만들기
> * 데이터 세트 만들기
> * 회귀 모델 학습
> * 실시간 채점 엔드포인트에 모델 배포


Power BI에서 사용할 모델을 만들고 배포하는 세 가지 방법이 있습니다.  이 문서에서는 '옵션 B: 디자이너를 사용하여 모델 학습 및 배포'에 대해 설명합니다.  이 옵션은 디자이너(끌어서 놓기 사용자 인터페이스)를 사용하는 로우 코드 제작 환경을 보여 줍니다.  

대신 다음을 사용할 수 있습니다.

* [옵션 A: Notebook을 사용하여 모델 학습 및 배포](tutorial-power-bi-custom-model.md) - Azure Machine Learning 스튜디오에서 호스팅되는 Jupyter Notebook을 사용하는 코드 우선 제작 환경입니다.
* [옵션 C: 자동화된 ML을 사용하여 모델 학습 및 배포](tutorial-power-bi-automated-model.md) - 데이터 준비 및 모델 학습을 완전히 자동화하는 코드 없음 제작 환경입니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독([평가판 사용 가능](https://aka.ms/AMLFree)) 
- Azure Machine Learning 작업 영역 작업 영역이 아직 없는 경우 [Azure Machine Learning 작업 영역을 만드는 방법](./how-to-manage-workspace.md#create-a-workspace)을 따르세요.
- 기계 학습 워크플로에 대한 기초 지식


## <a name="create-compute-for-training-and-scoring"></a>학습 및 채점 컴퓨팅 만들기

이 섹션에서는 기계 학습 모델 학습에 사용되는 *컴퓨팅 인스턴스* 를 만듭니다. 또한 실시간 채점을 위해 배포된 모델을 호스팅하는 데 사용할 *유추 클러스터* 를 만듭니다.

[Azure Machine Learning 스튜디오](https://ml.azure.com)에 로그인하고, 왼쪽 메뉴에서 **컴퓨팅** 을 선택한 다음, **새로 만들기** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="컴퓨팅 인스턴스를 만드는 방법을 보여 주는 스크린샷":::

결과 **컴퓨팅 인스턴스 만들기** 화면에서 VM 크기를 선택하고(이 자습서의 경우 `Standard_D11_v2` 선택), **다음** 을 선택합니다. [설정] 페이지에서 올바른 컴퓨팅 인스턴스 이름을 입력한 다음, **만들기** 를 선택합니다. 

>[!TIP]
> 컴퓨팅 인스턴스를 사용하여 Notebook을 만들고 실행할 수도 있습니다.

이제 컴퓨팅 인스턴스 **상태** 가 **만드는 중** 임을 확인할 수 있습니다. 컴퓨터를 프로비저닝하는 데 4분 정도 걸립니다. 기다리는 동안 컴퓨팅 페이지에서 **유추 클러스터** 탭을 선택한 다음, **새로 만들기** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="유추 클러스터를 만드는 방법을 보여 주는 스크린샷":::

결과 **유추 클러스터 만들기** 페이지에서 지역, VM 크기를 차례로 선택하고(이 자습서의 경우 `Standard_D11_v2` 선택), **다음** 을 선택합니다. **구성 설정** 페이지에서 다음을 수행합니다.

1. 올바른 컴퓨팅 이름을 제공합니다.
1. **Dev-test** 를 클러스터 용도로 선택합니다(배포된 모델을 호스팅하는 단일 노드를 만듦).
1. **만들기** 를 선택합니다.

이제 유추 클러스터 **상태** 가 **만드는 중** 임을 확인할 수 있습니다. 단일 노드 클러스터를 배포하는 데 4분 정도 걸립니다.

## <a name="create-a-dataset"></a>데이터 세트 만들기

이 자습서에서는 [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)에서 제공되는 [당뇨병 데이터 세트](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)를 사용합니다.

데이터 세트를 만들려면 왼쪽 메뉴에서 **데이터 세트** 를 선택한 다음, **데이터 세트 만들기** 를 선택합니다. 다음 옵션이 표시됩니다.

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

## <a name="create-a-machine-learning-model-using-designer"></a>디자이너를 사용하여 Machine Learning 모델 만들기

컴퓨팅 및 데이터 세트가 만들어지면 디자이너를 사용하여 기계 학습 모델을 만드는 단계로 이동할 수 있습니다. Azure Machine Learning 스튜디오에서 **디자이너** 를 선택한 다음, **새 파이프라인** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="새 파이프라인을 만드는 방법을 보여 주는 스크린샷":::

**설정 메뉴** 도 볼 수 있는 빈 *캔버스* 가 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="컴퓨팅 대상을 선택하는 방법을 보여 주는 스크린샷":::

**설정 메뉴** 에서 **컴퓨팅 대상** 을 선택하고, 이전에 만든 컴퓨팅 인스턴스를 선택한 다음, **저장** 을 선택합니다. **초안 이름** 을 기억하기 쉬운 이름(예: *diabetes-model*)으로 바꾸고, 설명을 입력합니다.

다음으로, 나열된 자산에서 **데이터 세트** 를 펼치고, **당뇨병** 데이터 세트를 찾습니다. 이 모듈을 캔버스로 끌어서 놓습니다.

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="구성 요소를 끄는 방법을 보여 주는 스크린샷":::

다음으로, 다음 구성 요소를 캔버스로 끌어서 놓습니다.

1. 선형 회귀(**Machine Learning 알고리즘** 에 있음)
1. 모델 학습(**모델 학습** 에 있음)

캔버스는 다음과 같습니다(구성 요소의 위쪽 및 아래쪽에는 아래쪽에 빨간색으로 강조 표시된 '포트'라는 작은 원이 있음).

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="연결되지 않은 구성 요소를 보여 주는 스크린샷":::
 
다음으로, 이러한 구성 요소를 모두 *연결* 해야 합니다. **당뇨병** 데이터 세트의 아래쪽에 있는 포트를 선택하고, **모델 학습** 구성 요소의 위쪽에 있는 오른쪽 포트로 끕니다. **선형 회귀** 구성 요소의 아래쪽에 있는 포트를 선택하고, **모델 학습** 포트의 위쪽에 있는 왼쪽 포트로 끕니다.

데이터 세트에서 예측할 레이블(대상) 변수로 사용할 열을 선택합니다. **모델 학습** 구성 요소를 선택한 다음, **열 편집** 을 선택합니다. 대화 상자에서 **열 이름 입력** 을 선택한 다음, 드롭다운 목록에서 **Y** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="레이블 열 선택의 스크린샷":::

**저장** 을 선택합니다. 기계 학습 *워크플로* 가 다음과 같이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="구성 요소가 연결된 방식을 보여 주는 스크린샷":::

**제출** 을 선택한 다음, 실험 아래에서 **새로 만들기** 를 선택합니다. 실험 이름을 제공한 다음, **제출** 을 선택합니다.

>[!NOTE]
> 첫 번째 실행에서 실험을 완료하는 데 5분 정도 걸립니다. 이후 실행은 훨씬 더 빠릅니다. 디자이너 캐시에서 이미 구성 요소를 실행하여 대기 시간을 줄입니다.

실험이 완료되면 다음과 같이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="완료된 실행을 보여 주는 스크린샷":::

**모델 학습** 을 선택한 다음, **출력 + 로그** 를 선택하여 실험 로그를 검사할 수 있습니다.

## <a name="deploy-the-model"></a>모델 배포

모델을 배포하려면 **유추 파이프라인 만들기**(캔버스 위쪽에 있음)를 선택한 다음, **실시간 유추 파이프라인** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="실시간 유추 파이프라인을 보여 주는 스크린샷":::
 
파이프라인은 모델 채점을 수행하는 데 필요한 구성 요소로만 압축됩니다. 데이터를 채점하는 경우 대상 변수 값을 알 수 없으므로 데이터 세트에서 **Y** 를 제거할 수 있습니다. 제거하려면 **데이터 세트에서 열 선택** 구성 요소를 캔버스에 추가합니다. 당뇨병 데이터 세트가 입력되고 결과가 **모델 채점** 구성 요소에 출력되도록 구성 요소를 연결합니다.

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="열의 제거를 보여 주는 스크린샷":::

캔버스에서 **데이터 세트에서 열 선택** 구성 요소를 선택한 다음, **열 편집** 을 선택합니다. [열 선택] 대화 상자에서 **이름별** 을 선택한 다음, 대상이 **아닌** 모든 입력 변수가 선택되었는지 확인합니다.

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="열 설정의 제거를 보여 주는 스크린샷":::

**저장** 을 선택합니다. 마지막으로, **모델 채점** 구성 요소를 선택하고, **출력에 점수 열 추가** 확인란의 선택을 취소합니다(입력 *및* 예측이 아니라 예측만 다시 보내지므로 대기 시간이 줄어듦).

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="모델 채점 구성 요소 설정을 보여 주는 스크린샷":::

캔버스 위쪽에서 **제출** 을 선택합니다.

유추 파이프라인이 성공적으로 실행되면 모델을 유추 클러스터에 배포할 수 있습니다. **배포** 를 선택합니다. 그러면 **실시간 엔드포인트 설정** 대화 상자가 표시됩니다. **새 실시간 엔드포인트 배포** 를 선택하고, 엔드포인트 이름을 **my-diabetes-model** 로 지정하고, 이전에 만든 유추를 선택하고, **배포** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="실시간 엔드포인트 설정을 보여 주는 스크린샷":::
## <a name="next-steps"></a>다음 단계

이 자습서에서는 디자이너 모델을 학습시키고 배포하는 방법을 살펴보았습니다. 다음 2부에서는 Power BI에서 이 모델을 사용(채점)하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Power BI에서 모델 사용](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
