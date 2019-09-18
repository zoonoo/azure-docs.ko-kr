---
title: '자습서: 그래픽 인터페이스를 사용하여 기계 학습 모델 배포'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service 그래픽 인터페이스에서 예측 분석 솔루션을 빌드하는 방법을 알아봅니다. 끌어서 놓기 모듈을 사용하여 기계 학습 모델을 학습, 채점 및 배포합니다. 이 자습서는 선형 회귀 분석을 사용하여 자동차 가격을 예측하는 2부로 구성된 시리즈 중 제2부입니다.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 64062aeab9a807d2aee7f4bca05d4019a3d9b736
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858639"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>자습서: 그래픽 인터페이스를 사용하여 기계 학습 모델 배포

다른 사용자에게 [이 자습서의 1부](ui-tutorial-automobile-price-train-score.md)에서 개발한 예측 모델을 사용할 기회를 제공하기 위해 해당 모델을 Azure 웹 서비스로 배포할 수 있습니다. 지금까지 모델을 학습시키는 실험을 수행해 왔습니다. 이제는 사용자 입력에 기반한 새 예측을 생성해야 합니다. 이 자습서에서 수행하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 배포할 모델 준비
> * 웹 서비스 배포
> * 웹 서비스 테스트
> * 웹 서비스 관리
> * 웹 서비스 사용

## <a name="prerequisites"></a>필수 조건

[이 자습서의 1부](ui-tutorial-automobile-price-train-score.md)를 완료하여 시각적 인터페이스에서 기계 학습 모델을 학습하고 채점하는 방법을 알아보세요.

## <a name="prepare-for-deployment"></a>배포 준비

실험을 웹 서비스로 배포하려면 먼저 *학습 실험*을 *예측 실험*으로 변환해야 합니다.

1. 실험 캔버스의 맨 아래에서 **예측 실험 만들기*** 를 선택합니다.

    ![학습 실험을 예측 실험으로 자동 변환하는 방법을 보여 주는 애니메이션 gif](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    **예측 실험 만들기**를 선택하면 다음과 같은 여러 작업이 수행됩니다.
    
    * 학습된 모델은 모듈 팔레트에 **학습된 모델** 모듈로 저장됩니다. 이는 **학습된 모델** 아래에서 찾을 수 있습니다.
    * 다음을 비롯하여 학습에 사용된 모듈은 제거됩니다.
      * 모델 학습
      * 데이터 분할
      * 모델 평가
    * 저장된 학습된 모델이 실험에 다시 추가됩니다.
    * **웹 서비스 입력** 및 **웹 서비스 출력** 모듈이 추가됩니다. 이러한 모듈은 사용자 데이터가 모델에 입력되는 위치와 데이터가 반환되는 위치를 식별합니다.

    **학습 실험**은 여전히 실험 캔버스의 위쪽에 있는 새 탭 아래에 저장되어 있습니다.

1. **실행** 합니다.

1. **모델 채점** 모듈의 출력을 선택하고 **결과 보기**를 선택하여 모델이 아직도 작동하고 있는지 확인합니다. 원래 데이터가 예측된 가격과 함께 표시되는 것을 볼 수 있습니다("점수가 매겨진 레이블").

이제 실험은 다음과 같습니다.  

![배포 준비 후 예상되는 실험 구성을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>웹 서비스 배포

1. 캔버스 아래에서 **웹 서비스 배포**를 선택합니다.

1. 웹 서비스를 실행하려는 **컴퓨팅 대상**을 선택합니다.

    현재 그래픽 인터페이스는 AKS(Azure Kubernetes Service) 컴퓨팅 대상에 대한 배포만 지원합니다. 기계 학습 서비스 작업 영역에서 사용 가능한 AKS 컴퓨팅 대상을 선택하거나 표시되는 대화 상자의 단계를 사용하여 새 AKS 환경을 구성할 수 있습니다.

    ![새 컴퓨팅 대상에 가능한 구성을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. **웹 서비스 배포**를 선택합니다. 배포가 완료되면 다음과 같은 알림이 표시됩니다. 배포하는 데 몇 분 정도 걸릴 수 있습니다.

    ![성공적인 배포를 위한 확인 메시지가 표시된 스크린샷.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>웹 서비스 테스트

**웹서비스** 탭으로 이동하여 시각적 인터페이스 웹 서비스를 테스트하고 관리할 수 있습니다.

1. 웹 서비스 섹션으로 이동합니다. **자습서 - 자동차 가격 예측[예측 실험]** 이라는 이름으로 배포한 웹 서비스를 볼 수 있습니다.

     ![최근에 만든 웹 서비스가 강조 표시된 웹 서비스 탭을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 웹 서비스 이름을 선택하여 추가 세부 정보를 봅니다.

1. **테스트**를 선택합니다.

    [![웹 서비스 테스트 페이지를 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. 테스트 데이터를 입력하거나 자동으로 채워진 샘플 데이터를 사용하고, **테스트**를 선택합니다.

    테스트 요청이 웹 서비스에 제출되고, 결과가 페이지에 표시됩니다. 입력 데이터에 대한 가격 값이 생성되지만 예측 값을 생성하는 데는 사용되지 않습니다.

## <a name="consume-the-web-service"></a>웹 서비스 사용

이제 사용자가 Azure 웹 서비스에 API 요청을 보내고 결과를 받아 새로운 자동차의 가격을 예측할 수 있습니다.

**요청/응답** - 사용자가 HTTP 프로토콜을 사용하여 하나 이상의 자동차 데이터 행을 서비스에 보냅니다. 서비스에서 하나 이상의 결과 집합을 사용하여 응답합니다.

REST 호출 샘플은 웹 서비스 세부 정보 페이지의 **사용** 탭에서 찾을 수 있습니다.

   ![사용자가 사용 탭에서 찾을 수 있는 REST 호출 샘플을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

**API 문서** 탭으로 이동하여 자세한 API 세부 정보를 확인합니다.

## <a name="manage-models-and-deployments"></a>모델 및 배포 관리

그래픽 인터페이스에서 만든 모델 및 웹 서비스 배포는 Azure Machine Learning Service 작업 영역에서도 관리할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 작업 영역을 엽니다.  

1. 작업 영역에서 **모델**을 선택합니다. 그런 다음, 만든 실험을 선택합니다.

    ![Azure Portal에서 실험으로 이동하는 방법을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    다음 페이지에서 모델에 대한 추가 세부 정보가 표시됩니다.

1. **배포**를 선택합니다. 그러면 모델을 사용하는 모든 웹 서비스가 나열됩니다. 웹 서비스 이름을 선택합니다. 그러면 웹 서비스 세부 정보 페이지로 이동합니다. 다음 페이지에서 웹 서비스에 대한 자세한 정보를 얻을 수 있습니다.

    [![자세한 실행 보고서의 스크린샷](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

[작업 영역 방문 페이지(미리 보기)](https://ml.azure.com)의 **모델** 및 **엔드포인트** 섹션에서 이러한 모델 및 배포를 찾을 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 그래픽 인터페이스에서 기계 학습 모델을 만들고, 배포하고, 사용하는 방법에 대한 주요 단계를 알아보았습니다. 그래픽 인터페이스를 사용하여 다른 유형의 문제를 해결하는 방법에 대해 자세히 알아보려면 다른 실험 샘플을 살펴보세요.

> [!div class="nextstepaction"]
> [신용 위험 분류 샘플](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
