---
title: '자습서: 그래픽 인터페이스를 사용하여 자동차 가격 예측'
titleSuffix: Azure Machine Learning service
description: 끌어서 놓기 그래픽 인터페이스를 사용하여 기계 학습 모델을 학습하고 점수를 매기고 배포하는 방법에 대해 알아봅니다. 이 자습서는 선형 회귀 분석을 사용하여 자동차 가격을 예측하는 2부로 구성된 시리즈 중 제1부입니다.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 352f695f186b656ad0f6471d8ede905d53ad5274
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234109"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>자습서: 그래픽 인터페이스를 사용하여 자동차 가격 예측

이 자습서에서는 Azure Machine Learning Service 그래픽 인터페이스에서 예측 솔루션을 개발하는 방법에 대해 자세히 살펴봅니다. 이 자습서를 마치면 구입 희망자가 보내는 기술 사양을 기반으로 자동차의 가격을 예측할 수 있는 솔루션을 갖게 됩니다.

이 자습서는 [빠른 시작에서 계속](ui-quickstart-run-experiment.md)하며 **2부로 구성된 자습서 시리즈 중 제1부**입니다. 그러나 시작하기 전에 빠른 시작을 완료할 필요는 없습니다.

이 자습서의 제1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 데이터 가져오기 및 정리(빠른 시작과 같은 단계)
> * 기계 학습 모델 학습
> * 모델 점수 매기기 및 평가

이 자습서 시리즈의 [제2부](ui-tutorial-automobile-price-deploy.md)에서는 예측 모델을 Azure 웹 서비스로 배포하는 방법을 알아봅니다.

> [!NOTE]
> 이 자습서의 완료된 버전을 샘플 실험으로 사용할 수 있습니다.
> 실험 페이지에서 **새로 추가** > **샘플 1 - 회귀: 자동차 가격 예측(기본)** 으로 이동합니다.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Build-zero-code-machine-learning-models-with-Azure-Machine-Learning-service/player]


## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning Service 작업 영역이 있으면 [다음 섹션](#open-the-visual-interface-webpage)으로 건너뜁니다. 그렇지 않으면 지금 작업 영역을 새로 만듭니다.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>그래픽 인터페이스 웹 페이지 열기

