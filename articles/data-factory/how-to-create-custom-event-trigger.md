---
title: Azure Data Factory에서 사용자 지정 이벤트 트리거 만들기
description: Event Grid에 게시 된 사용자 지정 이벤트에 대 한 응답으로 파이프라인을 실행 하는 Azure Data Factory에서 사용자 지정 트리거를 만드는 방법에 대해 알아봅니다.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: e372808250b1d5b21cd5f6c5226922d5d6ad0f77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609698"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>사용자 지정 이벤트 (미리 보기)에 대 한 응답으로 파이프라인을 실행 하는 트리거 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Data Factory 파이프라인에서 만들 수 있는 사용자 지정 이벤트 트리거를 설명 합니다.

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 고객이 특정 이벤트를 기반으로 파이프라인을 트리거하기 위해 Data Factory 해야 하는 경우가 많습니다. Data Factory 기본 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 와의 기본 통합은 [사용자 지정 이벤트](../event-grid/custom-topics.md)를 다룹니다. 고객은 Event Grid Data Factory 토픽으로 임의의 이벤트를 보내고 토픽을 구독 및 수신 하 고 그에 따라 파이프라인을 트리거합니다.

> [!NOTE]
> 이 문서에서 설명하는 통합은 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)에 따라 달라집니다. 구독이 Event Grid 리소스 공급자에 등록되어 있는지 확인합니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요. *Microsoft EventGrid/Eventgrid/** 작업을 수행할 수 있어야 합니다. 이 작업은 EventGrid Eventgrid 참여자 기본 제공 역할의 일부입니다.

또한 파이프라인 매개 변수 및 사용자 지정 이벤트 트리거를 결합 하 여 고객은 파이프라인 실행에서 사용자 지정 _데이터_ 페이로드를 구문 분석 하 고 참조할 수 있습니다. 사용자 지정 이벤트 페이로드의 _데이터_ 필드는 자유 형식의 json 키-값 구조 이며, 고객에 게 이벤트 기반 파이프라인 실행에 대 한 최대 제어를 제공 합니다.

> [!IMPORTANT]
> 일반적으로 매개 변수화에서 참조 되는 키가 사용자 지정 이벤트 페이로드에서 누락 될 수 있습니다. 속성 _keyName_ 이 존재 하지 않으므로 식을 계산할 수 없다는 오류가 발생 하 여 _트리거 실행_ 이 실패 합니다. 이벤트에 의해 트리거되는 _파이프라인 실행_ 이 __없습니다__ .

## <a name="setup-event-grid-custom-topic"></a>설치 Event Grid 사용자 지정 항목

Data Factory에서 사용자 지정 이벤트 트리거를 사용 하려면 _먼저_ [Event Grid에서 사용자 지정 항목](../event-grid/custom-topics.md)을 설정 해야 합니다. 워크플로는 저장소 이벤트 트리거와 다르며, 여기서 Data Factory는 항목을 설정 합니다. 여기에서 Azure Event Grid 탐색 하 고 항목을 직접 만들어야 합니다. 사용자 지정 항목을 만드는 방법에 대 한 자세한 내용은 Azure Event Grid [포털 자습서](../event-grid/custom-topics.md#azure-portal-tutorials) 및 [CLI 자습서](../event-grid/custom-topics.md#azure-cli-tutorials) 를 참조 하세요.

데이터 팩터리는 이벤트 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 따르도록 합니다. 이벤트 페이로드에 다음 필드가 있는지 확인 합니다.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>Data Factory UI

이 섹션에서는 Azure Data Factory 사용자 인터페이스 내에서 저장소 이벤트 트리거를 만드는 방법을 보여 줍니다.

1. 연필 기호와 함께 표시 되는 **편집** 탭으로 전환 합니다.

1. 메뉴에서 **트리거** 를 선택 하 고 **새로 만들기/편집** 을 선택 합니다.

1. **트리거 추가** 페이지에서 **트리거 선택**...을 선택 하 고 **+ 새로 만들기** 를 선택 합니다.

1. 트리거 유형 **사용자 지정 이벤트** 를 선택 합니다.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Data Factory UI에 새 사용자 지정 이벤트 트리거를 만드는 작성자 페이지의 스크린샷" lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Azure 구독 드롭다운에서 사용자 지정 항목을 선택 하거나 이벤트 토픽 범위를 수동으로 입력 합니다.

   > [!NOTE]
   > 새 사용자 지정 이벤트 트리거를 만들거나 수정 하려면 Data Factory에 로그인 하 고 저장소 이벤트 트리거를 게시 하는 데 사용 되는 Azure 계정에 항목에 대 한 적절 한 역할 기반 액세스 제어 (Azure RBAC) 권한이 있어야 합니다. 추가 권한이 필요 하지 않습니다. Azure Data Factory에 대 한 서비스 주체에는 Event Grid에 대 한 특별 한 권한이 필요 _하지_ 않습니다. 액세스 제어에 대 한 자세한 내용은 [역할 기반 액세스 제어](#role-based-access-control) 섹션을 참조 하세요.

1. **주체는로 시작** 하 고 **제목 끝** 속성은 파이프라인을 트리거할 이벤트를 필터링 할 수 있도록 합니다. 두 속성은 모두 선택 사항입니다.

1. **+ 새로 만들기** 를 사용 하 여 필터링 할 **이벤트 유형을** 추가 합니다. 사용자 지정 이벤트 트리거 직원 a 또는 목록에 대 한 관계: 사용자 지정 이벤트에 여기에 나열 된 것과 일치 하는 _eventType_ 속성이 있는 경우 파이프라인 실행을 트리거합니다. 이벤트 유형은 대/소문자를 구분 하지 않습니다. 예를 들어 아래 스크린샷에서, 트리거는 주체가 _시작 하는_ 모든 _copycompleted_ 또는 _copycompleted_ 이벤트와 일치 합니다.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Data Factory UI에서 이벤트 유형과 제목 필터링을 설명 하는 트리거 편집 페이지의 스크린샷":::

1. 사용자 지정 이벤트 트리거는 사용자 지정 _데이터_ 페이로드를 구문 분석 하 고 파이프라인으로 보낼 수 있습니다. 먼저 파이프라인 매개 변수를 만들고 **매개 변수** 페이지에서 값을 입력 합니다. Format **@triggerBody (). data를 사용 합니다 _. keyName_** -데이터 페이로드를 구문 분석 하 고 파이프라인 매개 변수에 값을 전달 합니다. 자세한 내용은 [파이프라인의 참조 트리거 메타 데이터](how-to-use-trigger-parameterization.md) 및 [사용자 지정 이벤트 트리거의 시스템 변수](control-flow-system-variables.md#custom-event-trigger-scope) 를 참조 하세요.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="파이프라인 매개 변수 설정의 스크린샷":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="사용자 지정 이벤트의 데이터 페이로드를 참조 하는 매개 변수 페이지의 스크린샷":::

1. 작업이 완료 되 면 **확인을** 클릭 합니다.

## <a name="json-schema"></a>JSON 스키마

다음 표에서는 사용자 지정 이벤트 트리거와 관련 된 스키마 요소에 대 한 개요를 제공 합니다.

| **JSON 요소** | **설명** | **형식** | **허용되는 값** | **필수** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **범위** | Event grid 토픽의 Azure Resource Manager 리소스 ID입니다. | String | Azure Resource Manager ID | 예 |
| **events** | 이 트리거를 발생시키는 이벤트 유형입니다. | 문자열 배열    |  | 예, 하나 이상의 값이 필요 합니다. |
| **subjectBeginsWith** | 제목 필드는 트리거를 시작 하기 위해 제공 된 패턴으로 시작 해야 합니다. 예를 들어는 `factories` 로 시작 하는 이벤트 주체에 대 한 트리거를 발생 시킵니다 `factories` . | String   | | 아니요 |
| **subjectEndsWith** | 주제 필드는 트리거를 시작 하기 위해 제공 된 패턴으로 끝나야 합니다. | String   | | 아니요 |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Data Factory는 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자 지정 이벤트에 연결 된 수신 대기, 업데이트 구독 및 트리거 파이프라인에 대 한 무단 액세스가 제한적으로 금지 되도록 합니다.

* 새 사용자 지정 이벤트 트리거를 성공적으로 만들거나 기존 사용자 지정 이벤트 트리거를 업데이트 하려면 Data Factory에 로그인 한 Azure 계정에 관련 저장소 계정에 대 한 적절 한 액세스 권한이 있어야 합니다. 그렇지 않으면 작업이 실패 하 고 _액세스가 거부_ 됩니다.
* Data Factory에는 Event Grid에 대 한 특별 한 권한이 필요 하지 않으며, 작업에 대해 Data Factory 서비스 사용자에 게 특별 한 Azure RBAC 권한을 할당할 필요가 _없습니다_ .

특히 고객은 _/subscriptions/# # # #/resourceGroups//#_ # # #/providers/Microsoft.EventGrid/topics/someTopics에 대해 _Microsoft. Eventgrid/Eventgrid/Write 권한이 필요 합니다._

## <a name="next-steps"></a>다음 단계

* 트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#trigger-execution)를 참조하세요.
* 파이프라인에서 트리거 메타 데이터를 참조 하는 방법에 대해 알아봅니다. [파이프라인 실행의 참조 트리거 메타 데이터](how-to-use-trigger-parameterization.md) 를 참조 하세요.
