---
title: Azure 기계 학습 이벤트 사용
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure Event Grid를 사용하여 Azure 기계 학습에서 생성된 이벤트를 구독, 반응 및 구독 취소하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139048"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Azure 기계 학습 이벤트 사용(미리 보기)

Azure Machine Learning은 모델 학습, 모델 배포 및 모니터링을 비롯한 기계 학습 프로세스의 전체 수명 주기를 관리합니다. Azure Machine Learning 이벤트를 사용하면 응용 프로그램이 최신 서버리스를 사용하여 학습 실행 완료, 모델 등록 및 배포, 데이터 드리프트 검색 과 같은 기계 학습 수명 주기 동안 이벤트에 반응할 수 있습니다. 아키텍처. 

이러한 이벤트는 [Azure 이벤트 그리드를](https://azure.microsoft.com/services/event-grid/)통해 게시됩니다. 고객은 Azure 포털, Powershell 또는 Azure CLI를 사용하여 [하나 이상의 이벤트 유형을 지정하고 조건을 필터링하여](/azure/event-grid/event-filtering)이벤트를 쉽게 구독할 수 있습니다. 또한 고객은 Azure Functions, Azure 논리 앱 또는 일반 웹후크와 같은 광범위한 이벤트 처리기를 빌드할 수 있습니다. Azure Machine Learning은 Azure Event Grid와 함께 이벤트 기반 응용 프로그램을 빌드할 수 있도록 사용 가능하고 안정적이며 내결함성이 높은 이벤트 배달 플랫폼을 제공합니다.

이벤트 그리드를 사용하여 Azure 기계 학습을 사용하는 방법에 대한 자세한 내용은 [이벤트 기반 기계 학습 워크플로 만들기(미리 보기)를](how-to-use-event-grid.md)참조하십시오.

## <a name="the-event-model"></a>이벤트 모델 

Azure Event Grid는 Azure 기계 학습 및 기타 Azure 서비스와 같은 소스에서 이벤트를 읽습니다. 그런 다음 이러한 이벤트는 Azure 이벤트 허브, Azure Functions, 논리 앱 등과 같은 이벤트 처리기로 전송됩니다. 다음 다이어그램에서는 Event Grid가 소스와 처리기를 연결하는 방법을 보여 주지만 지원되는 통합의 포괄적인 목록은 아닙니다.

![Azure Event Grid 기능 모델](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

이벤트 소스 및 이벤트 처리기에 대한 자세한 내용은 [이벤트 그리드란 무엇입니까?](/azure/event-grid/overview)

## <a name="azure-machine-learning-event-types"></a>Azure 기계 학습 이벤트 유형

Azure 기계 학습은 기계 학습 수명 주기의 다양한 지점에서 이벤트를 제공합니다. 

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 기계 학습 실험 실행이 완료되면 발생합니다. |
| `Microsoft.MachineLearningServices.ModelRegistered` | 기계 학습 모델이 작업 영역에 등록될 때 발생합니다. |
| `Microsoft.MachineLearningServices.ModelDeployed` | 하나 이상의 모델이 있는 추론 서비스 배포가 완료되면 발생합니다. |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 두 데이터 집합에 대한 데이터 드리프트 감지 작업이 완료되면 발생합니다. |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 실행 상태가 변경될 때 발생하며 현재 실행 상태가 '실패'한 경우에만 발생합니다. |

## <a name="subscribe-to-machine-learning-events"></a>기계 학습 이벤트 구독

Azure 기계 학습 이벤트에 대한 구독은 RBAC(역할 기반 액세스 제어)에 의해 보호됩니다. 작업 영역의 [참여자 또는 소유자만](how-to-assign-roles.md#default-roles) 이벤트 구독을 생성, 업데이트 및 삭제할 수 있습니다.

이벤트 구독은 다양한 조건에 따라 필터링할 수 있습니다. 이벤트 구독의 [생성](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 동안 또는 [나중에](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 이벤트 구독에 필터를 적용할 수 있습니다. 

### <a name="filter-by-event-type"></a>이벤트 유형별 필터링
이벤트 구독은 하나 이상의 Azure 기계 학습 이벤트 유형을 지정할 수 있습니다.

### <a name="filter-by-event-subject"></a>이벤트 제목별로 필터링
Azure Event Grid는 일치하는 주제가 있는 이벤트가 구독자에게 전달되도록 __일치로 시작하고__ __끝나는__ 주제 필터를 지원합니다. 기계 학습 이벤트에 따라 주제 형식이 다릅니다.

| 이벤트 유형 | 제목 형식 | 샘플 피사체 |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>고급 필터링

Azure Event Grid는 게시된 이벤트 스키마를 기반으로 고급 필터링도 지원합니다. Azure 기계 학습 이벤트 스키마 세부 정보는 [Azure 컴퓨터 학습에 대한 Azure 이벤트 Grid 이벤트 스키마에서](../event-grid/event-schema-machine-learning.md)찾을 수 있습니다.

수행할 수 있는 일부 고급 필터링은 다음과 같습니다.

* 이벤트의 `Microsoft.MachineLearningServices.ModelRegistered` 경우 모델의 태그 값을 필터링하려면 다음을 수행합니다.

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

필터를 적용하는 방법에 대한 자세한 내용은 [이벤트 그리드에 대한 필터 이벤트를](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)참조하십시오.

## <a name="consume-machine-learning-events"></a>기계 학습 이벤트 사용

기계 학습 이벤트를 처리하는 응용 프로그램은 다음과 같은 몇 가지 권장 방법을 따라야 합니다.

> [!div class="checklist"]
> * 이벤트를 동일한 이벤트 처리기로 라우팅하도록 여러 구독을 구성할 수 있으므로 이벤트가 특정 원본에서 온 것으로 가정하지 않고 메시지 의 항목을 확인하여 예상되는 기계 학습 작업 영역에서 오는지 확인하는 것이 중요합니다.
> * 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> * 메시지가 잘못된 순서로 오거나 조금 늦게 도착할 수도 있으므로 etag 필드를 사용하여 개체에 대한 정보가 아직 최신 상태인지 여부를 확인합니다.  또한 sequencer 필드를 사용하여 특정 개체에 대한 이벤트 순서를 파악합니다.
> * 이해할 수 없는 필드는 무시합니다. 이 지침은 나중에 추가될 수 있는 새로운 기능에 적용하는 데도 도움이 됩니다.
> * 실패하거나 취소된 Azure 기계 학습 작업은 이벤트를 트리거하지 않습니다. 예를 들어 모델 배포가 실패하면 Microsoft.MachineLearningServices.ModelDeploy는 트리거되지 않습니다. 응용 프로그램을 디자인할 때 이러한 오류 모드를 고려하십시오. 항상 Azure 기계 학습 SDK, CLI 또는 포털을 사용하여 작업 상태를 확인하고 자세한 실패 이유를 이해할 수 있습니다.

Azure Event Grid를 사용하면 고객이 Azure 기계 학습 이벤트에 의해 트리거될 수 있는 분리된 메시지 처리기를 빌드할 수 있습니다. 메시지 처리기의 몇 가지 주목할 만한 예는 다음과 같습니다.
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure 데이터 팩터리 파이프라인
* Azure 플랫폼 또는 다른 곳에서 호스팅될 수 있는 일반 웹후크

## <a name="next-steps"></a>다음 단계

이벤트 그리드에 대해 자세히 알아보고 Azure 기계 학습 이벤트를 사용해 보십시오.

- [Event Grid 정보](../event-grid/overview.md)
- [Azure 시스템 학습을 위한 Azure 이벤트 그리드 이벤트 스키마](../event-grid/event-schema-machine-learning.md)
- [Azure 기계 학습을 사용하여 이벤트 기반 워크플로 만들기](how-to-use-event-grid.md)
