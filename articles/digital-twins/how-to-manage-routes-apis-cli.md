---
title: 엔드포인트 및 경로 관리(API 및 CLI)
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 데이터에 대한 엔드포인트 및 이벤트 경로를 설정하고 관리하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0d7bbade3cdf928459e26c9c1c0269e097983a99
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110473713"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Azure Digital Twins에서 엔드포인트 및 경로 관리(API 및 CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins에서는 [이벤트 알림](concepts-event-notifications.md)을 다운스트림 서비스 또는 연결된 컴퓨팅 리소스로 라우팅할 수 있습니다. 먼저 이벤트를 수신할 수 있는 ‘엔드포인트’를 설정하여 이 작업을 수행합니다. 그런 다음, Azure Digital Twins에서 생성되는 어떤 이벤트를 어느 엔드포인트로 전달할지 지정하는 ‘이벤트 경로’를 만들 수 있습니다.

이 문서에서는 [REST API](/rest/api/azure-digitaltwins/), [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 및 [Azure Digital Twins CLI](concepts-cli.md)를 사용하여 엔드포인트와 경로를 만드는 과정을 안내합니다.

또는 [Azure Portal](https://portal.azure.com)을 사용하여 엔드포인트와 경로를 관리할 수도 있습니다. 포털을 대신 사용하는 이 문서의 버전은 [방법: 엔드포인트 및 경로 관리(포털)](how-to-manage-routes-portal.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [무료로 설정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)할 수 있는 **Azure 계정** 이 필요합니다.
- Azure 구독에는 **Azure Digital Twins 인스턴스** 가 필요합니다. 인스턴스가 아직 없는 경우 [방법: 인스턴스 및 인증 설정](how-to-set-up-instance-cli.md)의 단계를 사용하여 만들 수 있습니다. 이 문서의 뒷부분에서 쉽게 사용할 수 있도록 설정에서 다음 값을 가져옵니다.
    - 인스턴스 이름
    - Resource group

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins 엔드포인트 만들기

다음은 인스턴스에 대해 만들 수 있는 지원되는 엔드포인트 유형입니다.
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

다양한 엔드포인트 유형에 대한 자세한 정보는 [Azure 메시징 서비스 간 선택](../event-grid/compare-messaging-services.md)을 참조하세요.

이 섹션에서는 Azure CLI를 사용하여 이러한 엔드포인트를 만드는 방법을 설명합니다. [DigitalTwinsEndpoint 컨트롤 플레인 API](/rest/api/digital-twins/controlplane/endpoints)를 사용하여 엔드포인트를 관리할 수도 있습니다.

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>엔드포인트 만들기

엔드포인트 리소스를 만든 후에는 Azure Digital Twins 엔드포인트에 사용할 수 있습니다. 다음 예제에서는 [Azure Digital Twins CLI](concepts-cli.md)에 대해 [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 엔드포인트를 만드는 방법을 보여 줍니다. 명령의 자리 표시자를 사용자 고유의 리소스 세부 정보로 바꿉니다.

Event Grid 엔드포인트를 생성하려면 다음을 수행합니다.

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Event Hubs 엔드포인트(키 기반 인증)를 생성하려면 다음을 수행합니다.
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Service Bus 토픽 엔드포인트(키 기반 인증)를 생성하려면 다음을 수행합니다.
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

이러한 명령을 성공적으로 실행한 후에는 이벤트 그리드, 이벤트 허브 또는 Service Bus 토픽을 `--endpoint-name` 인수로 제공한 이름으로 Azure Digital Twins 내부의 엔드포인트로 사용할 수 있습니다. 일반적으로 이 이름은 **이벤트 경로** 의 대상으로 사용하며 [이 문서의 뒷부분](#create-an-event-route)에서 만들 것입니다.

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>ID 기반 인증으로 엔드포인트 만들기

ID 기반 인증을 사용하는 엔드포인트를 만들어 [관리 ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview)가 있는 엔드포인트를 사용할 수도 있습니다. 이 옵션은 Event Hub 및 Service Bus 유형 엔드포인트에만 사용할 수 있습니다(Event Grid에 지원되지 않음).

이 유형의 엔드포인트를 만드는 CLI 명령은 다음과 같습니다. 명령의 자리 표시자에 연결하려면 다음 값이 필요합니다.
* Azure Digital Twins 인스턴스의 Azure 리소스 ID
* 엔드포인트 이름
* 엔드포인트 유형
* 엔드포인트 리소스의 네임스페이스
* 이벤트 허브 또는 Service Bus 토픽의 이름
* Azure Digital Twins 인스턴스의 위치

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>배달 못한 편지를 사용하여 엔드포인트 만들기

엔드포인트가 일정 시간 안에 또는 일정 횟수의 이벤트 배달 시도 후 이벤트를 배달할 수 없는 경우, 미배달 이벤트를 스토리지 계정에 보낼 수 있습니다. 이 프로세스를 **배달 못한 편지** 라고 합니다.

배달 못한 편지를 사용하는 엔드포인트는 Azure Digital Twins [CLI](concepts-cli.md) 또는 [컨트롤 플레인 API](concepts-apis-sdks.md#overview-control-plane-apis)를 사용하여 설정할 수 있습니다.

배달 못한 편지에 대한 자세한 정보는 [개념: 이벤트 경로](concepts-route-events.md#dead-letter-events)를 참조하세요. 배달 못한 편지를 사용하여 엔드포인트를 설정하는 방법에 대한 지침은 이 섹션의 나머지 부분에서 이어집니다.

#### <a name="set-up-storage-resources"></a>스토리지 리소스 설정

배달 못한 편지 위치를 설정하기 전에 Azure 계정에 설정된 [컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)가 있는 [스토리지 계정](../storage/common/storage-account-create.md?tabs=azure-portal)이 있어야 합니다. 

나중에 엔드포인트를 만들 때 이 컨테이너에 대한 URI를 제공합니다. 배달 못한 편지 위치는 [SAS 토큰](../storage/common/storage-sas-overview.md)이 있는 컨테이너 URI로 엔드포인트에 제공됩니다. 이 토큰에는 스토리지 계정 내의 대상 컨테이너에 대한 `write` 권한이 필요합니다. 완전히 구성된 **배달 못한 편지 SAS URI** 의 형식은 다음과 같습니다. `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`

아래 단계에 따라 Azure 계정에서 이러한 스토리지 리소스를 설정하고 다음 섹션에서 엔드포인트 연결 설정을 준비합니다.

1. [스토리지 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)의 단계에 따라 Azure 구독에서 **스토리지 계정** 을 만듭니다. 나중에 사용할 수 있도록 스토리지 계정 이름을 적어 둡니다.
2. [컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)의 단계에 따라 새 스토리지 계정 내에 **컨테이너** 를 만듭니다. 나중에 사용할 수 있도록 컨테이너 이름을 적어 둡니다.
3. 그런 다음, 엔드포인트가 액세스하는 데 사용할 수 있는 스토리지 계정에 대한 **SAS 토큰** 을 만듭니다. [Azure Portal](https://ms.portal.azure.com/#home)에서 스토리지 계정으로 이동하여 시작합니다(포털 검색 창에서 이름으로 찾을 수 있음).
4. 스토리지 계정 페이지에서 왼쪽 탐색 모음의 _공유 액세스 서명_ 링크를 선택하여 SAS 토큰 설정을 시작합니다.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Azure Portal의 스토리지 계정 페이지 스크린샷." lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. *공유 액세스 서명 페이지* 의 *허용되는 서비스* 및 *허용되는 리소스 종류* 에서 원하는 설정을 선택합니다. 각 범주에서 하나 이상의 상자를 선택해야 합니다. *허용되는 권한* 에서 **쓰기** 를 선택합니다(원하는 경우 다른 권한도 선택할 수도 있음).
1. 나머지 설정에 대해 원하는 값을 설정합니다.
1. 완료되면 _SAS 및 연결 문자열 생성_ 단추를 선택하여 SAS 토큰을 생성합니다. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="SAS 토큰을 생성하기 위한 모든 설정 선택을 보여주는 Azure Portal의 스토리지 계정 페이지 스크린샷." lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. 이렇게 하면 동일한 페이지 하단의 설정 선택 아래에 여러 SAS 및 연결 문자열 값이 생성됩니다. 아래로 스크롤하여 해당 값을 확인하고 *클립보드에 복사* 아이콘을 사용하여 **SAS 토큰** 값을 복사합니다. 나중에 사용할 수 있도록 저장합니다.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="배달 못한 편지 비밀에 사용할 SAS 토큰을 복사하는 방법을 강조 표시하는 Azure Portal의 스토리지 계정 페이지 스크린샷." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>배달 못한 편지 엔드포인트 만들기

배달 못한 편지를 사용하는 엔드포인트를 만들려면 [Azure Digital Twins CLI](concepts-cli.md)에 대한 [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 명령에 다음 배달 못한 편지 매개 변수를 추가합니다.

이 매개 변수의 값은 [이전 섹션](#set-up-storage-resources)에서 수집한 스토리지 계정 이름, 컨테이너 이름 및 SAS 토큰으로 구성된 **배달 못한 편지 SAS URI** 입니다. 이 매개 변수는 키 기반 인증을 사용하여 엔드포인트를 만듭니다.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

이 매개 변수를 이전 [엔드포인트 만들기](#create-the-endpoint) 섹션의 엔드포인트 만들기 명령의 끝에 추가하여 배달 못한 편지를 사용하는 원하는 유형의 엔드포인트를 만듭니다.

또는 CLI 대신 [Azure Digital Twins 컨트롤 플레인 API](concepts-apis-sdks.md#overview-control-plane-apis)를 사용하여 배달 못한 편지 엔드포인트를 만들 수 있습니다. 이렇게 하려면 [DigitalTwinsEndpoint 설명서](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)를 참조하여 요청을 구조화하고 배달 못한 편지 매개 변수를 추가하는 방법을 확인합니다.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>ID 기반 인증을 사용하여 배달 못한 편지 엔드포인트 만들기

또한 ID 기반 인증을 사용하는 배달 못한 편지 엔드포인트를 만들어 [관리 ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview)와 함께 엔드포인트를 사용할 수도 있습니다. 이 옵션은 Event Hub 및 Service Bus 유형 엔드포인트에만 사용할 수 있습니다(Event Grid에 지원되지 않음).

이 엔드포인트 유형을 만들려면 이전과 동일한 CLI 명령을 사용하여 [ID 기반 인증을 사용하여 엔드포인트를 만들고](#create-an-endpoint-with-identity-based-authentication) `deadLetterUri`에 대한 JSON 페이로드의 추가 필드를 사용합니다.

다음은 명령의 자리 표시자에 연결하는 데 필요한 값입니다.
* Azure Digital Twins 인스턴스의 Azure 리소스 ID
* 엔드포인트 이름
* 엔드포인트 유형
* 엔드포인트 리소스의 네임스페이스
* 이벤트 허브 또는 Service Bus 토픽의 이름
* **배달 못한 편지 SAS URI** 세부 정보: 스토리지 계정 이름, 컨테이너 이름
* Azure Digital Twins 인스턴스의 위치

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>메시지 스토리지 스키마

배달 못한 편지를 사용하는 엔드포인트가 설정되면 배달 못한 편지 메시지가 다음 형식으로 스토리지 계정에 저장됩니다.

`<container>/<endpoint-name>/<year>/<month>/<day>/<hour>/<event-ID>.json`

배달 못한 편지 메시지는 원래 엔드포인트로 전달되도록 의도된 원래 이벤트의 스키마와 일치합니다.

다음은 [트윈 생성 알림](concepts-event-notifications.md#digital-twin-lifecycle-notifications)에 대한 배달 못한 편지 메시지의 예제입니다.

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>이벤트 경로 만들기

**필수 조건**: 경로 만들기를 진행하려면 먼저 이 문서의 앞부분에서 설명한 대로 엔드포인트를 만들어야 합니다. 엔드포인트 설정이 완료되면 이벤트 경로 만들기를 진행할 수 있습니다.

> [!NOTE]
> 최근에 엔드포인트를 배포한 경우 새 이벤트 경로에 해당 엔드포인트를 사용하기 **전에** 배포가 완료되었는지 확인합니다. 엔드포인트가 준비되지 않아 경로 배포가 실패하면 몇 분 정도 기다렸다가 다시 시도합니다.
>
> 이 흐름을 스크립팅하는 경우 경로 설정을 진행하기 전에 엔드포인트 서비스가 배포를 완료할 때까지 2-3분 동안 대기 시간을 구축하여 이 문제를 해결할 수 있습니다.

실제로 Azure Digital Twins에서 엔드포인트로 데이터를 보내려면 **이벤트 경로** 를 정의해야 합니다. 이벤트 경로는 시스템 전체 및 다운스트림 서비스에 이벤트 흐름을 연결하는 데 사용됩니다.

경로 정의에는 다음 요소가 포함될 수 있습니다.
* 사용하려는 경로 이름
* 사용하려는 엔드포인트 이름
* 엔드포인트로 전송되는 이벤트를 정의하는 필터 

경로 이름이 없으면 Azure Digital Twins 외부에서 메시지가 라우팅되지 않습니다. 경로 이름이 있고 필터가 `true`이면 모든 메시지가 엔드포인트로 라우팅됩니다. 경로 이름이 있고 다른 필터가 추가된 경우 메시지가 해당 필터를 기반으로 필터링됩니다.

하나의 경로에서 여러 알림 및 이벤트 유형을 선택할 수 있어야 합니다. 

이벤트 경로는 Azure Digital Twins [EventRoutes 데이터 평면 API](/rest/api/digital-twins/dataplane/eventroutes) 또는 [az dt route CLI 명령](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true)을 사용하여 만들 수 있습니다. 이 섹션의 나머지 부분에서 만들기 프로세스를 안내합니다.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>API 및 C# SDK를 사용하여 경로 만들기

이벤트 경로를 정의하는 한 가지 방법은 [데이터 평면 API](concepts-apis-sdks.md#overview-data-plane-apis)를 사용하는 것입니다. 이 섹션의 샘플에서는 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)를 사용합니다.

`CreateOrReplaceEventRouteAsync`는 이벤트 경로를 추가하는 데 사용되는 SDK 호출입니다. 사용 예제는 다음과 같습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> 모든 SDK 함수는 동기 및 비동기 버전으로 제공됩니다.

#### <a name="event-route-sample-sdk-code"></a>이벤트 경로 샘플 SDK 코드

다음 샘플 메서드는 C# SDK를 사용하여 이벤트 경로를 만들고, 나열하고, 삭제하는 방법을 보여 줍니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>CLI를 사용하여 경로 만들기

Azure Digital Twins CLI에 대한 [az dt route](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 경로를 관리할 수도 있습니다. 

CLI 사용 및 사용 가능한 명령에 대한 자세한 내용은 [개념: Azure Digital Twins CLI 명령 집합](concepts-cli.md)을 참조하세요.

## <a name="filter-events"></a>이벤트 필터링

필터링이 없이 엔드포인트는 Azure Digital Twins에서 다양한 이벤트를 수신합니다.
* Azure Digital Twins 서비스 API를 사용하여 [디지털 트윈](concepts-twins-graph.md)에서 발생된 원격 분석
* Azure Digital Twins 인스턴스의 모든 트윈에 대한 속성 변경 시 발생하는 트윈 속성 변경 알림
* 트윈 또는 관계가 생성되거나 삭제될 때 발생하는 수명 주기 이벤트

이벤트 경로에 엔드포인트에 대한 **필터** 를 추가하여 전송되는 이벤트를 제한할 수 있습니다.

>[!NOTE]
> 필터는 **대/소문자를 구분** 하며 페이로드 대/소문자와 일치해야 합니다. 
>
> 즉, 원격 분석 필터라면 트윈의 모델에 정의된 대/소문자와는 반드시 일치할 필요는 없고, 디바이스가 보낸 원격 분석의 대/소문자와 일치해야 합니다. 

필터를 추가하려면 다음 본문에서 `https://<Your-Azure-Digital-Twins-host-name>/eventRoutes/<event-route-name>?api-version=2020-10-31`에 대한 PUT 요청을 사용하면 됩니다.

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

다음은 지원되는 경로 필터입니다. *필터 텍스트 스키마* 열의 세부 정보를 사용하여 위의 요청 본문에서 `<filter-text>` 자리 표시자를 바꿉니다.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>다음 단계

받을 수 있는 다양한 이벤트 메시지 유형에 대해 읽어 보세요.
* [개념: 이벤트 알림](concepts-event-notifications.md)
