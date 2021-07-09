---
title: 엔드포인트 및 경로 관리(포털)
titleSuffix: Azure Digital Twins
description: Azure Portal을 사용하여 Azure Digital Twins 데이터에 대한 엔드포인트 및 이벤트 경로를 설정하고 관리하는 방법을 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bd3ae7c3dadc883c81f4f7a24da4fdcda83530ec
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110478220"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Azure Digital Twins에서의 엔드포인트 및 경로 관리(포털)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins에서는 [이벤트 알림](concepts-event-notifications.md)을 다운스트림 서비스 또는 연결된 컴퓨팅 리소스로 라우팅할 수 있습니다. 먼저 이벤트를 수신할 수 있는 ‘엔드포인트’를 설정하여 이 작업을 수행합니다. 그런 다음, Azure Digital Twins에서 생성되는 어떤 이벤트를 어느 엔드포인트로 전달할지 지정하는 ‘이벤트 경로’를 만들 수 있습니다.

이 문서는 [Azure Portal](https://portal.azure.com)을 사용하여 엔드포인트와 경로를 만드는 프로세스를 안내합니다.

또는 [이벤트 경로 API](/rest/api/digital-twins/dataplane/eventroutes), [SDK](concepts-apis-sdks.md#overview-data-plane-apis) 또는 [Azure Digital Twins CLI](concepts-cli.md)를 사용하여 엔드포인트와 경로를 관리할 수도 있습니다. 포털 대신에 이 메커니즘을 사용하는 문서 버전은 [방법: 엔드포인트 및 경로 관리(API 및 CLI)](how-to-manage-routes-apis-cli.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [무료로 설정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)할 수 있는 **Azure 계정** 이 필요합니다.
* Azure 구독에는 **Azure Digital Twins 인스턴스** 가 필요합니다. 인스턴스가 아직 없는 경우 [방법: 인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)의 단계를 사용하여 만들 수 있습니다. 이 문서의 뒷부분에서 쉽게 사용할 수 있도록 설정에서 다음 값을 가져옵니다.
    - 인스턴스 이름
    - Resource group

인스턴스를 설정한 후 [Azure Portal](https://portal.azure.com)에서 이 세부 정보를 확인할 수 있습니다. 포털에 로그인하고 포털 검색 창에서 해당 인스턴스의 이름을 검색합니다.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Azure Portal 검색 창의 스크린샷." lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

인스턴스에 대한 개요에서 이 세부 정보를 보려면 결과에서 해당 인스턴스를 선택합니다.

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Azure Portal의 Azure Digital Twins 인스턴스에 대한 개요 페이지의 스크린샷. 이름 및 리소스 그룹이 강조 표시됩니다.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins 엔드포인트 만들기

다음은 인스턴스에 대해 만들 수 있는 지원되는 엔드포인트 유형입니다.
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

다양한 엔드포인트 유형에 대한 자세한 정보는 [Azure 메시징 서비스 간 선택](../event-grid/compare-messaging-services.md)을 참조하세요.

이 섹션에서는 [Azure Portal](https://portal.azure.com)에서 이러한 엔드포인트 중 하나를 만드는 방법에 대해 설명합니다.

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>엔드포인트 만들기 

엔드포인트 리소스를 만든 후에는 Azure Digital Twins 엔드포인트에 사용할 수 있습니다. 새 엔드포인트를 만들려면 [Azure Portal](https://portal.azure.com)의 인스턴스 페이지로 이동합니다. 포털 검색 창에 해당 이름을 입력하여 인스턴스를 찾을 수 있습니다.

1. 인스턴스 메뉴에서 _엔드포인트_ 를 선택합니다. 그런 다음, 뒤에 오는 *엔드포인트* 페이지에서 *+ 엔드포인트 만들기* 를 선택합니다. 그러면 다음 단계에서 필드를 입력하는 *엔드포인트 만들기* 페이지가 열립니다.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Azure Portal에서 Event Grid 유형의 엔드포인트 만들기 스크린샷." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. 엔드포인트의 **이름** 을 입력하고 **엔드포인트 유형** 을 선택합니다.

1. [위에서](#prerequisite-create-endpoint-resources) 설명하는 구독과 엔드포인트 리소스를 비롯하여, 엔드포인트 유형에 필요한 다른 세부 정보를 완료합니다.
    1. Event Hub 및 Service Bus 엔드포인트의 경우 **인증 유형** 을 선택해야 합니다. Azure Digital Twins 인스턴스에 대해 [관리 ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview)로 엔드포인트를 사용하려는 경우, 미리 만든 권한 부여 규칙이나 ID 기반 인증을 통해 키 기반 인증을 사용할 수 있습니다. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Azure Portal에서 Event Hub 유형의 엔드포인트 만들기 스크린샷." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. _저장_ 을 선택하여 엔드포인트 만들기를 완료합니다.

>[!IMPORTANT]
> 엔드포인트에 대해 ID 기반 인증을 사용하기 위해서는 [방법: 이벤트 라우팅에 관리 ID 사용(미리 보기)](./how-to-enable-managed-identities-portal.md)의 단계에 따라 인스턴스에 대한 관리 ID를 만들어야 합니다.

엔드포인트를 만든 후 Azure Portal의 위쪽 표시줄에서 알림 아이콘을 확인하여 엔드포인트가 만들어졌는지 확인할 수 있습니다. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Azure Portal에서 엔드포인트 생성을 확인하는 알림의 스크린샷.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

엔드포인트 만들기가 실패하면 오류 메시지가 표시되며 몇 분 후 다시 시도합니다.

Azure Digital Twins 인스턴스의 *엔드포인트* 페이지로 돌아가서 만든 엔드포인트를 확인할 수도 있습니다.

이제 이벤트 그리드, 이벤트 허브 또는 Service Bus 토픽을 Azure Digital Twins 안에서 엔드포인트에 대해 선택한 이름의 엔드포인트로 사용할 수 있습니다. 일반적으로 [이 문서의 뒷부분에서](#create-an-event-route) 만들게 되는 **이벤트 경로** 의 대상으로 이 이름을 사용합니다.

### <a name="create-an-endpoint-with-dead-lettering"></a>배달 못한 편지 처리가 있는 엔드포인트 만들기

엔드포인트가 일정 시간 안에 또는 일정 횟수의 이벤트 배달 시도 후 이벤트를 배달할 수 없는 경우, 미배달 이벤트를 스토리지 계정에 보낼 수 있습니다. 이 프로세스를 **배달 못한 편지** 라고 합니다.

배달 못한 편지 처리를 사용하는 엔드포인트를 만들려면 Azure Portal이 아니라, [CLI 명령](concepts-cli.md) 또는 [컨트롤 플레인 API](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)를 사용하여 엔드포인트를 만들어야 합니다.

이 도구를 통해 해당 작업을 수행하는 방법에 대한 지침은 이 문서의 [API 및 CLI](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) 버전을 참조하세요.

## <a name="create-an-event-route"></a>이벤트 경로 만들기

실제로 Azure Digital Twins에서 엔드포인트로 데이터를 보내려면 **이벤트 경로** 를 정의해야 합니다. 이러한 경로를 통해 개발자는 시스템을 거쳐 다운스트림 서비스로 이벤트 흐름을 보낼 수 있습니다. 이벤트 경로에 대한 자세한 내용은 [개념: Azure Digital Twins 이벤트 라우팅](concepts-route-events.md)을 참조하세요.

**필수 조건**: 경로 만들기를 진행하려면 먼저 이 문서의 앞부분에서 설명한 대로 엔드포인트를 만들어야 합니다. 엔드포인트 설정이 완료되면 이벤트 경로 만들기를 진행할 수 있습니다.

>[!NOTE]
>최근에 엔드포인트를 배포한 경우 새 이벤트 경로에 해당 엔드포인트를 사용하기 **전에** 배포 완료의 유효성을 검사합니다. 엔드포인트가 준비되지 않아 경로를 설정하지 못한 경우 몇 분 정도 기다린 다음, 다시 시도하세요.

### <a name="creation-steps-with-the-azure-portal"></a>Azure Portal을 통한 만들기 단계

이벤트 경로 정의에는 다음 요소가 포함됩니다.
* 사용하려는 경로 이름
* 사용하려는 엔드포인트 이름
* 엔드포인트로 전송되는 이벤트를 정의하는 필터
    - 보내는 이벤트가 없게 경로를 사용하지 않도록 설정하려면 필터 값 `false`를 사용합니다.
    - 특정 필터링이 없는 경로를 사용하도록 설정하려면 필터 값 `true`를 사용합니다.
    - 다른 유형의 필터에 대한 자세한 내용은 아래의 [필터 이벤트](#filter-events) 섹션을 참조하세요.

단일 경로에서 여러 알림과 이벤트 유형을 선택하도록 허용할 수 있습니다.

이벤트 경로를 만들려면 [Azure Portal](https://portal.azure.com)에서 Azure Digital Twins 인스턴스에 대한 세부 정보 페이지로 이동합니다. 포털 검색 창에 해당 이름을 입력하여 인스턴스를 찾을 수 있습니다.

인스턴스 메뉴에서 _이벤트 경로_ 를 선택합니다. 그런 다음, 이어지는 *이벤트 경로* 페이지에서 *+ 이벤트 경로 만들기* 를 선택합니다. 

열리는 *이벤트 경로 만들기* 페이지에서 최소한 다음을 선택합니다.
* _이름_ 필드에서 경로 이름
* 경로를 만드는 데 사용할 _엔드포인트_ 

경로를 사용하려면 적어도 `true`인 **이벤트 경로 필터도 추가** 해야 합니다. (기본값 `false`를 그대로 두면 경로는 만들어지나 이벤트를 해당 경로로 보내지 않습니다.) 이를 위해 _고급 편집기_ 에 대한 스위치를 전환하여 사용하도록 설정하고 *필터* 상자에 `true`를 기입합니다.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Azure Portal에서 인스턴스에 대한 이벤트 경로 만들기의 스크린샷." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

완료되면 _저장_ 단추를 선택하여 이벤트 경로를 만듭니다.

## <a name="filter-events"></a>이벤트 필터링

위에서 설명한 것처럼 경로에는 **필터** 필드가 있습니다. 경로의 필터 값이 `false`라면 엔드포인트로 보내는 이벤트가 없습니다. 

최소 필터인 `true`를 사용하도록 설정한 후에는 엔드포인트가 Azure Digital Twins로부터 다양한 이벤트를 수신하게 됩니다.
* Azure Digital Twins 서비스 API를 사용하여 [디지털 트윈](concepts-twins-graph.md)에서 생성하는 원격 분석
* Azure Digital Twins 인스턴스의 트윈에 대한 속성 변경 시 발생하는 트윈 속성 변경 알림
* 트윈 또는 관계를 만들거나 삭제했을 때 발생하는 수명 주기 이벤트

더 구체적인 필터를 정의하면 전송되는 이벤트 유형을 제한할 수 있습니다.

이벤트 경로를 만들 때 이벤트 필터를 추가하려면 *이벤트 경로 만들기* 페이지의 "이벤트 경로 필터 추가" 섹션을 사용합니다. 

몇 가지 기본 공통 필터 옵션에서 선택하거나, 고급 필터 옵션을 사용하여 직접 사용자 지정 필터를 작성할 수 있습니다.

>[!NOTE]
> 필터는 **대/소문자를 구분** 하며 페이로드 대/소문자와 일치해야 합니다. 
>
> 즉, 원격 분석 필터라면 트윈의 모델에 정의된 대/소문자와는 반드시 일치할 필요는 없고, 디바이스가 보낸 원격 분석의 대/소문자와 일치해야 합니다.

#### <a name="use-the-basic-filters"></a>기본 필터 사용

기본 필터를 사용하려면 _이벤트 유형_ 옵션을 확장하고, 엔드포인트에 보내려는 이벤트에 해당하는 확인란을 선택합니다. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="이벤트의 확인란을 강조 표시하여 Azure Portal에서 기본 필터를 사용해 이벤트 경로를 만드는 스크린샷.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

그러면 선택한 필터의 텍스트로 필터 텍스트 상자가 자동 입력됩니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Azure Portal에서 기본 필터를 사용하여 이벤트 경로를 만드는 스크린샷으로, 이벤트 선택 후 자동 입력된 필터 텍스트가 강조 표시됩니다.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>고급 필터 사용

또는 고급 필터 옵션을 사용하여 고유의 사용자 지정 필터를 작성할 수 있습니다.

고급 필터 옵션을 사용하여 이벤트 경로를 만들려면 _고급 편집기_ 에 대한 스위치를 전환하여 사용하도록 설정합니다. 그런 다음, *필터* 상자에서 고유의 이벤트 필터를 작성할 수 있습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Azure Portal에서 고급 필터를 사용하여 이벤트 경로를 만드는 스크린샷.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

다음은 지원되는 경로 필터입니다. *필터 텍스트 스키마* 열의 세부 정보는 필터 상자에 입력할 수 있는 텍스트입니다.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>다음 단계

받을 수 있는 다양한 이벤트 메시지 유형에 대해 읽어 보세요.
* [개념: 이벤트 알림](concepts-event-notifications.md)