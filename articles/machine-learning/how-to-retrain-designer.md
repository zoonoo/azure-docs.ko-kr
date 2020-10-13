---
title: Azure Machine Learning 디자이너를 사용 하 여 모델 다시 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning designer에서 게시 된 파이프라인으로 모델을 다시 학습 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: d43bea855d9ac3dc34b8e72adcd9577e5933e52c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905676"
---
# <a name="retrain-models-with-azure-machine-learning-designer"></a>Azure Machine Learning designer를 사용 하 여 모델 다시 학습


이 방법 문서에서는 Azure Machine Learning 디자이너를 사용하여 기계 학습 모델을 재학습시키는 방법에 대해 알아봅니다. 게시된 파이프라인을 사용하여 워크플로를 자동화하고 매개 변수를 설정하여 새 데이터에 대한 모델을 학습시킵니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 기계 학습 모델 학습
> * 파이프라인 매개 변수를 만듭니다.
> * 학습 파이프라인을 게시합니다.
> * 새 매개 변수를 사용하여 모델을 재학습시킵니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역
* 이 방법 시리즈의 1 부를 완료 하 고 [디자이너에서 데이터를 변환 합니다](how-to-designer-transform-data.md) .

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

또한이 문서에서는 사용자가 디자이너에서 파이프라인을 빌드하는 데 대 한 지식이 있다고 가정 합니다. 단계별 소개를 보려면 [자습서](tutorial-designer-automobile-price-train-score.md)를 완료하세요. 

### <a name="sample-pipeline"></a>샘플 파이프라인

