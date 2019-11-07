---
title: Azure Machine Learning 디자이너를 사용하여 일괄 처리 예측 실행(미리 보기)
titleSuffix: Azure Machine Learning
description: 디자이너를 사용하여 모델을 학습하고 일괄 처리 예측 파이프라인을 설정하는 방법에 대해 알아봅니다. 파이프라인을 매개 변수가 있는 웹 서비스로 배포합니다. 그러면 HTTP 라이브러리에서 트리거될 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: c496e57ea5f4b5b7fcda4f9c43c511488ef3c246
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509437"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너를 사용하여 일괄 처리 예측 실행
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 방법에서는 디자이너를 사용하여 모델을 학습하고 일괄 처리 예측 파이프라인 및 웹 서비스를 설정하는 방법을 알아봅니다. 일괄 처리 예측을 사용하면 선택적으로 HTTP 라이브러리에서 트리거할 수 있는 웹 서비스로 구성되는, 대규모 데이터 세트에서 학습된 모델에 대한 지속적인 주문형 채점이 가능합니다. 

SDK를 사용하여 일괄 처리 채점 서비스를 설정하려면 함께 제공되는 [방법](how-to-run-batch-predictions.md)을 참조하세요.

이 방법에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * 파이프라인에서 기본 ML 실험 만들기
> * 매개 변수가 있는 일괄 처리 유추 파이프라인 만들기
> * 수동으로 또는 REST 엔드포인트에서 파이프라인 관리 및 실행

## <a name="prerequisites"></a>필수 조건

1. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

1. [작업 영역](tutorial-1st-experiment-sdk-setup.md)을 만듭니다.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.

이 방법에서는 디자이너에서 간단한 파이프라인을 구축하는 기본 지식이 있다고 가정합니다. 디자이너에 대한 단계별 소개를 보려면 [자습서](tutorial-designer-automobile-price-train-score.md)를 완료하세요. 

## <a name="create-a-pipeline"></a>파이프라인을 만들기

일괄 처리 유추 파이프라인을 만들려면 먼저 Machine Learning 실험이 필요합니다. 만들려면 작업 영역에서 **디자이너** 탭으로 이동하고 **사용하기 쉬운 미리 작성된 모듈** 옵션을 선택하여 새 파이프라인을 만듭니다.

![디자이너 홈](media/how-to-run-batch-predictions-ui/ui-batch-scoring-1.png)

다음은 데모용 간단한 Machine Learning 모델입니다. 데이터는 Azure Open Datasets 당뇨병 데이터에서 만든 등록된 데이터 세트입니다. Azure Open Datasets에서 데이터 세트를 등록하는 [방법 섹션](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)을 참조하세요. 데이터는 학습 및 유효성 검사 세트로 분할되며, 향상된 의사 결정 트리가 학습되고 채점됩니다. 유추 파이프라인을 만들려면 파이프라인을 한 번 이상 실행해야 합니다. **실행** 단추를 클릭하여 파이프라인을 실행합니다.

