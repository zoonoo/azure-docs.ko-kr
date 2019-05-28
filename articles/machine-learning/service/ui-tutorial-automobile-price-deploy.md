---
title: '자습서: 그래픽 인터페이스를 사용하여 기계 학습 모델 배포'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service 그래픽 인터페이스에서 예측 분석 솔루션을 빌드하는 방법을 알아봅니다. 끌어서 놓기 모듈을 사용하여 기계 학습 모델을 학습, 채점 및 배포합니다. 이 자습서는 선형 회귀 분석을 사용하여 자동차 가격을 예측하는 2부로 구성된 시리즈 중 제2부입니다.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: fe26417c6e3a9f159e884c19d7bd7c9dc7569229
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787826"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>자습서: 그래픽 인터페이스를 사용하여 기계 학습 모델 배포

이 자습서에서는 Azure Machine Learning Service 그래픽 인터페이스에서 예측 솔루션을 개발하는 방법에 대해 자세히 살펴봅니다. 이 자습서는 **2부로 구성된 자습서 시리즈 중 제2부**입니다. [자습서의 제1부](ui-tutorial-automobile-price-train-score.md)에서는 자동차 가격을 예측하는 모델을 학습, 채점 및 평가했습니다. 이 자습서에서 수행하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 배포할 모델 준비
> * 웹 서비스 배포
> * 웹 서비스 테스트
> * 웹 서비스 관리
> * 웹 서비스 사용

## <a name="prerequisites"></a>필수 조건

[이 자습서의 제1부](ui-tutorial-automobile-price-train-score.md)를 완료합니다.

## <a name="prepare-for-deployment"></a>배포 준비

다른 사용자에게 이 자습서에서 개발한 예측 모델을 사용할 기회를 제공하기 위해 해당 모델을 Azure 웹 서비스로 배포할 수 있습니다.

지금까지 모델을 학습시키는 실험을 수행해 왔습니다. 이제는 사용자 입력에 기반한 새 예측을 생성해야 합니다.

배포 준비는 다음 두 단계로 이루어집니다.  

1. 만든 *학습 실험*을 *예측 실험*으로 변환
1. 예측 실험을 웹 서비스로 배포

먼저 실험 캔버스의 아래쪽에서 **다른 이름으로 저장**을 선택하여 실험 복사본을 만들 수 있습니다.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>학습 실험에서 예측 실험으로 변환

이 모델을 배포할 수 있도록 준비하려면 이 학습 실험을 예측 실험으로 변환합니다. 여기에는 일반적으로 다음 세 단계가 포함됩니다.

1. 학습된 모델 저장 및 학습 모듈 바꾸기
1. 학습에만 필요한 모듈을 제거하여 실험 축소
1. 웹 서비스에서 입력 데이터를 수락하는 위치와 출력을 생성하는 위치 정의

이러한 단계는 수동으로 수행하거나 실험 캔버스의 아래쪽에서 **웹 서비스 설정**을 선택하여 자동으로 수행하도록 할 수 있습니다.