이 문서에서 사용 되는 파이프라인은 디자이너 홈페이지에서 샘플 파이프라인 [수입 예측](samples-designer.md#classification) 의 변경 된 버전입니다. 파이프라인에서는 자체 데이터를 사용하여 모델을 학습시키는 방법을 보여 주기 위해 샘플 데이터 세트 대신 [데이터 가져오기](algorithm-module-reference/import-data.md) 모듈을 사용합니다.

![데이터 가져오기 모듈을 강조 표시하는 상자를 포함된 수정된 샘플 파이프라인을 보여 주는 스크린샷](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>파이프라인 매개 변수 만들기

런타임에 동적으로 변수를 설정하는 파이프라인 매개 변수를 만듭니다. 이 예에서는 학습 데이터 경로를 고정 값에서 매개 변수로 변경하여 다른 데이터에 대해 모델을 재학습시킬 것입니다.

1. **데이터 가져오기** 모듈을 선택합니다.

    > [!NOTE]
    > 이 예에서는 데이터 가져오기 모듈을 사용하여 등록된 데이터 저장소의 데이터에 액세스합니다. 그러나 대체 데이터 액세스 패턴을 사용하는 경우 비슷한 단계를 따를 수 있습니다.

1. 캔버스 오른쪽에 있는 모듈 세부 정보 창에서 데이터 원본을 선택합니다.

1. 데이터에 대한 경로를 입력합니다. **경로 찾아보기**를 선택하여 파일 트리를 찾아볼 수도 있습니다. 

1. **경로** 필드를 마우스로 가리키고 표시되는 **경로** 필드 위에 있는 줄임표를 선택합니다.

    ![파이프라인 매개 변수를 만드는 방법을 보여 주는 스크린샷](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. **파이프라인 매개 변수 추가**를 선택합니다.

1. 매개 변수 이름 및 기본값을 제공합니다.

   > [!NOTE]
   > 파이프라인 초안의 제목 옆에 있는 **설정** 기어 아이콘을 선택하여 파이프라인 매개 변수를 검사하고 편집할 수 있습니다. 

1. **저장**을 선택합니다.

1. 파이프라인 실행을 제출합니다.

## <a name="find-a-trained-model"></a>학습된 모델을 찾습니다.

디자이너가 학습된 모델을 비롯한 모든 파이프라인 출력을 기본 작업 영역 스토리지 계정에 저장합니다. 디자이너에서 직접 학습된 모델에 액세스할 수도 있습니다.

1. 파이프라인이 실행을 마칠 때까지 기다립니다.
1. **모델 학습** 모듈을 선택합니다.
1. 캔버스 오른쪽에 있는 모듈 세부 정보 창에서 **출력 + 로그**를 선택합니다.
1. 실행 로그와 함께 **다른 출력**에서 모델을 찾을 수 있습니다.
1. 또는 **출력 보기** 아이콘을 선택합니다. 여기에서 대화 상자의 지침에 따라 데이터 저장소로 직접 이동할 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![학습된 모델을 다운로드하는 방법을 보여 주는 스크린샷](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>학습 파이프라인 게시

파이프라인을 파이프라인 엔드포인트에 게시하여 나중에 파이프라인을 쉽게 다시 사용할 수 있습니다. 파이프라인 엔드포인트는 나중에 파이프라인을 호출하는 REST 엔드포인트를 만듭니다. 이 예에서는 파이프라인 엔드포인트로 파이프라인을 다시 사용하여 다른 데이터에서 모델을 재학습시킬 수 있습니다.

1. 디자이너 캔버스 위의 **게시**를 선택합니다.
1. 파이프라인 엔드포인트를 선택하거나 만듭니다.

   > [!NOTE]
   > 단일 엔드포인트에 여러 파이프라인을 게시할 수 있습니다. 지정된 엔드포인트의 각 파이프라인에는 파이프라인 엔드포인트를 호출할 때 지정할 수 있는 버전 번호가 제공됩니다.

1. **게시**를 선택합니다.

## <a name="retrain-your-model"></a>모델 재학습

이제 게시된 학습 파이프라인이 있으므로 이를 사용하여 새 데이터에 대해 모델을 재학습시킬 수 있습니다. 스튜디오 작업 영역이나 프로그래밍 방식으로 파이프라인 엔드포인트에서 실행을 제출할 수 있습니다.

### <a name="submit-runs-by-using-the-studio-portal"></a>Studio 포털을 사용 하 여 실행 제출

다음 단계를 사용 하 여 studio 포털에서 실행 되는 매개 변수가 있는 파이프라인 끝점을 제출 합니다.

1. 스튜디오 작업 영역에서 **엔드포인트** 페이지로 이동합니다.
1. **파이프라인 엔드포인트** 탭을 선택합니다. 그런 다음 파이프라인 엔드포인트를 선택합니다.
1. **게시된 파이프라인** 탭을 선택합니다. 그런 다음 실행하려는 파이프라인 버전을 선택합니다.
1. **제출**을 선택합니다.
1. 설정 대화 상자에서 실행에 대한 매개 변수 값을 지정할 수 있습니다. 이 예에서는 US가 아닌 데이터 세트를 사용하여 모델을 학습시키도록 데이터 경로를 업데이트합니다.

![디자이너에서 매개 변수가 있는 파이프라인 실행을 설정하는 방법을 보여 주는 스크린샷](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>코드를 사용하여 실행 제출

게시된 파이프라인의 REST 엔드포인트는 개요 패널에서 찾을 수 있습니다. 엔드포인트를 호출하면 게시된 파이프라인을 재학습시킬 수 있습니다.

REST 호출을 수행하려면 OAuth 2.0 전달자 유형 인증 헤더가 필요합니다. 작업 영역에 대한 인증을 설정하고 매개 변수가 있는 REST 호출을 만드는 방법에 대한 자세한 내용은 [일괄 처리 채점용 Azure Machine Learning 파이프라인 빌드](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 디자이너를 사용하여 매개 변수가 있는 학습 파이프라인 엔드포인트를 만드는 방법을 알아보았습니다.

모델을 배포하여 예측을 만드는 방법에 대한 전체 연습은 회귀 모델을 학습시키고 배포하는 [디자이너 자습서](tutorial-designer-automobile-price-train-score.md)를 참조하세요.
