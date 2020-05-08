---
title: ML 워크플로의 트리거 이벤트
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 이벤트에 기반 하 여 이벤트 구동 응용 프로그램, 프로세스 또는 CI/CD 워크플로를 트리거하여 ML 수명 주기를 간소화 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 236cc46bb6f9e5ed95e4a49068ac41ae77a736f5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982873"
---
# <a name="trigger-event-driven-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Azure Machine Learning 이벤트 (미리 보기)를 기반으로 이벤트 기반 응용 프로그램, 프로세스 또는 CI/CD 워크플로 트리거

이 문서에서는 [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/)에 의해 특정 조건이 검색 되는 경우 실패 알림 전자 메일 또는 ML 파이프라인 실행과 같은 Azure Machine Learning 이벤트에 따라 이벤트 기반 응용 프로그램, 프로세스 또는 CI/CD 워크플로를 설정 하는 방법에 대해 알아봅니다. 

Azure Machine Learning는 모델 학습, 모델 배포 및 모니터링을 포함 하 여 기계 학습 프로세스의 전체 수명 주기를 관리 합니다. 최신 서버를 사용 하지 않는 아키텍처를 사용 하 여 학습 실행 완료, 모델 등록 및 배포, 데이터 드리프트 검색 등의 Azure Machine Learning 이벤트에 대응 하는 Event Grid를 사용할 수 있습니다. 그런 다음 작업 영역 내에서 변경 된 실행 상태, 실행 완료, 모델 등록, 모델 배포 및 데이터 드리프트 감지와 같은 이벤트를 구독 하 고 사용할 수 있습니다.

이벤트 기반 작업에 대해 Event Grid를 사용 하는 경우:
* 실행 실패 시 전자 메일을 보내고 완료를 실행 합니다.
* 모델을 등록 한 후 Azure function 사용
* Azure Machine Learning에서 다양 한 끝점으로 이벤트 스트리밍
* 드리프트가 검색 되 면 ML 파이프라인 트리거

> [!NOTE] 
> 현재 runStatusChanged 이벤트는 실행 상태가 failed 인 경우에만 트리거됩니다 **.**

## <a name="prerequisites"></a>사전 요구 사항
Event Grid를 사용 하려면 이벤트를 만들 Azure Machine Learning 작업 영역에 대 한 참가자 또는 소유자 권한이 있어야 합니다.

## <a name="the-event-model--types"></a>이벤트 모델 & 형식

Azure Event Grid는 Azure Machine Learning 및 기타 Azure 서비스와 같은 원본에서 이벤트를 읽습니다. 이러한 이벤트는 Azure Event Hubs, Azure Functions, Logic Apps 및 기타와 같은 이벤트 처리기로 전송 됩니다. 다음 다이어그램은 Event Grid 원본과 처리기를 연결 하는 방법을 보여 주지만 지원 되는 통합의 포괄적인 목록은 아닙니다.