![간단한 실험 만들기](media/how-to-run-batch-predictions-ui/ui-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>일괄 처리 유추 파이프라인 만들기

파이프라인이 실행되었으면, **실행** 및 **게시** 옆에 **유추 파이프라인 만들기**라는 새로운 옵션이 있습니다. 드롭다운을 클릭하고 **일괄 처리 유추 파이프라인**을 선택합니다.

![일괄 처리 유추 파이프라인 만들기](media/how-to-run-batch-predictions-ui/ui-batch-scoring-5.png)

그러면 기본 일괄 처리 유추 파이프라인이 생성됩니다. 여기에는 원본 파이프라인 실험 설정을 위한 노드, 채점을 위한 원시 데이터 노드 및 원본 파이프라인에 대한 원시 데이터를 채점하는 노드가 포함됩니다.

![기본 일괄 처리 유추 파이프라인](media/how-to-run-batch-predictions-ui/ui-batch-scoring-6.png)

일괄 처리 유추 프로세스의 동작을 변경하기 위해 다른 노드를 추가할 수 있습니다. 이 예제에서는 채점하기 전에 입력 데이터에서 무작위로 샘플링할 노드를 추가합니다. **파티션 및 샘플** 노드를 만들어서 원시 데이터와 채점 노드 사이에 놓습니다. 그런 다음, **파티션 및 샘플** 노드를 클릭하여 설정 및 매개 변수에 대한 액세스 권한을 얻습니다.

![새 노드](media/how-to-run-batch-predictions-ui/ui-batch-scoring-7.png)

*샘플링 비율* 매개 변수는 무작위 샘플링을 수행할 원본 데이터 세트의 백분율을 제어합니다. 자주 조정하는 데 유용한 매개 변수이므로 파이프라인 매개 변수로 사용하도록 설정합니다. 파이프라인 매개 변수는 런타임 시 변경될 수 있고, REST 엔드포인트에서 파이프라인을 다시 실행하면 페이로드 개체에 지정할 수 있습니다. 

이 필드를 파이프라인 매개 변수로 사용하도록 설정하려면 필드 위의 줄임표를 클릭한 다음, **파이프라인 매개 변수에 추가**를 클릭합니다. 

![샘플 설정](media/how-to-run-batch-predictions-ui/ui-batch-scoring-8.png)

그런 다음, 매개 변수에 이름과 기본값을 지정합니다. 이름은 매개 변수를 식별하고 REST 호출에서 지정하는 데 사용됩니다.

![파이프라인 매개 변수](media/how-to-run-batch-predictions-ui/ui-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>일괄 처리 유추 파이프라인 배포

이제 파이프라인을 배포할 준비가 되었습니다. **배포** 단추를 클릭하면 엔드포인트를 설정하는 인터페이스가 열립니다. 드롭다운을 클릭하고 **새 파이프라인 엔드포인트**를 선택합니다.

![파이프라인 배포](media/how-to-run-batch-predictions-ui/ui-batch-scoring-10.png)

엔드포인트에 이름 및 설명(선택 사항)을 지정합니다. 맨 아래 근처에 `sample-rate` 매개 변수가 기본값이 0.8로 구성된 것이 보입니다. 준비가 되면 **배포**를 클릭합니다.

![엔드포인트 설정](media/how-to-run-batch-predictions-ui/ui-batch-scoring-11.png)

## <a name="manage-endpoints"></a>엔드포인트 관리 

배포가 완료되면 **엔드포인트** 탭으로 이동하여 방금 만든 엔드포인트의 이름을 클릭합니다.

![엔드포인트 링크](media/how-to-run-batch-predictions-ui/ui-batch-scoring-12.png)

이 화면에는 특정 엔드포인트 아래 게시된 모든 파이프라인이 표시됩니다. 유추 파이프라인을 클릭합니다.

![유추 파이프라인](media/how-to-run-batch-predictions-ui/ui-batch-scoring-13.png)

파이프라인 세부 정보 페이지에 파이프라인에 대한 자세한 실행 기록 및 연결 문자열 정보가 표시됩니다. **실행** 단추를 클릭하여 파이프라인의 수동 실행을 만듭니다.

![파이프라인 세부 정보](media/how-to-run-batch-predictions-ui/ui-batch-scoring-14.png)

실행 설정에서, 실행에 대한 설명을 제공하고 파이프라인 매개 변수의 값을 변경할 수 있습니다. 이번에는 샘플링 비율 0.9로 유추 파이프라인을 다시 실행합니다. **실행**을 클릭하여 파이프라인을 실행합니다.

![파이프라인 실행](media/how-to-run-batch-predictions-ui/ui-batch-scoring-15.png)

**Consume**(사용) 탭에는 파이프라인을 다시 실행하기 위한 REST 엔드포인트가 포함되어 있습니다. REST 호출을 수행하려면 OAuth 2.0 전달자 유형 인증 헤더가 필요합니다. 작업 영역에 대한 인증을 설정하고 매개 변수가 있는 REST 호출을 수행하는 방법에 대한 자세한 내용은 다음 [자습서 섹션](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)을 참조하세요.

## <a name="next-steps"></a>다음 단계

디자이너 [자습서](tutorial-designer-automobile-price-train-score.md)에 따라 회귀 모델을 학습하고 배포합니다.