1. [Azure Portal](https://portal.azure.com/)에서 작업 영역을 엽니다.  

1. 작업 영역에서 **그래픽 인터페이스**를 선택합니다.  그런 다음, **그래픽 인터페이스 시작**을 선택합니다.  

    ![Azure Machine Learning Service 작업 영역에서 그래픽 인터페이스에 액세스하는 방법을 보여주는 Azure Portal 스크린샷](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    새 브라우저 페이지에 인터페이스 웹 페이지가 열립니다.  

## <a name="import-and-clean-your-data"></a>데이터 가져오기 및 정리

가장 먼저 해야 하는 작업은 데이터 정리입니다. 빠른 시작을 완료한 경우 여기서 데이터 준비 실험을 재사용할 수 있습니다. 빠른 시작을 완료하지 않은 경우 다음 섹션을 건너뛰고 [새 실험에서 시작](#start-from-a-new-experiment)합니다.

### <a name="reuse-the-quickstart-experiment"></a>빠른 시작 실험 재사용

1. 빠른 시작 실험을 엽니다.

1. 창의 아래쪽에서 **다른 이름으로 저장**을 선택합니다.

1. 표시되는 팝업 대화 상자에서 새 이름을 지정합니다.

    ![실험의 이름을 "자습서 - 자동차 가격 예측"으로 바꾸는 방법을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. 이제 실험이 다음과 같이 표시됩니다.

    ![실험의 예상되는 상태를 보여주는 스크린샷입니다. 자동차 데이터 세트는 열 선택 모듈에 연결되며, 이 모듈은 누락 데이터 정리에 연결됩니다.](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

빠른 시작 실험을 성공적으로 재사용한 경우 다음 섹션을 건너뛰고 [모델 학습](#train-the-model)을 시작합니다.

### <a name="start-from-a-new-experiment"></a>새 실험에서 시작

빠른 시작을 완료하지 않은 경우 다음 단계에 따라 자동차 데이터 세트를 가져오고 정리하는 새 실험을 빨리 만듭니다.

1. 그래픽 인터페이스 창의 아래쪽에서 **+새로 만들기**를 선택하여 새 실험을 만듭니다.

1. **실험** >  **빈 실험**을 차례로 선택합니다.

1. 캔버스 위쪽에서 기본 실험 이름 **"다음에 만들기 실험...**"을 선택하고 이를 의미 있는 이름으로 바꿉니다. 예를 들어 **자동차 가격 예측**으로 바꿉니다. 이름은 고유할 필요가 없습니다.

1. 실험 캔버스 왼쪽에는 데이터 세트와 모듈의 팔레트가 있습니다. 모듈을 찾으려면 모듈 팔레트의 위쪽에 있는 검색 상자를 사용합니다. 검색 상자에 **자동차**를 입력하여 레이블이 **자동차 가격 데이터(원시)** 인 데이터 세트를 찾습니다. 실험 캔버스에 데이터 세트를 끌어 놓습니다.

    ![자동차 가격 데이터 세트를 찾는 방법을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    이제 데이터를 준비했으므로 **normalized-losses** 열을 완전히 제거하는 모듈을 추가할 수 있습니다. 그런 다음, 누락된 데이터가 있는 행을 제거하는 또 다른 모듈을 추가합니다.

1. 검색 창에 **열 선택**을 입력하여 **데이터 세트에서 열 선택** 모듈을 찾습니다. 그런 다음, 실험 캔버스로 끌어서 놓습니다. 이 모듈을 사용하면 모델에서 포함하거나 제외할 데이터 열을 선택할 수 있습니다.

1. **자동차 가격 데이터(원시)** 데이터 세트의 출력 포트를 '데이터 세트에서 열 선택'의 입력 포트에 연결합니다.

    ![자동차 가격 데이터 모듈을 열 선택 모듈에 연결하는 방법을 보여주는 애니메이션 gif](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. 데이터 세트 모듈에서 열 선택을 선택하고 **속성** 창에서 **열 선택기 시작**을 선택합니다.

   1. 왼쪽에서 **규칙으로**를 선택합니다.

   1. **다음으로 시작** 옆에서 **모든 열**을 선택합니다. 이 규칙은 **데이터 세트에서 열 선택**에서 모든 열(제외하려는 열 제외)을 통과하도록 지정합니다.

   1. 드롭다운 메뉴에서 **제외** 및 **열 이름**을 선택한 다음, 텍스트 상자 안에 **normalized-losses**를 입력합니다.

   1. 확인 단추를 선택하여 열 선택기(오른쪽 아래)를 닫습니다.

     이제 **데이터 세트에서 열 선택**의 속성 창은 **nomalized-losses**를 제외한 데이터 세트의 모든 열을 통과한다는 것을 나타냅니다.

1. 모듈을 두 번 클릭하고 "정규화된 손실 제외"를 입력하여 **데이터 세트에서 열 선택** 모듈에 주석을 추가합니다. 그러면 모듈이 실험에서 수행하는 내용을 한눈에 볼 수 있습니다.

    ![열 선택 모듈의 올바른 구성을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. 검색 상자에 **정리**를 입력하여 **누락 데이터 정리** 모듈을 찾습니다. **누락 데이터 정리** 모듈을 실험 캔버스로 끌어 놓고 **데이터 세트에서 열 선택** 모듈과 연결합니다.

1. **속성** 창에서 **정리 모드** 아래 **전체 열 제거**를 선택합니다. 이 옵션은 **누락 데이터 정리**에서 누락된 값이 있는 행을 제거하여 데이터를 정리하도록 지정합니다. 모듈을 두 번 클릭하고 주석 "Remove missing value rows"를 입력합니다.

![누락 데이터 정리 모듈의 올바른 구성을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

## <a name="train-the-model"></a>모델 학습

이제 데이터가 준비되었으므로 예측 모델을 생성할 수 있습니다. 데이터를 사용하여 모델을 학습합니다. 그런 다음, 모델을 테스트하여 가격을 얼마나 근접하게 예측할 수 있는지 확인합니다.

**분류**와 **회귀**는 감독 기계 학습 알고리즘의 두 형식입니다. **분류**는 색(빨강, 파랑 또는 녹색)과 같이 정의된 범주 세트에서 답변을 예측합니다. **회귀**는 숫자를 예측하는 데 사용됩니다.

숫자인 가격을 예측하려고 하므로 회귀 알고리즘을 사용할 수 있습니다. 이 예에서는 선형 회귀 모델을 사용합니다.

가격을 포함하는 데이터 세트를 부여하여 모델을 학습합니다. 이 모델은 데이터를 검색하고 자동차의 기능과 해당 가격 간의 상관 관계를 찾습니다. 그런 다음, 익숙한 자동차의 기능 세트를 부여하여 모델을 테스트하고 해당 모델이 알려진 가격을 얼마나 근접하게 예측하는지 확인합니다.

데이터 세트를 별도로 학습하고 테스트하도록 데이터를 분할하여 모델을 학습하고 테스트하는 데 모두 데이터를 사용합니다.

1. 검색 상자에 **데이터 분할**을 입력하여 **데이터 분할** 모듈을 찾은 후 해당 모듈을 **누락 데이터 정리** 모듈의 왼쪽 포트에 연결합니다.

1. 방금 연결한 **데이터 분할** 모듈을 선택합니다. 속성 창에서 첫 번째 출력 데이터 세트의 행 분수를 0.7로 설정합니다. 그러면 데이터의 70%를 모델 학습에 사용하고 30%는 테스트용으로 보유합니다.

    ![속성 창의 올바른 구성을 보여주는 스크린샷 "데이터 분할"의 값은 "행 분할", 0.7, 임의 분할, 0, False이어야 합니다.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. **데이터 분할**을 두 번 클릭하고 주석 "학습 세트(0.7)와 테스트 세트(0.3)로 데이터 세트 분할"을 입력합니다.

1. 학습 알고리즘을 선택하려면 모듈 팔레트 검색 상자를 지웁니다.

1. **Machine Learning**, **모델 초기화**를 차례로 확장합니다. 기계 학습 알고리즘을 초기화하는 데 사용할 수 있는 몇 가지 범주의 모듈이 표시됩니다.

1. 이 실험의 경우 **회귀** > **선형 회귀**를 선택하고 실험 캔버스로 끌어 놓습니다.

    ![속성 창의 올바른 구성을 보여주는 스크린샷 "데이터 분할"의 값은 "행 분할", 0.7, 임의 분할, 0, False이어야 합니다.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. **모델 학습** 모듈을 찾아서 실험 캔버스로 끌어 놓습니다. 선형 회귀 모듈의 출력을 모델 학습 모듈의 왼쪽 입력에 연결하고 **데이터 분할** 모듈의 학습 데이터 출력(왼쪽 포트)을 **모델 학습** 모듈의 오른쪽 입력에 연결합니다.

    ![모델 학습 모듈의 올바른 구성을 보여주는 스크린샷 선형 회귀 모듈은 모델 학습 모듈의 왼쪽 포트에 연결되며 데이터 분할 모듈은 모델 학습의 오른쪽 포트에 연결됩니다.](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. **모델 학습** 모듈을 선택합니다. 속성 창에서 시작 열 선택기를 선택한 다음, **열 이름 포함** 옆에 **가격**을 입력합니다. 가격은 모델이 예측하려는 값입니다.

    ![열 선택기 모듈의 올바른 구성을 보여주는 스크린샷 규칙으로 > 열 이름 포함 > "가격"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    이제 실험은 다음과 같습니다.
    ![모델 학습 모듈을 추가한 후 실험의 올바른 구성을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>학습 실험 실행

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>모델 점수 매기기 및 평가

지금까지 데이터의 70%를 사용하여 모델을 학습했으며, 이제 모델을 사용하여 남은 데이터 30%의 점수를 매겨서 모델 기능이 얼마나 잘 작동하는지 확인할 수 있습니다.

1. 검색 상자에 **모델 점수 매기기**를 입력하여 **모델 점수 매기기** 모듈을 찾고 해당 모듈을 실험 캔버스로 끌어 놓습니다. **모델 학습** 모듈의 출력을 **모델 점수 매기기**의 왼쪽 입력 포트에 연결합니다. **데이터 분할** 모듈의 테스트 데이터 출력(오른쪽 포트)을 **모델 점수 매기기**의 오른쪽 입력 포트에 연결합니다.

1. 검색 상자에 **평가**를 입력하여 **모델 평가** 모듈을 찾고 해당 모듈을 실험 캔버스로 끌어 놓습니다. **모델 점수 매기기** 모듈의 출력을 **모델 평가**의 왼쪽 입력에 연결합니다. 최종 실험은 다음과 같이 표시됩니다.

    ![실험의 올바른 최종 구성을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. 이전에 사용한 동일한 컴퓨팅 대상을 사용하여 실험을 실행합니다.

1. **모델 점수 매기기**의 출력 포트를 선택하여 **모델 점수 매기기** 모듈의 출력을 보고 **시각화**를 선택합니다. 출력에 테스트 데이터에서 가져온 알려진 값과 함께 가격 예측 값이 표시됩니다.

    !["점수를 매긴 레이블"을 강조 표시한 출력 시각화의 스크린샷](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. 모델 평가 모듈에서 출력을 보려면 출력 포트를 선택한 다음, 시각화를 선택합니다.

    ![최종 실험에 대한 평가 결과를 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

모델에 대한 다음 통계가 표시됩니다.

* **MAE(절대 평균 오차)**: 절대 오차의 평균입니다(오차는 예측 값과 실제 값 사이의 차이임).
* **제곱 평균 오차의 제곱근(RMSE)**: 테스트 데이터 세트에 대해 예측한 제곱 평균 오차의 제곱근입니다.
* **상대 절대 오차**: 실제 값과 모든 실제 값 평균 사이의 절대값 차에 대해 상대적인 절대 평균 오차입니다.
* **상대 제곱 오차**: 실제 값과 모든 실제 값 평균 사이의 제곱 차에 대해 상대적인 제곱 평균 오차입니다.
* **결정 계수**: R 제곱 값이라고도 하며, 모델이 데이터에 얼마나 적합한지 나타내는 통계 메트릭입니다.

각 오차 통계는 작을수록 좋습니다. 값이 작을수록 예측이 실제 값과 더 근접하게 일치함을 나타냅니다. 결정 계수의 경우 값이 1(1.0)에 가까울수록 더 잘 예측한 것입니다.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Azure Machine Learning Service 작업 영역에서 실험 관리

그래픽 인터페이스에서 만든 실험을 Azure Machine Learning Service 작업 영역에서 관리할 수 있습니다. 작업 영역을 사용하여 개별 실험 실행, 진단 로그, 실행 그래프 등과 같은 더 자세한 정보를 확인합니다.

1. [Azure Portal](https://portal.azure.com/)에서 작업 영역을 엽니다.  

1. 작업 영역에서 **실험**을 선택합니다. 그런 다음, 만든 실험을 선택합니다.

    ![Azure Portal에서 실험으로 이동하는 방법을 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    이 페이지에서 실험 및 해당 실험의 최근 실행에 대한 개요를 확인합니다.

    ![Azure Portal에서 실험 통계의 개요를 보여주는 스크린샷](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. 실행 번호를 선택하여 특정 실행에 대한 더 자세한 정보를 확인합니다.

    ![자세한 실행 보고서의 스크린샷](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    실행 보고서는 실시간으로 업데이트됩니다. 실험에서 **Python 스크립트 실행** 모듈을 사용한 경우 **로그** 탭에서 스크립트 로그를 출력에 지정할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서의 제1부에서는 다음 단계를 완료했습니다.

* 빠른 시작에서 만든 실험 재사용
* 데이터 준비
* 모델 학습
* 모델 점수 매기기 및 평가

제2부에서는 모델을 Azure 웹 서비스로 배포하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [모델 배포 계속](ui-tutorial-automobile-price-deploy.md)
