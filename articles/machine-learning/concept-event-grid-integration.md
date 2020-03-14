---
title: Azure Machine Learning 이벤트 사용
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure Event Grid를 사용 하 여 Azure Machine Learning에서 생성 된 이벤트를 구독 하 고, 응답 하 고, 구독을 취소 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139048"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Azure Machine Learning 이벤트 사용 (미리 보기)

Azure Machine Learning는 모델 학습, 모델 배포 및 모니터링을 포함 하 여 기계 학습 프로세스의 전체 수명 주기를 관리 합니다. 최신 서버를 사용 하 여 응용 프로그램에서 기계 학습 수명 주기 동안 이벤트에 대응할 수 있도록 하는 Azure Machine Learning 이벤트 (예: 학습 실행 완료, 모델 등록 및 배포, 데이터 드리프트 검색) 아키텍처. 

이러한 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 게시 됩니다. Azure Portal, Powershell 또는 Azure CLI를 사용 하 여 고객은 하나 이상의 [이벤트 유형과 필터링 조건을 지정](/azure/event-grid/event-filtering)하 여 이벤트를 쉽게 구독할 수 있습니다. 또한 고객은 Azure Functions, Azure Logic Apps 또는 일반 웹 후크에 같은 광범위 한 이벤트 처리기를 구축할 수도 있습니다. Azure Event Grid와 함께 Azure Machine Learning는 이벤트 기반 응용 프로그램을 빌드할 수 있는 고가용성, 안정성 및 내결함성 이벤트 전달 플랫폼을 제공 합니다.

Event Grid에서 Azure Machine Learning를 사용 하는 방법에 대 한 자세한 내용은 [이벤트 기반 Machine Learning 워크플로 만들기 (미리 보기)](how-to-use-event-grid.md)를 참조 하세요.

## <a name="the-event-model"></a>이벤트 모델 

Azure Event Grid는 Azure Machine Learning 및 기타 Azure 서비스와 같은 원본에서 이벤트를 읽습니다. 이러한 이벤트는 Azure Event Hubs, Azure Functions, Logic Apps 및 기타와 같은 이벤트 처리기로 전송 됩니다. 다음 다이어그램은 Event Grid 원본과 처리기를 연결 하는 방법을 보여 주지만 지원 되는 통합의 포괄적인 목록은 아닙니다.

![Azure Event Grid 기능 모델](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

이벤트 원본 및 이벤트 처리기에 대 한 자세한 내용은 [Event Grid?](/azure/event-grid/overview)을 참조 하세요.

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning 이벤트 유형

Azure Machine Learning은 다양 한 기계 학습 수명 주기에서 이벤트를 제공 합니다. 

| 이벤트 유형 | Description |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Machine learning 실험 실행이 완료 되 면 발생 합니다. |
| `Microsoft.MachineLearningServices.ModelRegistered` | Machine learning 모델이 작업 영역에 등록 될 때 발생 합니다. |
| `Microsoft.MachineLearningServices.ModelDeployed` | 하나 이상의 모델이 포함 된 유추 서비스 배포가 완료 되 면 발생 합니다. |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 두 데이터 집합에 대 한 데이터 드리프트 검색 작업이 완료 되 면 발생 합니다. |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 실행 상태가 변경 될 때 발생 하며 현재 실행 상태가 ' 실패 ' 인 경우에만 발생 합니다. |

## <a name="subscribe-to-machine-learning-events"></a>Machine Learning 이벤트 구독

Azure Machine Learning 이벤트에 대 한 구독은 RBAC (역할 기반 액세스 제어)에 의해 보호 됩니다. 작업 영역의 [참가자 또는 소유자](how-to-assign-roles.md#default-roles) 만 이벤트 구독을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

이벤트 구독은 다양 한 조건에 따라 필터링 할 수 있습니다. 이벤트 구독의 [생성](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 동안 또는 [나중에](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 이벤트 구독에 필터를 적용할 수 있습니다. 

### <a name="filter-by-event-type"></a>이벤트 유형별 필터링
이벤트 구독은 하나 이상의 Azure Machine Learning 이벤트 유형을 지정할 수 있습니다.

### <a name="filter-by-event-subject"></a>이벤트 주제별로 필터링
Azure Event Grid는 __로 시작__ 하 고 일치 항목으로 __끝나는__ 주체 필터를 지원 하므로 일치 하는 제목이 있는 이벤트는 구독자에 게 전달 됩니다. 다른 기계 학습 이벤트의 주체 형식이 다릅니다.

| 이벤트 유형 | 제목 형식 | 샘플 주제 |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>고급 필터링

또한 Azure Event Grid는 게시 된 이벤트 스키마를 기반으로 하는 고급 필터링을 지원 합니다. Azure Machine Learning 이벤트 스키마 세부 정보는 [Azure Machine Learning에 대 한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-machine-learning.md)에서 찾을 수 있습니다.

수행할 수 있는 몇 가지 샘플 고급 필터는 다음과 같습니다.

* `Microsoft.MachineLearningServices.ModelRegistered` 이벤트의 경우 모델의 태그 값을 필터링 하려면 다음을 수행 합니다.

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

필터를 적용 하는 방법에 대 한 자세한 내용은 [Event Grid에 대 한 필터 이벤트](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)를 참조 하세요.

## <a name="consume-machine-learning-events"></a>Machine Learning 이벤트 사용

Machine Learning 이벤트를 처리 하는 응용 프로그램은 다음과 같은 몇 가지 권장 방법을 따라야 합니다.

> [!div class="checklist"]
> * 이벤트를 동일한 이벤트 처리기로 라우팅하는 여러 구독을 구성할 수 있으므로 이벤트를 특정 원본에서 가져온 것으로 가정 하지 않는 것이 중요 하며, 메시지의 항목을 확인 하 여 예상 되는 기계 학습 작업 영역에서 온 것인지 확인 해야 합니다.
> * 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> * 메시지가 잘못된 순서로 오거나 조금 늦게 도착할 수도 있으므로 etag 필드를 사용하여 개체에 대한 정보가 아직 최신 상태인지 여부를 확인합니다.  또한 sequencer 필드를 사용하여 특정 개체에 대한 이벤트 순서를 파악합니다.
> * 이해할 수 없는 필드는 무시합니다. 이 지침은 나중에 추가될 수 있는 새로운 기능에 적용하는 데도 도움이 됩니다.
> * 작업이 실패 하거나 취소 Azure Machine Learning 작업이 이벤트를 트리거하지 않습니다. 예를 들어 모델 배포가 실패 하는 경우 MachineLearningServices 배포 되지 않습니다. 응용 프로그램을 디자인할 때 이러한 오류 모드를 고려 합니다. 항상 Azure Machine Learning SDK, CLI 또는 포털을 사용 하 여 작업 상태를 확인 하 고 자세한 오류 원인을 파악할 수 있습니다.

Azure Event Grid를 사용 하 여 고객은 Azure Machine Learning 이벤트에 의해 트리거될 수 있는 결합 되지 않은 메시지 처리기를 만들 수 있습니다. 메시지 처리기의 몇 가지 주목할 만한 예는 다음과 같습니다.
* Azure 기능
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory 파이프라인
* Azure 플랫폼 또는 다른 곳에서 호스팅될 수 있는 일반 웹 후크

## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure Machine Learning 이벤트를 제공 해 보세요.

- [Event Grid 정보](../event-grid/overview.md)
- [Azure Machine Learning에 대 한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-machine-learning.md)
- [Azure Machine Learning를 사용 하 여 이벤트 기반 워크플로 만들기](how-to-use-event-grid.md)
