---
title: Azure Data Factory에서 사용자 지정 이벤트 트리거 만들기
description: Azure Data Factory에서 Event Grid에 게시된 사용자 지정 이벤트에 대한 응답으로 파이프라인을 실행하는 트리거를 만드는 방법을 알아봅니다.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785651"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>사용자 지정 이벤트에 대한 응답으로 파이프라인을 실행하는 트리거 만들기(미리 보기)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Data Factory 파이프라인에서 만들 수 있는 사용자 지정 이벤트 트리거를 설명합니다.

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 Data Factory 고객이 특정 이벤트 발생을 기반으로 파이프라인을 트리거해야 하는 경우가 종종 있습니다. [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와의 Data Factory 네이티브 통합에 이제 [사용자 지정 이벤트](../event-grid/custom-topics.md)가 포함됩니다. 고객은 Event Grid 토픽에 임의의 이벤트를 보내고, Data Factory는 토픽을 구독 및 수신 대기하고, 그에 따라 파이프라인을 트리거합니다.

> [!NOTE]
> 이 문서에서 설명하는 통합은 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)에 따라 달라집니다. 구독이 Event Grid 리소스 공급자에 등록되어 있는지 확인합니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요. *Microsoft.EventGrid/eventSubscriptions/* 작업을 수행할 수 있어야 합니다. 이 작업은 EventGrid EventSubscription 참여자 기본 제공 역할의 일부입니다.

또한 고객은 파이프라인 매개 변수와 사용자 지정 이벤트 트리거를 결합하여 파이프라인 실행에서 사용자 지정 _데이터_ 페이로드를 구문 분석하고 참조할 수 있습니다. 사용자 지정 이벤트 페이로드의 _데이터_ 필드는 자유 형식의 json 키-값 구조이며, 고객에게 이벤트 기반 파이프라인 실행에 대한 최대 제어를 제공합니다.

> [!IMPORTANT]
> 때때로 매개 변수화에서 참조되는 키가 사용자 지정 이벤트 페이로드에서 누락될 수 있습니다. _keyName_ 속성이 없기 때문에 식을 계산할 수 없다는 오류와 함께 _트리거 실행_ 이 실패합니다. 이벤트에 의해 트리거되는 _파이프라인 실행_ 이 __없습니다__.

## <a name="setup-event-grid-custom-topic"></a>Event Grid 사용자 지정 토픽 설정

Data Factory에서 사용자 지정 이벤트 트리거를 사용하려면 _먼저_ [Event Grid에서 사용자 지정 토픽](../event-grid/custom-topics.md)을 설정해야 합니다. 이 워크플로는 Data Factory가 사용자를 위해 토픽을 설정하는 스토리지 이벤트 트리거와 다릅니다. 여기서는 직접 Azure Event Grid를 탐색하고 토픽을 만들어야 합니다. 사용자 지정 토픽을 만드는 방법에 대한 자세한 내용은 Azure Event Grid [포털 자습서](../event-grid/custom-topics.md#azure-portal-tutorials) 및 [CLI 자습서](../event-grid/custom-topics.md#azure-cli-tutorials)를 참조하세요.

Data Factory는 이벤트가 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)에 따른다고 예상합니다. 이벤트 페이로드에 다음 필드가 있는지 확인합니다.

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

이 섹션에서는 Azure Data Factory 사용자 인터페이스 안에서 스토리지 이벤트 트리거를 만드는 방법을 살펴봅니다.

1. 연필 기호와 함께 표시되는 **편집** 탭으로 전환합니다.

1. 메뉴에서 **트리거** 를 선택한 다음 **새로 만들기/편집** 을 선택합니다.

1. **트리거 추가** 페이지에서 **트리거 선택...** 을 선택한 다음 **+새로 만들기** 를 선택합니다.

1. 트리거 유형 **사용자 지정 이벤트** 를 선택합니다.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Data Factory UI에 새 사용자 지정 이벤트 트리거를 만드는 작성자 페이지의 스크린샷." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Azure 구독 드롭다운에서 사용자 지정 토픽을 선택하거나 이벤트 토픽 범위를 수동으로 입력합니다.

   > [!NOTE]
   > 새 사용자 지정 이벤트 트리거를 만들거나 기존 사용자 지정 이벤트 트리거를 수정하려면 Data Factory에 로그인하고 스토리지 이벤트 트리거를 게시하는 데 사용한 Azure 계정에 토픽에 대한 적절한 역할 기반 액세스 제어(Azure RBAC) 권한이 있어야 합니다. 추가 권한은 필요하지 않습니다. Azure Data Factory의 서비스 주체에게는 Event Grid에 대한 특별한 권한이 필요하지 _않습니다_. 액세스 제어에 대한 자세한 내용은 [역할 기반 액세스 제어](#role-based-access-control)를 참조하세요.

1. **Subject begins with** 속성과 **Subject ends with** 속성을 사용하면 파이프라인을 트리거할 이벤트를 필터링할 수 있습니다. 두 속성 모두 선택 사항입니다.

1. **+새로 만들기** 를 사용하여 필터링할 **이벤트 유형** 을 추가합니다. 사용자 지정 이벤트 트리거는 목록에 OR 관계를 사용합니다. 사용자 지정 이벤트에 여기에 나열된 것과 일치하는 _eventType_ 속성이 있는 경우 파이프라인 실행이 트리거됩니다. 이벤트 형식은 대/소문자를 구분하지 않습니다. 예를 들어 아래 스크린샷에서 트리거는 제목이 _factories_ 로 시작하는 모든 _copycompleted_ 또는 _copysucceeded_ 이벤트와 일치합니다.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Data Factory UI에서 이벤트 유형 및 제목 필터링을 설명하는 트리거 편집 페이지의 스크린샷.":::

1. 사용자 지정 이벤트 트리거는 사용자 지정 _데이터_ 페이로드를 구문 분석하고 파이프라인으로 보낼 수 있습니다. 먼저 파이프라인 매개 변수를 만들고 **매개 변수** 페이지에서 값을 입력합니다. **@triggerBody().event.data._keyName_** 형식을 사용하여 데이터 페이로드를 구문 분석하고, 값을 파이프라인 매개 변수에 전달합니다. 자세한 설명은 [파이프라인의 참조 트리거 메타데이터](how-to-use-trigger-parameterization.md) 및 [사용자 지정 이벤트 트리거의 시스템 변수](control-flow-system-variables.md#custom-event-trigger-scope)를 참조하세요.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="파이프라인 매개 변수 설정의 스크린샷.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="사용자 지정 이벤트의 데이터 페이로드를 참조하는 매개 변수 페이지의 스크린샷.":::

1. 완료되면 **확인** 을 클릭합니다.

## <a name="json-schema"></a>JSON 스키마

다음 표에는 사용자 지정 이벤트 트리거와 관련된 스키마 요소의 개요가 나와 있습니다.

| **JSON 요소** | **설명** | **형식** | **허용되는 값** | **필수** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **범위** | Event Grid 토픽의 Azure Resource Manager 리소스 ID입니다. | String | Azure Resource Manager ID | 예 |
| **events** | 이 트리거를 발생시키는 이벤트 유형입니다. | 문자열 배열    |  | 예, 하나 이상의 값이 필요합니다. |
| **subjectBeginsWith** | 트리거가 발생하려면 제목 필드는 제공된 패턴으로 시작해야 합니다. 예를 들어 `factories`는 `factories`로 시작하는 이벤트 제목에 대해서만 트리거를 발생시킵니다. | String   | | 예 |
| **subjectEndsWith** | 트리거가 발생하려면 제목 필드는 제공된 패턴으로 끝나야 합니다. | String   | | 예 |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Data Factory는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자 지정 이벤트에 연결된 파이프라인 수신 대기를 위한 무단 액세스, 업데이트를 위한 구독, 트리거가 엄격히 금지되도록 합니다.

* 새 사용자 지정 이벤트 트리거를 성공적으로 만들거나 기존 사용자 지정 이벤트 트리거를 업데이트하려면 Data Factory에 로그인한 Azure 계정에 관련 스토리지 계정에 대한 적절한 액세스 권한이 있어야 합니다. 그렇지 않으면 작업이 실패하고 _액세스가 거부_ 됩니다.
* Data Factory는 Event Grid에 대한 특별한 권한이 필요하지 않으며, 작업의 Data Factory 서비스 사용자에게 특별한 Azure RBAC 권한을 할당할 필요가 _없습니다_.

특히 고객은 _/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics_ 에 대해 _Microsoft.EventGrid/EventSubscriptions/Write_ 권한이 필요합니다.

## <a name="next-steps"></a>다음 단계

* 트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#trigger-execution)를 참조하세요.
* 파이프라인에서 트리거 메타데이터를 참조하는 방법에 대해 알아보려면 [파이프라인 실행의 참조 트리거 메타데이터](how-to-use-trigger-parameterization.md)를 참조하세요.
