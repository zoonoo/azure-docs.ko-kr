---
title: '자습서: 그래픽 인터페이스를 사용하여 기계 학습 모델 배포'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 시각적 인터페이스에서 예측 분석 솔루션을 빌드하는 방법을 알아봅니다. 끌어서 놓기 모듈을 사용하여 기계 학습 모델을 학습, 채점 및 배포합니다.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 6f8717f70a2cb03a7fd683cfe61f1198461f4305
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792665"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>자습서: 그래픽 인터페이스를 사용하여 기계 학습 모델 배포

다른 사용자에게 [이 자습서의 1부](ui-tutorial-automobile-price-train-score.md)에서 개발한 예측 모델을 사용할 기회를 주려면 해당 모델을 실시간 엔드포인트로 배포하면 됩니다. 1부에서는 모델을 학습시켰습니다. 이제는 사용자 입력에 기반한 새 예측을 생성해야 합니다. 이 자습서에서 수행하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 실시간 엔드포인트 배포
> * 유추 클러스터 만들기
> * 실시간 엔드포인트 테스트

## <a name="prerequisites"></a>필수 조건

[이 자습서의 1부](ui-tutorial-automobile-price-train-score.md)를 완료하여 시각적 인터페이스에서 기계 학습 모델을 학습하고 채점하는 방법을 알아보세요.

## <a name="deploy-a-real-time-endpoint"></a>실시간 엔드포인트 배포

파이프라인을 배포하려면 다음을 수행해야 합니다.

1. 학습 파이프라인을 실시간 유추 파이프라인으로 변환합니다. 그러면 실시간 유추 파이프라인이 학습 모듈을 제거하고 유추 요청에 대한 입력 및 출력을 추가합니다.
1. 유추 파이프라인을 배포합니다.

### <a name="create-a-real-time-inference-pipeline"></a>실시간 유추 파이프라인 만들기

1. 파이프라인 캔버스의 위쪽에서 **유추 파이프라인 만들기** > **실시간 유추 파이프라인**을 선택합니다.

    **유추 파이프라인 만들기**를 선택하면 다음과 같은 여러 작업이 수행됩니다.
    
    * 학습된 모델은 모듈 팔레트에 **데이터 세트** 모듈로 저장됩니다. **내 데이터 세트** 아래에서 찾을 수 있습니다.
    * 학습에 사용된 **모델 학습** 및 **데이터 분할** 같은 모듈은 제거됩니다.
    * 저장된 학습된 모델이 파이프라인에 다시 추가됩니다.
    * **웹 서비스 입력** 및 **웹 서비스 출력** 모듈이 추가됩니다. 이러한 모듈은 사용자 데이터가 모델에 입력되는 위치와 데이터가 반환되는 위치를 식별합니다.

    > [!Note]
    > **학습 파이프라인**은 파이프라인 캔버스 위쪽의 새로 만들기 탭에 저장됩니다. 시각적 인터페이스에서 게시된 파이프라인으로 찾을 수도 있습니다.
    >

    이제 파이프라인이 다음과 비슷할 것입니다.  

   ![배포 준비 후 예상되는 파이프라인 구성을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. **실행**을 선택하고 1부에서 사용한 것과 동일한 컴퓨팅 대상 및 실험을 사용합니다.

1. **모델 점수 매기기** 모듈을 선택합니다.

1. 속성 창에서 **출력** > **시각화**를 선택하여 모델이 계속 작동하는지 확인합니다. 원래 데이터가 예상 가격("점수가 매겨진 레이블")과 함께 표시되는 것을 볼 수 있습니다.

1. **배포**를 선택합니다.

### <a name="create-an-inferencing-cluster"></a>유추 클러스터 만들기

표시되는 대화 상자에서, 작업 영역의 기존 AKS(Azure Kubernetes Service) 클러스터를 선택하여 모델을 배포할 수 있습니다. AKS 클러스터가 없는 경우 다음 단계에 따라 클러스터를 만듭니다.

1. 대화 상자에서 **컴퓨팅**을 선택하여 **컴퓨팅** 페이지로 이동합니다.

1. 탐색 리본에서 **유추 클러스터** >  **+ 새로 만들기**를 선택합니다.

    ![새 유추 클러스터 창으로 이동하는 방법을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. 유추 클러스터 창에서 새 Kubernetes 서비스를 구성합니다.

1. **컴퓨팅 이름**으로 "aks-compute"를 입력합니다.
    
1. 사용 가능한 가까운 **Azure 지역**을 선택합니다.

1. **만들기**를 선택합니다.

    > [!Note]
    > 새 AKS 클러스터를 만드는 데 약 15분이 걸립니다. **유추 클러스터** 페이지에서 프로비저닝 상태를 확인할 수 있습니다.
    >

### <a name="deploy-the-real-time-endpoint"></a>실시간 엔드포인트 배포

AKS 서비스 프로비저닝이 완료된 후에는 실시간 유추 파이프라인으로 돌아가서 배포를 완료합니다.

1. 캔버스 위에서 **배포**를 선택합니다.

1. **새 실시간 엔드포인트 배포**를 선택합니다. 

1. 만든 AKS 클러스터를 선택합니다.

1. **배포**를 선택합니다.

    ![새 실시간 엔드포인트를 설정하는 방법을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    배포가 완료될 때까지 몇 분 정도 걸릴 수 있으며, 배포가 완료되면 캔버스 위에 성공 알림이 표시됩니다.

## <a name="test-the-real-time-endpoint"></a>실시간 엔드포인트 테스트

왼쪽의 작업 영역 탐색 창에서 **엔드포인트** 페이지로 이동하여 실시간 엔드포인트를 테스트할 수 있습니다.

1. **엔드포인트** 페이지에서 배포한 엔드포인트를 선택합니다.

    ![최근에 만든 엔드포인트가 강조 표시된 실시간 엔드포인트를 보여주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. **테스트**를 선택합니다.

1. 테스트 데이터를 입력하거나 자동으로 채워진 샘플 데이터를 사용하고, **테스트**를 선택합니다.

    테스트 요청이 엔드포인트에 제출되고 결과가 페이지에 표시됩니다. 입력 데이터에 대한 가격 값이 생성되지만 예측 값을 생성하는 데는 사용되지 않습니다.

    ![강조 표시된 가격에 대한 채점 레이블을 사용하여 실시간 엔드포인트를 테스트하는 방법을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 그래픽 인터페이스에서 기계 학습 모델을 만들고, 배포하고, 사용하는 방법에 대한 주요 단계를 알아보았습니다. 시각적 인터페이스를 사용하여 다른 유형의 문제를 해결하는 방법에 대해 자세히 알아보려면 다른 샘플 파이프라인을 살펴보세요.

> [!div class="nextstepaction"]
> [신용 위험 분류 샘플](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