![Azure Event Grid 기능 모델](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

이벤트 원본 및 이벤트 처리기에 대 한 자세한 내용은 [Event Grid?](/azure/event-grid/overview)을 참조 하세요.

### <a name="event-types-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 이벤트 유형

Azure Machine Learning은 다양 한 기계 학습 수명 주기에서 이벤트를 제공 합니다. 

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Machine learning 실험 실행이 완료 되 면 발생 합니다. |
| `Microsoft.MachineLearningServices.ModelRegistered` | Machine learning 모델이 작업 영역에 등록 될 때 발생 합니다. |
| `Microsoft.MachineLearningServices.ModelDeployed` | 하나 이상의 모델이 포함 된 유추 서비스 배포가 완료 되 면 발생 합니다. |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 두 데이터 집합에 대 한 데이터 드리프트 검색 작업이 완료 되 면 발생 합니다. |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 실행 상태가 변경 될 때 발생 하며 현재 실행 상태가 ' 실패 ' 인 경우에만 발생 합니다. |

### <a name="filter--subscribe-to-events"></a>이벤트 & 구독 필터링

이러한 이벤트는 Azure Event Grid를 통해 게시 됩니다. Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 고객은 [하나 이상의 이벤트 유형과 필터링 조건을 지정](/azure/event-grid/event-filtering)하 여 이벤트를 쉽게 구독할 수 있습니다. 

이벤트를 설정할 때 특정 이벤트 데이터에 대 한 트리거로만 필터를 적용할 수 있습니다. 아래 예제에서는 실행 상태 변경 이벤트에 대해 실행 형식으로 필터링 할 수 있습니다. 이 이벤트는 조건이 충족 될 때만 트리거됩니다. 필터링 할 수 있는 이벤트 데이터에 대 한 자세한 내용은 [Azure Machine Learning event grid 스키마](/azure/event-grid/event-schema-machine-learning) 를 참조 하세요. 

Azure Machine Learning 이벤트에 대 한 구독은 RBAC (역할 기반 액세스 제어)에 의해 보호 됩니다. 작업 영역의 [참가자 또는 소유자](how-to-assign-roles.md#default-roles) 만 이벤트 구독을 만들고, 업데이트 하 고, 삭제할 수 있습니다.  이벤트 구독을 [만들](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 때 또는 나중에 이벤트 구독에 필터를 적용할 수 있습니다. 


1. Azure Portal로 이동 하 여 새 구독 또는 기존 구독을 선택 합니다. 

1. 필터 탭을 선택 하 고 아래로 스크롤하여 고급 필터를 선택 합니다. **키** 및 **값**에 대해 필터링 할 속성 형식을 제공 합니다. 여기서는 실행 유형이 파이프라인 실행 또는 파이프라인 단계 실행 인 경우에만 트리거되는 이벤트를 확인할 수 있습니다.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="이벤트 필터링":::


+ **이벤트 유형별로 필터링:** 이벤트 구독은 하나 이상의 Azure Machine Learning 이벤트 유형을 지정할 수 있습니다.

+ **이벤트 주제별로 필터링:** Azure Event Grid는 __로 시작__ 하 고 일치 항목으로 __끝나는__ 주체 필터를 지원 하므로 일치 하는 제목이 있는 이벤트는 구독자에 게 전달 됩니다. 다른 기계 학습 이벤트의 주체 형식이 다릅니다.

  | 이벤트 유형 | 제목 형식 | 샘플 주제 |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **고급 필터링**: Azure Event Grid는 게시 된 이벤트 스키마를 기반으로 하는 고급 필터링도 지원 합니다. Azure Machine Learning 이벤트 스키마 세부 정보는 [Azure Machine Learning에 대 한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-machine-learning.md)에서 찾을 수 있습니다.  수행할 수 있는 몇 가지 샘플 고급 필터는 다음과 같습니다.

  이벤트 `Microsoft.MachineLearningServices.ModelRegistered` 의 경우 모델의 태그 값을 필터링 하려면 다음을 수행 합니다.

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

## <a name="set-up-in-azure-portal"></a>Azure Portal에서 설정

1. [Azure Portal](https://portal.azure.com) 를 열고 Azure Machine Learning 작업 영역으로 이동 합니다.

1. 왼쪽 막대에서 __이벤트__ 를 선택한 다음 **이벤트 구독**을 선택 합니다. 

    ![select-events-in-workspace](./media/how-to-use-event-grid/select-event.png)

1. 사용할 이벤트 유형을 선택 합니다. 예를 들어 다음 스크린샷에서는 선택한 __모델을 등록__하 고, __모델을 배포__하 고, 실행을 __완료__하 고, __데이터 집합 드리프트를 검색__했습니다.

    ![-이벤트 유형 추가](./media/how-to-use-event-grid/add-event-type-updated.png)

1. 이벤트를 게시할 끝점을 선택 합니다. 다음 스크린샷에서 __이벤트 허브__ 는 선택 된 끝점입니다.

    ![select-이벤트 처리기](./media/how-to-use-event-grid/select-event-handler.png)

선택 항목을 확인 한 후 __만들기__를 클릭 합니다. 구성 후 이러한 이벤트는 끝점으로 푸시됩니다.


### <a name="set-up-with-the-cli"></a>CLI를 사용 하 여 설정

최신 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치 하거나 Azure 구독의 일부로 제공 되는 Azure Cloud Shell를 사용할 수 있습니다.

Event Grid 확장을 설치 하려면 CLI에서 다음 명령을 사용 합니다.

```azurecli-interactive
az add extension --name eventgrid
```

다음 예제에서는 Azure 구독을 선택 하 고 Azure Machine Learning에 대 한 새 이벤트 구독을 만드는 방법을 보여 줍니다.

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>예

### <a name="example-send-email-alerts"></a>예: 전자 메일 알림 보내기

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) 를 사용 하 여 모든 이벤트에 대 한 전자 메일을 구성 합니다. 조건을 사용 하 여 사용자 지정 하 고 수신자를 지정 하 여 공동 작업 하는 팀 간에 공동 작업 및 인식을 가능

1. Azure Portal에서 Azure Machine Learning 작업 영역으로 이동한 다음 왼쪽 표시줄에서 이벤트 탭을 선택 합니다. 여기에서 __논리 앱__을 선택 합니다. 

    ![선택-논리-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 논리 앱 UI에 로그인 하 고 토픽 유형으로 Machine Learning 서비스를 선택 합니다. 

    ![선택-토픽-형식](./media/how-to-use-event-grid/select-topic-type.png)

1. 알릴 이벤트를 선택 합니다. 예를 들어 다음 스크린샷에서 __Runcompleted가 완료__되었습니다.

    ![select-이벤트-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 위의 섹션에서 필터링 방법을 사용 하거나 필터를 추가 하 여 이벤트 형식의 하위 집합에 대 한 논리 앱만 트리거할 수 있습니다. 다음 스크린샷에서는 __/datadriftID/runs/__ 의 __접두사 필터__ 를 사용 합니다.

    ![필터-이벤트](./media/how-to-use-event-grid/filtering-events.png)

1. 그런 다음이 이벤트를 사용 하 고 전자 메일을 검색 하는 단계를 추가 합니다. 이벤트를 수신 하는 데 사용할 수 있는 여러 가지 메일 계정이 있습니다. 전자 메일 경고를 보낼 시기에 대 한 조건을 구성할 수도 있습니다.

    ![-전자 메일-동작을 선택 합니다.](./media/how-to-use-event-grid/select-email-action.png)

1. __전자 메일 보내기__ 를 선택 하 고 매개 변수를 입력 합니다. 주체에서 이벤트를 필터링 하는 데 도움이 되는 __이벤트 유형과__ __토픽__ 을 포함할 수 있습니다. 메시지 본문에서 실행할 작업 영역 페이지에 대 한 링크를 포함할 수도 있습니다. 

    ![구성-전자 메일-본문](./media/how-to-use-event-grid/configure-email-body.png)

1. 이 작업을 저장 하려면 페이지의 왼쪽 모서리에 있는 **다른 이름으로 저장** 을 선택 합니다. 표시 되는 오른쪽 막대에서이 작업 만들기를 확인 합니다.

    ![논리 확인-앱-만들기](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>예: 데이터 드리프트 트리거 재 학습

모델은 시간이 지남에 따라 오래 지속 되며, 실행 되는 컨텍스트에서는 유용 하지 않습니다. 모델을 다시 학습 시간 인지 여부를 확인 하는 한 가지 방법은 데이터 드리프트를 검색 하는 것입니다. 

이 예제에서는 Azure 논리 앱에서 event grid를 사용 하 여 재 학습을 트리거하는 방법을 보여 줍니다. 이 예에서는 모델의 학습 및 데이터 집합 처리 사이에 데이터 드리프트가 발생할 때 Azure Data Factory 파이프라인을 트리거합니다.

시작 하기 전에 다음 작업을 수행 합니다.

* 작업 영역에서 [데이터 드리프트를 검색]( https://aka.ms/datadrift) 하도록 데이터 집합 모니터 설정
* 게시 된 [Azure Data Factory 파이프라인](https://docs.microsoft.com/azure/data-factory/)을 만듭니다.

이 예제에서는 간단한 Data Factory 파이프라인을 사용 하 여 파일을 blob 저장소에 복사 하 고 게시 된 Machine Learning 파이프라인을 실행 합니다. 이 시나리오에 대 한 자세한 내용은 [Machine Learning 단계](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service) 를 설정 하는 방법을 참조 하세요 Azure Data Factory

![adf-mlpipeline-단계](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 논리 앱을 만드는 것으로 시작 합니다. [Azure Portal](https://portal.azure.com)로 이동 하 여 Logic Apps를 검색 하 고 만들기를 선택 합니다.

    ![검색-논리-앱](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 요청 된 정보를 입력 합니다. 환경을 단순화 하려면 Azure Data Factory 파이프라인 및 Azure Machine Learning 작업 영역과 동일한 구독 및 리소스 그룹을 사용 합니다.

    ![설정-논리 앱-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 논리 앱을 만든 후 __Event Grid 리소스 이벤트가 발생__하는 경우를 선택 합니다. 

    ![선택-이벤트 그리드-트리거](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 로그인 하 고 이벤트에 대 한 세부 정보를 입력 합니다. __리소스 이름을__ 작업 영역 이름으로 설정 합니다. __이벤트 유형을__ __DatasetDriftDetected__로 설정 합니다.

    ![로그인 및 추가 이벤트](./media/how-to-use-event-grid/login-and-add-event.png)

1. 새 단계를 추가 하 고 __Azure Data Factory__를 검색 합니다. __파이프라인 실행 만들기__를 선택 합니다. 

    ![만들기-adfpipeline-실행](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 로그인 하 고 실행할 게시 된 Azure Data Factory 파이프라인을 지정 합니다.

    ![-adf-파이프라인을 지정 합니다.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 페이지 왼쪽 위에 있는 **저장** 단추를 사용 하 여 논리 앱을 저장 하 고 만듭니다. 앱을 보려면 [Azure Portal](https://portal.azure.com) 작업 영역으로 이동 하 여 **이벤트**를 클릭 합니다.

    ![표시-논리-앱-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

이제 드리프트가 발생할 때 데이터 팩터리 파이프라인이 트리거됩니다. [새 작업 영역 포털](https://ml.azure.com)에서 데이터 드리프트 실행 및 machine learning 파이프라인에 대 한 세부 정보를 확인 합니다. 

![작업 영역 보기](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>예: 태그를 기반으로 모델 배포

Azure Machine Learning 모델 개체에는 모델 이름, 버전, 태그 및 속성과 같이 배포를 피벗할 수 있는 매개 변수가 포함 되어 있습니다. 모델 등록 이벤트는 끝점을 트리거할 수 있으며 Azure 함수를 사용 하 여 해당 매개 변수의 값을 기반으로 모델을 배포할 수 있습니다.

예제는 [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) 리포지토리를 참조 하 고 **추가 정보** 파일의 단계를 따릅니다.

## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure Machine Learning 이벤트를 제공 해 보세요.

- [Event Grid 정보](../event-grid/overview.md)

- [Azure Machine Learning에 대 한 이벤트 스키마](../event-grid/event-schema-machine-learning.md)

