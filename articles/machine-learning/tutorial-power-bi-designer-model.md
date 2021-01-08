---
title: '자습서: 끌어서 놓기를 사용하여 예측 모델 만들기(2-1부)'
titleSuffix: Azure Machine Learning
description: 디자이너를 사용하여 기계 학습 예측 모델을 빌드하고 배포하는 방법을 알아봅니다. 나중에 이를 사용하여 Microsoft Power BI에서 결과를 예측할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814791"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>자습서: Power BI 통합 - 끌어서 놓기를 사용하여 예측 모델 만들기(2-1부)

이 자습서의 1부에서는 Azure Machine Learning 디자이너를 사용하여 기계 학습 예측 모델을 학습하고 배포합니다. 디자이너는 로우 코드 끌어서 놓기 사용자 인터페이스입니다. 2부에서는 Microsoft Power BI에서 모델을 사용하여 결과를 예측합니다.

이 자습서에서는 다음과 같은 작업을 수행했습니다.

> [!div class="checklist"]
> * Azure Machine Learning 컴퓨팅 인스턴스를 만듭니다.
> * Azure Machine Learning 유추 클러스터를 만듭니다.
> * 데이터 세트를 만듭니다.
> * 회귀 모델을 학습합니다.
> * 실시간 채점 엔드포인트에 모델을 배포합니다.


Power BI에서 사용할 모델을 만들고 배포하는 세 가지 방법이 있습니다.  이 문서에서는 "옵션 B: 디자이너를 사용하여 모델 학습 및 배포"에 대해 설명합니다.  이 옵션은 디자이너 인터페이스를 사용하는 로우 코드 작성 경험입니다.  

그러나 대신 다른 옵션 중 하나를 사용할 수 있습니다.

