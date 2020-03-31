---
title: 이벤트 기반 기계 학습 워크플로우 생성
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 이벤트 기반 솔루션을 사용하도록 설정하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129696"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>이벤트 기반 기계 학습 워크플로 만들기(미리 보기)

[Azure 이벤트 그리드는](https://docs.microsoft.com/azure/event-grid/) Azure 기계 학습 이벤트를 지원합니다. 작업 영역 내에서 실행 상태 변경, 실행 완료, 모델 등록, 모델 배포 및 데이터 드리프트 검색과 같은 이벤트를 구독하고 사용할 수 있습니다.

이벤트 유형에 대한 자세한 내용은 이벤트 그리드 및 Azure 기계 학습 [이벤트 그리드 스키마와의](/azure/event-grid/event-schema-machine-learning) [Azure 기계 학습 통합을](concept-event-grid-integration.md) 참조하십시오.

이벤트 그리드를 사용하여 다음과 같은 일반적인 시나리오를 활성화합니다.

* 실행 실패 시 이메일 보내기 및 실행 완료
* 모델이 등록된 후 Azure 함수 사용
* Azure 기계 학습에서 다양한 끝점으로 의 이벤트 스트리밍
* 드리프트가 감지되면 ML 파이프라인 트리거

> [!NOTE] 
> 현재 runStatusChanged 이벤트는 실행 상태가 **실패한** 경우에만 트리거됩니다.
>

## <a name="prerequisites"></a>사전 요구 사항
* 이벤트를 만들 Azure 기계 학습 작업 영역에 대한 기여자 또는 소유자 액세스.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Azure 포털을 사용하여 EventGrid 구성

1. Azure [포털을](https://portal.azure.com) 열고 Azure 기계 학습 작업 영역으로 이동합니다.

1. 왼쪽 막대에서 __이벤트를__ 선택한 다음 **이벤트 구독을 선택합니다.** 

    ![작업 공간에서 이벤트 선택](./media/how-to-use-event-grid/select-event.png)

1. 사용할 이벤트 유형을 선택합니다. 예를 들어 다음 스크린샷에서 __모델 등록,__ __모델 배포,__ __실행 완료__및 데이터 __집합 드리프트가 검색됨을__선택했습니다.

    ![추가 이벤트 유형](./media/how-to-use-event-grid/add-event-type-updated.png)

1. 이벤트를 게시할 끝점을 선택합니다. 다음 스크린샷에서 __이벤트 허브는__ 선택한 끝점입니다.

    ![선택 이벤트 처리기](./media/how-to-use-event-grid/select-event-handler.png)

선택 영역을 확인한 후 을 __클릭합니다.__ 구성 후 이러한 이벤트는 끝점으로 푸시됩니다.


### <a name="configure-eventgrid-using-the-cli"></a>CLI를 사용하여 이벤트 그리드 구성

최신 [Azure CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)설치하거나 Azure 구독의 일부로 제공되는 Azure 클라우드 셸을 사용할 수 있습니다.

이벤트 그리드 확장을 설치하려면 CLI에서 다음 명령을 사용합니다.

```azurecli-interactive
az add extension --name eventgrid
```

다음 예제에서는 Azure 구독을 선택하고 Azure 기계 학습에 대한 새 이벤트 구독을 만드는 방법을 보여 줍니다.

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

## <a name="filter-events"></a>이벤트 필터링

이벤트를 설정할 때 특정 이벤트 데이터에 대해서만 트리거에 필터를 적용할 수 있습니다. 아래 예제에서 실행 상태 변경 된 이벤트에 대 한 실행 형식별로 필터링할 수 있습니다. 이 이벤트는 조건이 충족될 때만 트리거됩니다. 필터링할 수 있는 이벤트 데이터에 대해 알아보려면 [Azure Machine Learning 이벤트 그리드 스키마를](/azure/event-grid/event-schema-machine-learning) 참조하십시오. 

1. Azure 포털로 이동하여 새 구독 또는 기존 구독을 선택합니다. 

1. 필터 탭을 선택하고 고급 필터로 아래로 스크롤합니다. **키** 및 **값에서** 필터링할 속성 형식을 제공합니다. 여기서 실행 유형이 파이프라인 실행 또는 파이프라인 단계 실행인 경우에만 이벤트가 트리거되는 것을 볼 수 있습니다.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="필터 이벤트":::

## <a name="sample-scenarios"></a>샘플 시나리오

### <a name="use-a-logic-app-to-send-email-alerts"></a>로직 앱을 사용하여 이메일 알림 보내기

[Azure 논리 앱을](https://docs.microsoft.com/azure/logic-apps/) 활용하여 모든 이벤트에 대한 전자 메일을 구성합니다. 조건에 맞게 사용자 지정하고 받는 사람을 지정하여 함께 작업하는 팀 간에 공동 작업 및 인식을 활성화할 수 있습니다.

1. Azure 포털에서 Azure 기계 학습 작업 영역으로 이동하여 왼쪽 막대에서 이벤트 탭을 선택합니다. 여기에서 논리 __앱을__선택합니다. 

    ![선택-로직-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 논리 앱 UI에 로그인하고 기계 학습 서비스를 주제 유형으로 선택합니다. 

    ![선택 주제 유형](./media/how-to-use-event-grid/select-topic-type.png)

1. 알림을 받을 이벤트를 선택합니다. 예를 들어 다음 스크린샷 __RunCompleted__.

    ![선택-이벤트 실행 완료](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 위의 섹션에서 필터링 메서드를 사용하거나 필터를 추가하여 이벤트 유형의 하위 집합에서만 논리 앱을 트리거할 수 있습니다. 다음 스크린샷에서는 __/datadriftID/runs/의__ __접두사 필터가__ 사용됩니다.

    ![필터 이벤트](./media/how-to-use-event-grid/filtering-events.png)

1. 다음으로 이 이벤트를 사용하고 전자 메일을 검색하는 단계를 추가합니다. 이벤트를 수신하는 데 사용할 수 있는 여러 가지 메일 계정이 있습니다. 이메일 경고를 보낼 시기에 대한 조건을 구성할 수도 있습니다.

    ![이메일-작업 선택](./media/how-to-use-event-grid/select-email-action.png)

1. __이메일 보내기를__ 선택하고 매개 변수를 입력합니다. 주제에 이벤트를 필터링하는 데 도움이 되는 __이벤트 유형__ 및 __주제를__ 포함할 수 있습니다. 메시지 본문에 실행을 위한 작업 영역 페이지에 대한 링크를 포함할 수도 있습니다. 

    ![구성 전자 메일 본문](./media/how-to-use-event-grid/configure-email-body.png)

1. 이 작업을 저장하려면 페이지 왼쪽 모서리에 있는 **As 저장을** 선택합니다. 나타나는 오른쪽 막대에서 이 작업 만들기를 확인합니다.

    ![확인 논리 앱 만들기](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>논리 앱을 사용하여 데이터 드리프트가 발생할 때 재학습 워크플로를 트리거합니다.

모델은 시간이 지남에 따라 부실해지며 실행 중인 컨텍스트에서 유용하지 않습니다. 모델을 다시 학습할 때인지 를 알 수 있는 한 가지 방법은 데이터 드리프트를 감지하는 것입니다. 

이 예제에서는 Azure Logic 앱에서 이벤트 그리드를 사용하여 재교육을 트리거하는 방법을 보여 주며 있습니다. 이 예제는 모델의 학습과 서비스 데이터 집합 간에 데이터 드리프트가 발생하는 경우 Azure Data Factory 파이프라인을 트리거합니다.

시작하기 전에 다음 작업을 수행합니다.

* 작업 영역에서 [데이터 드리프트를 감지하기]( https://aka.ms/datadrift) 위해 데이터 집합 모니터 설정
* 게시된 [Azure 데이터 팩터리 파이프라인을](https://docs.microsoft.com/azure/data-factory/)만듭니다.

이 예제에서는 간단한 Data Factory 파이프라인을 사용하여 파일을 Blob 저장소에 복사하고 게시된 기계 학습 파이프라인을 실행합니다. 이 시나리오에 대한 자세한 내용은 Azure [데이터 팩터리에서 기계 학습 단계를](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service) 설정하는 방법을 참조하세요.

![adf-ml파이프라인 스테이지](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 논리 앱을 만드는 것부터 시작합니다. [Azure 포털로](https://portal.azure.com)이동하여 논리 앱을 검색하고 만들기를 선택합니다.

    ![검색 논리 앱](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 요청된 정보를 입력합니다. 환경을 단순화하려면 Azure 데이터 팩터리 파이프라인 및 Azure 기계 학습 작업 영역과 동일한 구독 및 리소스 그룹을 사용합니다.

    ![설정 논리 - 앱 - adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 논리 앱을 만든 후 __이벤트 그리드 리소스 이벤트가 발생하는 경우를__선택합니다. 

    ![선택-이벤트-그리드 트리거](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 로그인하여 이벤트에 대한 세부 정보를 입력합니다. 리소스 __이름을__ 작업 영역 이름으로 설정합니다. 이벤트 __유형을__ __데이터 집합으로 설정드드했습니다.__

    ![로그인 및 추가 이벤트](./media/how-to-use-event-grid/login-and-add-event.png)

1. 새 단계를 추가하고 Azure __데이터 팩터리__를 검색합니다. __파이프라인 실행 만들기를 선택합니다.__ 

    ![생성-adf파이프라인 실행](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 로그인하고 실행할 게시된 Azure 데이터 팩터리 파이프라인을 지정합니다.

    ![Adf-파이프라인 지정](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 페이지 왼쪽 상단에 있는 저장 버튼을 사용하여 논리 앱을 **저장하고** 만듭니다. 앱을 보려면 [Azure 포털의](https://portal.azure.com) 작업 영역으로 이동하여 **이벤트를**클릭합니다.

    ![쇼 로직 앱 웹후크](./media/how-to-use-event-grid/show-logic-app-webhook.png)

이제 드리프트가 발생하면 데이터 팩터리 파이프라인이 트리거됩니다. [새 작업 영역 포털에서](https://ml.azure.com)데이터 드리프트 실행 및 기계 학습 파이프라인에 대한 세부 정보를 봅니다. 

![작업 영역 보기](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Azure 함수를 사용하여 태그를 기반으로 모델을 배포합니다.

Azure 기계 학습 모델 개체에는 모델 이름, 버전, 태그 및 속성과 같은 배포를 피벗할 수 있는 매개 변수가 포함되어 있습니다. 모델 등록 이벤트는 끝점을 트리거할 수 있으며 Azure Function을 사용하여 해당 매개 변수의 값에 따라 모델을 배포할 수 있습니다.

예를 들어 리포지토리를 [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) 참조하고 **readme** 파일의 단계를 따릅니다.

## <a name="next-steps"></a>다음 단계

* 사용 가능한 이벤트에 대한 자세한 내용은 [Azure 기계 학습 이벤트 스키마를](/azure/event-grid/event-schema-machine-learning) 참조하십시오.