![학습 실험을 예측 실험으로 자동 변환하는 방법을 보여 주는 애니메이션 gif](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

**웹 서비스 설정**을 선택하면 다음과 같은 몇 가지 작업이 수행됩니다.

* 학습된 모델이 단일 **학습된 모델** 모듈로 변환됩니다. 실험 캔버스의 왼쪽에 있는 모듈 팔레트에 저장됩니다. 이는 **학습된 모델** 아래에서 찾을 수 있습니다.
* 다음을 비롯하여 학습에 사용된 모듈은 제거됩니다.
  * 모델 학습
  * 데이터 분할
  * 모델 평가
* 저장된 학습된 모델이 실험에 다시 추가됩니다.
* **웹 서비스 입력** 및 **웹 서비스 출력** 모듈이 추가됩니다. 이러한 모듈은 사용자의 데이터가 모델에 입력되는 위치와 데이터가 반환되는 위치를 식별합니다.

실험 캔버스의 위쪽에 있는 새 탭 아래에서 실험이 두 부분으로 저장되어 있음을 알 수 있습니다. 원래 학습 실험은 **학습 실험** 탭 아래에 표시되고, 새로 만든 예측 실험은 **예측 실험** 아래에 표시됩니다. 예측 실험은 웹 서비스로 배포됩니다.

이제 실험은 다음과 같습니다.  

![배포 준비 후 예상되는 실험 구성을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

실험을 마지막으로 한 번 실행합니다(**실행** 선택). 팝업 대화 상자에서 실험을 실행할 컴퓨팅 대상을 선택합니다. 모델이 아직도 작동하고 있는지 확인하려면 모델 점수 매기기 모듈의 출력을 선택하고 **결과 보기**를 선택합니다. 원래 데이터가 예측된 가격과 함께 표시되는 것을 볼 수 있습니다("점수가 매겨진 레이블").

## <a name="deploy-the-web-service"></a>웹 서비스 배포

실험에서 파생된 새 웹 서비스를 배포하려면 다음을 수행합니다.

1. 캔버스 아래에서 **웹 서비스 배포**를 선택합니다.
1. 웹 서비스를 실행하려는 **컴퓨팅 대상**을 선택합니다.

    현재 그래픽 인터페이스는 AKS(Azure Kubernetes Service) 컴퓨팅 대상에 대한 배포만 지원합니다. 기계 학습 서비스 작업 영역에서 사용 가능한 AKS 컴퓨팅 대상을 선택하거나 표시되는 대화 상자의 단계를 사용하여 새 AKS 환경을 구성할 수 있습니다.

    ![새 컴퓨팅 대상에 가능한 구성을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. **웹 서비스 배포**를 선택합니다. 배포가 완료되면 다음과 같은 알림이 표시됩니다. 배포하는 데 몇 분 정도 걸릴 수 있습니다.

    ![성공적인 배포를 위한 확인 메시지가 표시된 스크린샷.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>웹 서비스 테스트

사용자 입력 데이터는 **웹 서비스 입력** 모듈을 통해 배포된 모델에 입력됩니다. 그런 다음, **모델 점수 매기기** 모듈에서 입력에 대한 점수가 매겨집니다. 예측 실험을 설정한 방식으로 모델에는 원래 자동차 가격 데이터 세트와 동일한 형식의 데이터가 필요합니다. 마지막으로, 결과는 **웹 서비스 출력** 모듈을 통해 사용자에게 반환됩니다.

웹 서비스는 그래픽 인터페이스의 웹 서비스 탭에서 테스트할 수 있습니다.

1. 웹 서비스 섹션으로 이동합니다. **자습서 - 자동차 가격 예측[예측 실험]** 이라는 이름으로 배포한 웹 서비스를 볼 수 있습니다.

     ![최근에 만든 웹 서비스가 강조 표시된 웹 서비스 탭을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 웹 서비스 이름을 선택하여 추가 세부 정보를 봅니다.

     ![웹 서비스 보기에서 사용할 수 있는 추가 세부 정보를 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. **테스트**를 선택합니다.

    ![웹 서비스 테스트 페이지를 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. 테스트 데이터를 입력하거나 자동으로 채워진 샘플 데이터를 사용하고, 아래쪽에서 **테스트**를 선택합니다. 테스트 요청이 웹 서비스에 제출되고, 결과가 페이지에 표시됩니다.

## <a name="manage-the-web-service"></a>웹 서비스 관리

웹 서비스가 배포되면 그래픽 인터페이스의 **웹 서비스** 탭에서 관리할 수 있습니다.

웹 서비스는 웹 서비스 세부 정보 페이지에서 **삭제**를 선택하여 삭제할 수 있습니다.

   ![창의 아래쪽에 있는 웹 서비스 삭제 단추의 위치를 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>웹 서비스 사용

이 자습서의 이전 단계에서는 자동차 예측 모델을 Azure 웹 서비스로 배포했습니다. 이제 사용자는 REST API를 통해 데이터를 보내고 결과를 받을 수 있습니다.

**요청/응답** - 사용자가 HTTP 프로토콜을 사용하여 하나 이상의 자동차 데이터 행을 서비스에 보냅니다. 서비스에서 하나 이상의 결과 집합을 사용하여 응답합니다.

REST 호출 샘플은 웹 서비스 세부 정보 페이지의 **사용** 탭에서 찾을 수 있습니다.

   ![사용자가 사용 탭에서 찾을 수 있는 REST 호출 샘플을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

**API 문서** 탭으로 이동하여 자세한 API 세부 정보를 확인합니다.

  ![사용자가 API 문서 탭에서 찾을 수 있는 추가 API 세부 정보를 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Azure Machine Learning Service 작업 영역에서 모델 및 배포 관리

그래픽 인터페이스에서 만든 모델 및 웹 서비스 배포는 Azure Machine Learning Service 작업 영역에서 관리할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 작업 영역을 엽니다.  

1. 작업 영역에서 **모델**을 선택합니다. 그런 다음, 만든 실험을 선택합니다.

    ![Azure Portal에서 실험으로 이동하는 방법을 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    다음 페이지에서 모델에 대한 추가 세부 정보가 표시됩니다.

    ![Azure Portal에서 실험 통계 개요를 보여 주는 스크린샷](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. **배포**를 선택합니다. 그러면 모델을 사용하는 모든 웹 서비스가 나열됩니다. 웹 서비스 이름을 선택합니다. 그러면 웹 서비스 세부 정보 페이지로 이동합니다. 다음 페이지에서 웹 서비스에 대한 자세한 정보를 얻을 수 있습니다.

    ![자세한 실행 보고서의 스크린샷](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 그래픽 인터페이스에서 기계 학습 모델을 만들고, 배포하고, 사용하는 방법에 대한 주요 단계를 알아보았습니다. 그래픽 인터페이스를 사용하여 다른 유형의 문제를 해결하는 방법에 대해 자세히 알아보려면 실험 샘플을 살펴보세요.

> [!div class="nextstepaction"]
> [신용 위험 분류 샘플](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