* [옵션 A: Jupyter Notebook을 사용하여 모델을 학습하고 배포합니다](tutorial-power-bi-custom-model.md). 이는 Azure Machine Learning 스튜디오에서 호스팅되는 Jupyter Notebook을 사용하는 코드 우선 제작 환경을 사용합니다.
* [옵션 C: 자동화된 기계 학습을 사용하여 모델을 학습하고 배포합니다](tutorial-power-bi-automated-model.md). 이는 데이터 준비 및 모델 학습을 완전히 자동화하는 코드 없음 제작 환경입니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 구독이 아직 없는 경우 [평가판](https://aka.ms/AMLFree)을 사용할 수 있습니다. 
- Azure Machine Learning 작업 영역 작업 영역이 아직 없는 경우 [Azure Machine Learning 작업 영역 만들기 및 관리](./how-to-manage-workspace.md#create-a-workspace)를 참조하세요.
- 기계 학습 워크플로에 대한 기초 지식


## <a name="create-compute-to-train-and-score"></a>학습하고 채점하는 컴퓨팅 만들기

이 섹션에서는 *컴퓨팅 인스턴스* 를 만듭니다. 컴퓨팅 인스턴스는 기계 학습 모델을 교육하는 데 사용됩니다. 또한 실시간 채점을 위해 배포된 모델을 호스팅할 *유추 클러스터* 를 만듭니다.

[Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다. 왼쪽 메뉴에서 **컴퓨팅** 을 선택하고 **새로 만들기** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="컴퓨팅 인스턴스를 만드는 방법을 보여주는 스크린샷":::

**컴퓨팅 인스턴스 만들기** 페이지에서 VM 크기를 선택합니다. 이 자습서에서는 **Standard_D11_v2** VM을 선택합니다. 그런 후 **다음** 을 선택합니다. 

**설정** 페이지에서 컴퓨팅 인스턴스의 이름을 설정합니다. 그런 다음 **만들기** 를 선택합니다. 

>[!TIP]
> 또한 컴퓨팅 인스턴스를 사용하여 Notebook을 만들고 실행할 수도 있습니다.

이제 컴퓨팅 인스턴스 **상태** 는 **생성 중** 입니다. 머신을 프로비저닝하는 데 약 4분이 소요됩니다. 

대기하는 동안 **컴퓨팅** 페이지에서 **유추 클러스터** 탭을 선택합니다. 그런 다음, **새로 만들기** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="유추 클러스터를 만드는 방법을 보여 주는 스크린샷.":::

**유추 클러스터 만들기** 페이지에서 지역 및 VM 크기를 선택합니다. 이 자습서에서는 **Standard_D11_v2** VM을 선택합니다. 그런 후 **다음** 을 선택합니다. 

**구성 설정** 페이지에서 다음을 수행합니다.

1. 올바른 컴퓨팅 이름을 제공합니다.
1. 클러스터 용도로 **Dev-test** 를 선택합니다. 이 옵션은 배포된 모델을 호스팅하는 단일 노드를 만듭니다.
1. **만들기** 를 선택합니다.

이제 유추 클러스터 **상태** 는 **생성 중** 입니다. 단일 노드 클러스터를 배포하는 데 약 4분이 소요됩니다.

## <a name="create-a-dataset"></a>데이터 세트 만들기

이 자습서에서는 [당뇨병 데이터 세트](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)를 사용합니다. 이 데이터 세트는 [Azure Open 데이터 세트](https://azure.microsoft.com/services/open-datasets/)에서 사용할 수 있습니다.

데이터 세트를 만들려면 왼쪽의 메뉴에서 **데이터 세트** 를 선택합니다. 그런 다음, **데이터 세트 만들기** 를 선택합니다. 다음 옵션이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="데이터 세트를 만드는 방법을 보여 주는 스크린샷.":::

**공개 데이터 세트에서** 를 선택합니다. **공개 데이터 세트에서 데이터 세트 만들기** 페이지에서

1. 검색 창을 사용하여 *당뇨병* 을 찾습니다.
1. **샘플: 당뇨병** 을 선택합니다.
1. **다음** 을 선택합니다.
1. 데이터 세트의 이름을 *당뇨병* 으로 지정합니다.
1. **만들기** 를 선택합니다.

데이터를 탐색하려면 데이터 세트를 선택하고 **탐색** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="데이터 세트를 검색하는 방법을 보여 주는 스크린샷.":::

데이터에는 연령, 성별, 체질량 지수, 평균 혈압 및 6개 혈청 측정값 등 10개의 기준 입력 변수가 있습니다. 또한 **Y** 라는 하나의 대상 변수가 있습니다. 이 대상 변수는 기준 1년 후 당뇨병의 진행 과정을 정량적으로 측정한 것입니다.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>디자이너를 사용하여 기계 학습 모델 만들기

컴퓨팅 및 데이터 세트를 생성한 후 디자이너를 사용하여 기계 학습 모델을 만들 수 있습니다. Azure Machine Learning 스튜디오에서 **디자이너** 를 선택한 다음, **새 파이프라인** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="새 파이프라인을 만드는 방법을 보여 주는 스크린샷.":::

빈 *캔버스* 와 **설정** 메뉴가 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="컴퓨팅 대상을 선택하는 방법을 보여주는 스크린샷.":::

**설정** 메뉴에서 **컴퓨팅 대상 선택** 을 선택합니다. 앞에서 만든 컴퓨팅 인스턴스를 선택한 다음, **저장** 을 선택합니다. **임시 이름** 을 *당뇨병 모델* 과 같이 기억하기 쉬운 것으로 변경합니다. 마지막으로 설명을 입력합니다.

자산 목록에서 **데이터 세트** 를 확장하고 **당뇨병** 데이터 세트를 찾습니다. 이 구성 요소를 캔버스로 끌어 옵니다.

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="구성 요소를 캔버스로 끌어 오는 방법을 보여주는 스크린샷.":::

다음으로, 다음 구성 요소를 캔버스로 끌어 옵니다.

1. **선형 회귀**(**Machine Learning 알고리즘** 에 있음)
1. **모델 학습**(**모델 학습** 에 있음)

캔버스에서 구성 요소의 상단과 하단에 있는 원을 확인합니다. 이러한 원은 포트입니다.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="연결되지 않은 구성 요소의 포트를 보여주는 스크린샷.":::
 
이제 구성 요소를 서로 *연결* 합니다. **당뇨병** 데이터 세트의 맨 아래에 있는 포트를 선택합니다. **학습 모델** 구성 요소의 오른쪽 위에 있는 포트로 끌어 옵니다. **선형 회귀 분석** 구성 요소의 맨 아래에 있는 포트를 선택합니다. **학습 모델** 구성 요소의 왼쪽 위에 있는 포트로 끌어 옵니다.

데이터 세트 열을 선택하여 예측할 레이블(대상) 변수로 사용합니다. **모델 학습** 구성 요소를 선택한 다음, **열 편집** 을 선택합니다. 

대화 상자에서 **열 이름 입력** > **Y** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="레이블 열을 선택하는 방법을 보여주는 스크린샷.":::

**저장** 을 선택합니다. 기계 학습 *워크플로* 는 이와 같이 표시됩니다.

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="연결된 구성 요소를 보여주는 스크린샷.":::

**제출** 을 선택합니다. **실험** 아래에서 **새로 만들기** 를 선택합니다. 실험의 이름을 지정한 다음, **제출** 을 선택합니다.

>[!NOTE]
> 실험의 첫 실행은 약 5분 정도 걸립니다. 디자이너는 지연 시간을 줄이기 위해 실행된 구성 요소를 캐시하기 때문에 후속 실행이 훨씬 더 빠릅니다.

실험이 완료되면 이 보기가 나타납니다.

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="완료된 실행을 보여주는 스크린샷.":::

실험 로그를 검사하려면 **모델 학습** 을 선택한 다음, **출력 + 로그** 를 선택합니다.

## <a name="deploy-the-model"></a>모델 배포

모델을 배포하려면 캔버스의 위쪽에서 **유추 파이프라인 만들기** > **실시간 유추 파이프라인** 을 선택합니다.

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="실시간 유추 파이프라인을 선택하는 위치를 보여주는 스크린샷.":::
 
파이프라인은 모델을 채점하는 데 필요한 구성 요소로만 압축됩니다. 데이터를 채점할 때 대상 변수 값을 알 수 없습니다. 따라서 데이터 세트에서 **Y** 를 제거할 수 있습니다. 

**Y** 를 제거하려면 **데이터 세트에서 열 선택** 구성 요소를 캔버스에 추가합니다. 당뇨병 데이터 세트가 입력이 되도록 구성 요소를 연결합니다. 결과는 **점수 모델** 구성 요소에 출력됩니다.

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="열을 제거하는 방법을 보여주는 스크린샷.":::

캔버스에서 **데이터 세트에서 열 선택** 구성 요소를 선택한 다음, **열 편집** 을 선택합니다. 

**열 선택** 대화 상자에서 **이름 기준** 을 선택합니다. 그런 다음, 모든 입력 변수가 선택되었지만 대상이 선택되지 *않았는지* 확인합니다.

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="열 설정을 제거하는 방법을 보여주는 스크린샷.":::

**저장** 을 선택합니다. 

마지막으로 **점수 모델** 구성 요소를 선택하고 **출력에 점수 열 추가** 확인란의 선택을 취소했는지 확인합니다. 대기 시간을 줄이기 위해 예측은 입력 없이 다시 전송됩니다.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="점수 모델 구성 요소에 대한 설정을 보여주는 스크린샷.":::

캔버스 위쪽에서 **제출** 을 선택합니다.

유추 파이프라인이 성공적으로 실행되면 모델을 유추 클러스터에 배포할 수 있습니다. **배포** 를 선택합니다. 

**실시간 엔드포인트 설정** 대화 상자에서 **새 실시간 엔드포인트 배포** 를 선택합니다. *my-diabetes-model* 엔드포인트의 이름을 지정합니다. 앞에서 만든 유추를 선택한 다음, **배포** 를 선택합니다.

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="실시간 엔드포인트 설정을 보여주는 스크린샷.":::
## <a name="next-steps"></a>다음 단계

이 자습서에서는 디자이너 모델을 학습시키고 배포하는 방법을 살펴보았습니다. 다음 2부에서는 Power BI에서 이 모델을 사용(채점)하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Power BI에서 모델 사용](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
