---
title: Azure Machine Learning 디자이너를 사용하여 일괄 처리 예측 실행(미리 보기)
titleSuffix: Azure Machine Learning
description: 디자이너를 사용하여 모델을 학습하고 일괄 처리 예측 파이프라인을 설정하는 방법에 대해 알아봅니다. 파이프라인을 매개 변수가 있는 웹 서비스로 배포합니다. 그러면 HTTP 라이브러리에서 트리거될 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477224"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Azure Machine Learning 디자이너를 사용하여 일괄 처리 예측 실행(미리 보기)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 문서에서는 디자이너를 사용하여 일괄 처리 예측 파이프라인을 만드는 방법을 배웁니다. 일괄 처리 예측을 사용하면 모든 HTTP 라이브러리에서 트리거할 수 있는 웹 서비스를 사용하여 주문형 대규모 데이터 집합에 지속적으로 점수를 매점할 수 있습니다.

이 방법에서는 다음 작업을 수행하는 방법을 배웁니다.

> [!div class="checklist"]
> * 일괄 처리 추론 파이프라인 만들기 및 게시
> * 파이프라인 끝점 사용
> * 엔드포인트 버전 관리

SDK를 사용하여 일괄 처리 점수 매기기 서비스를 설정하는 방법에 대해 알아보려면 함께 제공되는 [방법을](how-to-run-batch-predictions.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

이 방법은 이미 교육 파이프라인이 있다고 가정합니다. 디자이너에 대한 가이드 소개를 들어, [디자이너 자습서 의 1 부분을](tutorial-designer-automobile-price-train-score.md)완료 . 

## <a name="create-a-batch-inference-pipeline"></a>일괄 처리 유추 파이프라인 만들기

추론 파이프라인을 만들려면 교육 파이프라인을 한 번 이상 실행해야 합니다.

1. 작업 영역의 **디자이너** 탭으로 이동합니다.

1. 예측을 만드는 데 사용할 모델을 학습하는 학습 파이프라인을 선택합니다.

1. 파이프라인을 **제출합니다.**

    ![파이프라인 제출](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

이제 학습 파이프라인이 실행되었으므로 일괄 처리 추론 파이프라인을 만들 수 있습니다.

1. **제출**다음으로 새 드롭다운 을 **선택하여 추론 파이프라인**만들기.

1. **일괄 처리 추론 파이프라인을**선택합니다.

    ![일괄 처리 유추 파이프라인 만들기](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
그러면 기본 일괄 처리 유추 파이프라인이 생성됩니다. 

### <a name="add-a-pipeline-parameter"></a>파이프라인 매개 변수 추가

새 데이터에 대한 예측을 만들려면 이 파이프라인 초안 보기에서 다른 데이터 집합을 수동으로 연결하거나 데이터 집합에 대한 매개 변수를 만들 수 있습니다. 매개 변수를 사용하면 런타임시 일괄 처리 추론 프로세스의 동작을 변경할 수 있습니다.

이 섹션에서는 데이터 집합 매개 변수를 만들어 다른 데이터 집합을 지정하여 예측을 수행할 수 있습니다.

1. 데이터 집합 모듈을 선택합니다.

1. 창이 캔버스의 오른쪽에 나타납니다. 창 아래쪽에서 **파이프라인 매개변수로 설정을**선택합니다.
   
    매개 변수에 대한 이름을 입력하거나 기본값을 수락합니다.

## <a name="publish-your-batch-inferencing-pipeline"></a>일괄 처리 추론 파이프라인 게시

이제 추론 파이프라인을 배포할 준비가 되었습니다. 이렇게 하면 파이프라인이 배포되고 다른 사용자가 사용할 수 있습니다.

1. **게시** 단추를 선택합니다.

1. 표시되는 대화 상자에서 **파이프라인Endpoint에**대한 드롭다운을 확장하고 **새 파이프라인 Endpoint를**선택합니다.

1. 끝점 이름과 선택적 설명을 제공합니다.

    대화 상자 의 맨 아래에 있으면 학습 중에 사용되는 데이터 집합 ID의 기본값으로 구성한 매개 변수를 볼 수 있습니다.

1. **게시**를 선택합니다.

![파이프라인 게시](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>끝점 사용

이제 데이터 집합 매개 변수를 사용하여 게시된 파이프라인이 있습니다. 파이프라인은 학습 파이프라인에서 생성된 모델을 사용하여 매개 변수로 제공하는 데이터 집합의 점수를 매겨집니다.

### <a name="submit-a-pipeline-run"></a>파이프라인 실행 제출 

이 섹션에서는 수동 파이프라인 실행을 설정하고 파이프라인 매개 변수를 변경하여 새 데이터의 점수를 매러립니다. 

1. 배포가 완료되면 **끝점** 섹션으로 이동합니다.

1. **파이프라인 끝점을**선택합니다.

1. 만든 끝점의 이름을 선택합니다.

![엔드포인트 링크](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. **게시된 파이프라인을 선택합니다.**

    이 화면에는 이 끝점에 게시된 모든 게시된 파이프라인이 표시됩니다.

1. 게시한 파이프라인을 선택합니다.

    파이프라인 세부 정보 페이지에는 파이프라인에 대한 자세한 실행 기록 및 연결 문자열 정보가 표시됩니다. 
    
1. 파이프라인의 수동 실행을 만들려면 **제출을** 선택합니다.

    ![파이프라인 세부 정보](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. 매개 변수를 변경하여 다른 데이터 집합을 사용합니다.
    
1. 파이프라인을 실행하려면 **제출을** 선택합니다.

### <a name="use-the-rest-endpoint"></a>REST 끝점 사용

**끝점** 섹션에서 파이프라인 끝점 및 게시된 파이프라인을 사용하는 방법에 대한 정보를 찾을 수 있습니다.

실행 개요 패널에서 파이프라인 끝점의 REST 끝점을 찾을 수 있습니다. 끝점을 호출하면 기본 게시된 파이프라인을 사용하게 됩니다.

게시된 파이프라인 페이지에서 게시된 파이프라인을 사용할 수도 **있습니다.** 게시된 파이프라인을 선택하고 나머지 끝점을 찾습니다. 

![나머지 끝점 세부 정보](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

REST 호출을 하려면 OAuth 2.0 베어러 유형 인증 헤더가 필요합니다. 작업 영역에 대한 인증을 설정하고 매개 변수가 있는 REST 호출을 수행하는 방법에 대한 자세한 내용은 다음 [자습서 섹션](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)을 참조하세요.

## <a name="versioning-endpoints"></a>엔드포인트 버전 조정

디자이너는 끝점에 게시하는 각 후속 파이프라인에 버전을 할당합니다. REST 호출에서 매개 변수로 실행할 파이프라인 버전을 지정할 수 있습니다. 버전 번호를 지정하지 않으면 디자이너가 기본 파이프라인을 사용합니다.

파이프라인을 게시할 때 해당 끝점에 대한 새 기본 파이프라인으로 만들도록 선택할 수 있습니다.

![기본 파이프라인 설정](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

끝점의 게시된 파이프라인 탭에서 새 기본 **파이프라인을** 설정할 수도 있습니다.

![기본 파이프라인 설정](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>다음 단계

디자이너 [자습서를](tutorial-designer-automobile-price-train-score.md) 따라 회귀 모델을 학습하고 배포합니다.
''