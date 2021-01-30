---
title: 끝점 및 경로 관리 (포털)
titleSuffix: Azure Digital Twins
description: Azure Portal를 사용 하 여 Azure Digital Twins 데이터에 대 한 끝점 및 이벤트 경로를 설정 하 고 관리 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0f705aa61f1fe627dc0c8227242538e01ffce1d5
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070831"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Azure Digital Twins에서 끝점 및 경로 관리 (포털)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins에서 [이벤트 알림을](how-to-interpret-event-data.md) 다운스트림 서비스 또는 연결 된 계산 리소스로 라우팅할 수 있습니다. 먼저 이벤트를 수신할 수 있는 ‘엔드포인트’를 설정하여 이 작업을 수행합니다. 그런 다음 Azure Digital Twins에 의해 생성 되는 이벤트를 지정 하는 [**이벤트 경로**](concepts-route-events.md) 를 끝점으로 전달할 수 있습니다.

이 문서에서는 [Azure Portal](https://portal.azure.com)를 사용 하 여 끝점과 경로를 만드는 과정을 안내 합니다.

또는 [이벤트 경로 api](/rest/api/digital-twins/dataplane/eventroutes), [Sdk](how-to-use-apis-sdks.md#overview-data-plane-apis)또는 [Azure Digital twins CLI](how-to-use-cli.md)를 사용 하 여 끝점과 경로를 관리할 수도 있습니다. 포털 대신 이러한 메커니즘을 사용 하는이 문서의 버전에 대해서는 [*방법: 끝점 및 경로 관리 (api 및 CLI)*](how-to-manage-routes-apis-cli.md)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

* **Azure 계정이** 필요 합니다 ( [여기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)에서 무료로 설정할 수 있음).
* Azure 구독에는 **Azure Digital Twins 인스턴스가** 필요 합니다. 인스턴스가 아직 없는 경우 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md)의 단계를 사용 하 여 인스턴스를 만들 수 있습니다. 이 문서의 뒷부분에서 사용할 수 있도록 다음 값을 설정 하는 것이 유용 합니다.
    - 인스턴스 이름
    - Resource group

인스턴스를 설정한 후 [Azure Portal](https://portal.azure.com) 에서 이러한 세부 정보를 찾을 수 있습니다. 포털에 로그인 하 고 포털 검색 표시줄에서 인스턴스 이름을 검색 합니다.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Azure Portal 검색 표시줄의 스크린샷" lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

인스턴스에 대 한 개요에서 이러한 세부 정보를 보려면 결과에서 인스턴스를 선택 합니다.

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Azure Digital Twins 인스턴스에 대 한 개요 페이지의 스크린샷 이름 및 리소스 그룹이 강조 표시 됩니다.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins에 대 한 끝점 만들기

다음은 인스턴스에 대해 만들 수 있는 지원 되는 끝점 유형입니다.
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

다른 끝점 형식에 대 한 자세한 내용은 [*Azure 메시징 서비스 중에서 선택*](../event-grid/compare-messaging-services.md)을 참조 하세요.

이 섹션에서는 [Azure Portal](https://portal.azure.com)에서 이러한 끝점 중 하나를 만드는 방법에 대해 설명 합니다.

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>끝점 만들기 

끝점 리소스를 만든 후에는 Azure Digital Twins 끝점에 사용할 수 있습니다. 새 끝점을 만들려면 [Azure Portal](https://portal.azure.com) 의 인스턴스 페이지로 이동 합니다. 포털 검색 표시줄에 해당 이름을 입력 하 여 인스턴스를 찾을 수 있습니다.

1. 인스턴스 메뉴에서 _끝점_ 을 선택 합니다. 그런 다음 뒤에 오는 *끝점* 페이지에서 *+ 끝점 만들기* 를 선택 합니다. 그러면 다음 단계에서 필드를 채울 *끝점 만들기* 페이지가 열립니다.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Event Grid 형식의 끝점을 만드는 스크린샷" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. 끝점의 **이름을** 입력 하 고 **끝점 형식을** 선택 합니다.

1. 구독 및 [위에](#prerequisite-create-endpoint-resources)설명 된 끝점 리소스를 포함 하 여 끝점 형식에 필요한 기타 세부 정보를 완료 합니다.
    1. 이벤트 허브 및 Service Bus 끝점의 경우 **인증 유형을** 선택 해야 합니다. Azure Digital Twins 인스턴스에 대해 [관리 되는 id](concepts-security.md#managed-identity-for-accessing-other-resources-preview) 를 사용 하 여 끝점을 사용 하려는 경우 미리 만든 권한 부여 규칙 또는 id 기반 인증을 사용 하 여 키 기반 인증을 사용할 수 있습니다. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Event Hub 형식의 끝점을 만드는 스크린샷" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. _저장_ 을 선택 하 여 끝점 만들기를 완료 합니다.

>[!IMPORTANT]
> 끝점에 대해 id 기반 인증을 성공적으로 사용 하려면 [*방법: 라우팅 이벤트에 대해 관리 되는 Id 사용 (미리 보기)*](how-to-enable-managed-identities.md)의 단계를 수행 하 여 인스턴스에 대 한 관리 id를 만들어야 합니다.

끝점을 만든 후 위쪽 Azure Portal 표시줄의 알림 아이콘을 선택 하 여 끝점이 성공적으로 만들어졌는지 확인할 수 있습니다. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="끝점 생성을 확인 하는 알림 스크린샷 포털의 위쪽 표시줄에서 종 모양의 아이콘이 선택 되 고 ' Endpoint ADT-eh-끝점이 성공적으로 만들어짐 ' 이라는 알림이 표시 됩니다.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

끝점을 만들 때 오류가 발생 하면 오류 메시지를 확인 하 고 몇 분 후에 다시 시도 합니다.

또한 Azure Digital Twins 인스턴스의 *끝점* 페이지에서 다시 만들어진 끝점을 볼 수 있습니다.

이제 event grid, event hub 또는 Service Bus 토픽은 끝점에 대해 선택한 이름으로 Azure Digital Twins 내부의 끝점으로 사용할 수 있습니다. 일반적으로 [이 이름은이 문서의 뒷부분에서](#create-an-event-route)만들 **이벤트 경로의** 대상으로 사용 합니다.

### <a name="create-an-endpoint-with-dead-lettering"></a>배달 못 한 문자를 사용 하 여 끝점 만들기

끝점이 특정 기간 내에 이벤트를 전달할 수 없거나 특정 횟수 만큼 이벤트를 배달 하려고 시도한 후에는 배달 되지 않은 이벤트를 저장소 계정으로 보낼 수 있습니다. 이 프로세스를 **배달 못 한 문자** 라고 합니다.

배달 못 한 편지 처리를 사용 하도록 설정 된 끝점을 만들려면 [CLI 명령](how-to-use-cli.md) 또는 [제어 평면 api](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) 를 사용 하 여 Azure Portal 아니라 끝점을 만들어야 합니다.

이러한 도구를 사용 하 여이 작업을 수행 하는 방법에 대 한 지침은이 문서의 [*api 및 CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) 버전을 참조 하세요.

## <a name="create-an-event-route"></a>이벤트 경로 만들기

실제로 Azure Digital Twins에서 끝점으로 데이터를 전송 하려면 **이벤트 경로** 를 정의 해야 합니다. 이러한 경로를 통해 개발자는 시스템 및 다운스트림 서비스 전체에서 이벤트 흐름을 연결할 수 있습니다. 이벤트 경로에 대 한 자세한 내용은 [*Azure Digital Twins 이벤트 라우팅*](concepts-route-events.md)을 참조 하세요.

**필수 조건**: 경로 만들기로 이동 하기 전에이 문서의 앞부분에서 설명한 대로 끝점을 만들어야 합니다. 끝점 설정이 완료 되 면 이벤트 경로를 계속 만들 수 있습니다.

>[!NOTE]
>최근에 끝점을 배포한 경우 새 이벤트 경로에 대 한 사용을 시도 **하기 전에** 배포를 완료 했는지 확인 합니다. 끝점이 준비 되지 않았기 때문에 경로를 설정할 수 없는 경우 몇 분 정도 기다린 후 다시 시도 하세요.

### <a name="creation-steps-with-the-azure-portal"></a>Azure Portal 만들기 단계

이벤트 경로 정의에는 다음 요소가 포함 됩니다.
* 사용 하려는 경로 이름
* 사용 하려는 끝점의 이름입니다.
* 엔드포인트로 전송되는 이벤트를 정의하는 필터
    - 이벤트를 보내지 않도록 경로를 사용 하지 않도록 설정 하려면의 필터 값을 사용 합니다. `false`
    - 특정 필터링이 없는 경로를 사용 하도록 설정 하려면의 필터 값을 사용 합니다. `true`
    - 다른 유형의 필터에 대 한 자세한 내용은 아래의 [*필터 이벤트*](#filter-events) 섹션을 참조 하십시오.

단일 경로를 사용 하 여 여러 알림과 이벤트 유형을 선택할 수 있습니다.

이벤트 경로를 만들려면 [Azure Portal](https://portal.azure.com) 에서 Azure Digital twins 인스턴스에 대 한 세부 정보 페이지로 이동 합니다. 포털 검색 표시줄에 해당 이름을 입력 하 여 인스턴스를 찾을 수 있습니다.

인스턴스 메뉴에서 _이벤트 경로_ 를 선택 합니다. 그런 다음 아래의 *이벤트 경로* 페이지에서 *+ 이벤트 경로 만들기* 를 선택 합니다. 

열리는 *이벤트 경로 만들기* 페이지에서 최소한 다음을 선택 합니다.
* _이름_ 필드에 있는 경로 이름
* 경로를 만드는 데 사용할 _끝점_ 입니다. 

경로를 사용 하려면 최소한의 **이벤트 경로 필터도 추가** 해야 합니다 `true` . 의 기본값을 그대로 두면 `false` 경로가 만들어지지만 이벤트는 전달 되지 않습니다. 이렇게 하려면 _고급 편집기_ 의 스위치를 설정 하 여 사용 하도록 설정 하 고 `true` *필터* 상자에를 작성 합니다.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="인스턴스에 대 한 이벤트 경로를 만드는 스크린샷" lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

완료 되 면 _저장_ 단추를 눌러 이벤트 경로를 만듭니다.

## <a name="filter-events"></a>이벤트 필터링

위에서 설명한 것 처럼 경로에는 **필터** 필드가 있습니다. 경로의 필터 값이 이면 `false` 끝점으로 전송 되는 이벤트가 없습니다. 

최소 필터를 사용 하도록 설정 하 고 나면 `true` 끝점은 Azure Digital Twins에서 다양 한 이벤트를 수신 합니다.
* Azure digital 쌍 서비스 API를 사용 하 여 [디지털](concepts-twins-graph.md) 쌍에서 발생 하는 원격 분석
* 쌍 속성 변경 알림, Azure Digital Twins 인스턴스의 모든 쌍에 대 한 속성 변경 시 발생
* 쌍 또는 관계가 만들어지거나 삭제 될 때 발생 하는 수명 주기 이벤트

보다 구체적인 필터를 정의 하 여 전송 되는 이벤트 유형을 제한할 수 있습니다.

이벤트 경로를 만드는 동안 이벤트 필터를 추가 하려면 *이벤트 경로 만들기* 페이지의 _이벤트 경로 필터 추가_ 섹션을 사용 합니다. 

몇 가지 기본 일반 필터 옵션 중에서 선택 하거나 고급 필터 옵션을 사용 하 여 사용자 지정 필터를 작성할 수 있습니다.

#### <a name="use-the-basic-filters"></a>기본 필터 사용

기본 필터를 사용 하려면 _이벤트 유형_ 옵션을 확장 하 고 끝점에 보내려는 이벤트에 해당 하는 확인란을 선택 합니다. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="기본 필터를 사용 하 여 이벤트 경로를 만드는 스크린샷 이벤트의 확인란을 선택 합니다.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

그러면 선택한 필터 텍스트를 사용 하 여 필터 텍스트 상자가 자동으로 채워집니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="기본 필터를 사용 하 여 이벤트 경로를 만드는 스크린샷 이벤트를 선택한 후 자동으로 채워진 필터 텍스트를 표시 합니다.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>고급 필터 사용

또는 고급 필터 옵션을 사용 하 여 고유한 사용자 지정 필터를 작성할 수 있습니다.

고급 필터 옵션을 사용 하 여 이벤트 경로를 만들려면 _고급 편집기_ 에 대 한 스위치를 설정/해제 하 여 설정 합니다. 그런 다음 *필터* 상자에 사용자 고유의 이벤트 필터를 작성할 수 있습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="고급 필터를 사용 하 여 이벤트 경로를 만드는 스크린샷":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

다음은 지원 되는 경로 필터입니다. *필터 텍스트 스키마* 열의 세부 정보는 필터 상자에 입력할 수 있는 텍스트입니다.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>다음 단계

받을 수 있는 다양 한 이벤트 메시지 유형에 대해 읽어 보세요.
* [*방법: 이벤트 데이터 해석*](how-to-interpret-event-data.md)
