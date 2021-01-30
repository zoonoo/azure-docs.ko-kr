---
title: 끝점 및 경로 관리 (Api 및 CLI)
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 데이터에 대 한 끝점 및 이벤트 경로를 설정 하 고 관리 하는 방법을 참조 하세요.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d25a429873ccf8b546c0919456c97e64445f184c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071701"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Azure Digital Twins (Api 및 CLI)에서 끝점 및 경로 관리

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins에서 [이벤트 알림을](how-to-interpret-event-data.md) 다운스트림 서비스 또는 연결 된 계산 리소스로 라우팅할 수 있습니다. 먼저 이벤트를 수신할 수 있는 ‘엔드포인트’를 설정하여 이 작업을 수행합니다. 그런 다음 Azure Digital Twins에 의해 생성 되는 이벤트를 지정 하는  [**이벤트 경로**](concepts-route-events.md) 를 끝점으로 전달할 수 있습니다.

이 문서에서는 [REST api](/rest/api/azure-digitaltwins/), [.net (c #) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)및 [Azure Digital twins CLI](how-to-use-cli.md)를 사용 하 여 끝점과 경로를 만드는 과정을 안내 합니다.

또는 [Azure Portal](https://portal.azure.com)를 사용 하 여 끝점과 경로를 관리할 수도 있습니다. 포털을 대신 사용 하는이 문서의 버전에 대해서는 [*방법: 끝점 및 경로 관리 (포털)*](how-to-manage-routes-portal.md)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

- **Azure 계정이** 필요 합니다 ( [여기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)에서 무료로 설정할 수 있음).
- Azure 구독에는 **Azure Digital Twins 인스턴스가** 필요 합니다. 인스턴스가 아직 없는 경우 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-cli.md)의 단계를 사용 하 여 인스턴스를 만들 수 있습니다. 이 문서의 뒷부분에서 사용할 수 있도록 다음 값을 설정 하는 것이 유용 합니다.
    - 인스턴스 이름
    - Resource group

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins에 대 한 끝점 만들기

다음은 인스턴스에 대해 만들 수 있는 지원 되는 끝점 유형입니다.
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

다른 끝점 형식에 대 한 자세한 내용은 [*Azure 메시징 서비스 중에서 선택*](../event-grid/compare-messaging-services.md)을 참조 하세요.

이 섹션에서는 Azure CLI를 사용 하 여 이러한 끝점을 만드는 방법을 설명 합니다. [DigitalTwinsEndpoint 제어 평면 api](/rest/api/digital-twins/controlplane/endpoints)를 사용 하 여 끝점을 관리할 수도 있습니다.

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>끝점 만들기

끝점 리소스를 만든 후에는 Azure Digital Twins 끝점에 사용할 수 있습니다. 다음 예제에서는 [Azure Digital Twins CLI](how-to-use-cli.md)에 대해 [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 명령을 사용 하 여 끝점을 만드는 방법을 보여 줍니다. 명령의 자리 표시자를 사용자 고유의 리소스의 세부 정보로 바꿉니다.

Event Grid 끝점을 만들려면:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Event Hubs 끝점을 만들려면 (키 기반 인증):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Service Bus 토픽 끝점 (키 기반 인증)을 만들려면 다음을 수행 합니다.
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

이러한 명령을 성공적으로 실행 한 후에는 인수를 사용 하 여 제공한 이름에 따라 event grid, event hub 또는 Service Bus 항목을 Azure Digital Twins 내부의 끝점으로 사용할 수 있습니다 `--endpoint-name` . 일반적으로 [이 이름은이 문서의 뒷부분에서](#create-an-event-route)만들 **이벤트 경로의** 대상으로 사용 합니다.

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Id 기반 인증을 사용 하 여 끝점 만들기

Id 기반 인증을 사용 하는 끝점을 만들어 [관리 id](concepts-security.md#managed-identity-for-accessing-other-resources-preview)가 있는 끝점을 사용할 수도 있습니다. 이 옵션은 이벤트 허브 및 Service Bus 형식 끝점에 대해서만 사용할 수 있습니다 (Event Grid에는 지원 되지 않음).

이 유형의 끝점을 만드는 CLI 명령은 다음과 같습니다. 명령에서 자리 표시자에 연결 하려면 다음 값이 필요 합니다.
* Azure Digital Twins 인스턴스의 Azure 리소스 ID
* 끝점 이름
* 끝점 유형
* 끝점 리소스의 네임 스페이스입니다.
* 이벤트 허브 또는 Service Bus 항목의 이름
* Azure Digital Twins 인스턴스의 위치

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>배달 못 한 문자를 사용 하 여 끝점 만들기

끝점이 특정 기간 내에 이벤트를 전달할 수 없거나 특정 횟수 만큼 이벤트를 배달 하려고 시도한 후에는 배달 되지 않은 이벤트를 저장소 계정으로 보낼 수 있습니다. 이 프로세스를 **배달 못 한 문자** 라고 합니다.

배달 못 한 편지 처리를 사용 하는 끝점은 Azure Digital Twins [CLI](how-to-use-cli.md) 또는 [제어 평면 api](how-to-use-apis-sdks.md#overview-control-plane-apis)를 사용 하 여 설정할 수 있습니다.

배달 못 한 편지에 대해 자세히 알아보려면 [*개념: 이벤트 경로*](concepts-route-events.md#dead-letter-events)를 참조 하세요. 배달 못 한 문자를 사용 하 여 끝점을 설정 하는 방법에 대 한 지침은이 섹션의 나머지 부분을 계속 진행 합니다.

#### <a name="set-up-storage-resources"></a>저장소 리소스 설정

배달 못 한 편지 위치를 설정 하기 전에 Azure 계정에 [컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 를 설정 하는 [저장소 계정이](../storage/common/storage-account-create.md?tabs=azure-portal) 있어야 합니다. 

나중에 끝점을 만들 때이 컨테이너에 대 한 URI를 제공 합니다. 배달 못 한 편지 위치는 [SAS 토큰](../storage/common/storage-sas-overview.md)을 사용 하 여 컨테이너 URI로 끝점에 제공 됩니다. 해당 토큰에는 `write` 저장소 계정 내의 대상 컨테이너에 대 한 권한이 필요 합니다. 완전히 구성 된 **배달 못 한 편지 SAS URI** 는: 형식입니다 `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` .

다음 섹션에서 끝점 연결 설정 준비를 위해 다음 단계에 따라 Azure 계정에 이러한 저장소 리소스를 설정 합니다.

1. [*저장소 계정 만들기*](../storage/common/storage-account-create.md?tabs=azure-portal) 의 단계에 따라 Azure 구독에서 **저장소 계정을** 만듭니다. 나중에 사용할 수 있도록 저장소 계정 이름을 적어 둡니다.
2. [*컨테이너 만들기*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 의 단계에 따라 새 저장소 계정 내에 **컨테이너** 를 만듭니다. 나중에 사용할 수 있도록 컨테이너 이름을 적어 둡니다.
3. 그런 다음 끝점에서 액세스할 때 사용할 수 있는 저장소 계정에 대 한 **SAS 토큰** 을 만듭니다. [Azure Portal](https://ms.portal.azure.com/#home) 에서 저장소 계정으로 이동 하 여 시작 합니다 (포털 검색 표시줄에서 이름으로 찾을 수 있음).
4. 저장소 계정 페이지의 왼쪽 탐색 모음에서 _공유 액세스 서명_ 링크를 선택 하 여 SAS 토큰 설정을 시작 합니다.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Azure Portal의 저장소 계정 페이지" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. *공유 액세스 서명 페이지* 의 *허용 되는 서비스* 및 *허용 되는 리소스 종류* 아래에서 원하는 설정을 선택 합니다. 각 범주에서 하나 이상의 상자를 선택 해야 합니다. *허용 된 권한* 에서 **쓰기** 를 선택 합니다. 원하는 경우 기타 사용 권한을 선택할 수도 있습니다.
1. 나머지 설정에 대해 원하는 값을 설정 합니다.
1. 완료 되 면 _sas 및 연결 문자열 생성_ 단추를 선택 하 여 sas 토큰을 생성 합니다. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="SAS 토큰을 생성 하 고 ' SAS 및 연결 문자열 생성 ' 단추를 강조 표시 하는 모든 설정 선택을 보여 주는 Azure Portal의 저장소 계정 페이지" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. 그러면 동일한 페이지의 아래쪽에 있는 설정 선택 항목 아래에 몇 개의 SAS 및 연결 문자열 값이 생성 됩니다. 아래로 스크롤하여 값을 확인 하 고 *클립보드로 복사* 아이콘을 사용 하 여 **SAS 토큰** 값을 복사 합니다. 나중에 사용할 수 있도록 저장 합니다.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="배달 못 한 문자 암호에 사용할 SAS 토큰을 복사 합니다." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>배달 못 한 편지 엔드포인트 만들기

배달 못 한 편지 처리를 사용 하는 끝점을 만들려면 [Azure Digital Twins CLI](how-to-use-cli.md)에 대해 [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 명령에 다음 배달 못한 편지 매개 변수를 추가 합니다.

매개 변수의 값은 [이전 섹션](#set-up-storage-resources)에서 수집한 저장소 계정 이름, 컨테이너 이름 및 SAS 토큰으로 구성 된 **배달 못 한 편지 sas URI** 입니다. 이 매개 변수는 키 기반 인증을 사용 하 여 끝점을 만듭니다.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

이전에 [*끝점 만들기*](#create-the-endpoint) 섹션에서 끝점 생성 명령의 끝에이 매개 변수를 추가 하 여 배달 못 한 편지 처리를 사용 하도록 설정 된 원하는 형식의 끝점을 만듭니다.

또는 CLI 대신 [Azure 디지털 Twins 제어 평면 api](how-to-use-apis-sdks.md#overview-control-plane-apis) 를 사용 하 여 배달 못 한 편지 끝점을 만들 수 있습니다. 이렇게 하려면 [DigitalTwinsEndpoint 설명서](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) 를 참조 하 여 요청을 구조화 하 고 배달 못 한 편지 매개 변수를 추가 하는 방법을 확인 합니다.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Id 기반 인증을 사용 하 여 배달 못 한 편지 끝점 만들기

Id 기반 인증을 사용 하는 배달 못 한 편지 처리 끝점을 만들어 [관리 id](concepts-security.md#managed-identity-for-accessing-other-resources-preview)와 함께 끝점을 사용할 수도 있습니다. 이 옵션은 이벤트 허브 및 Service Bus 형식 끝점에 대해서만 사용할 수 있습니다 (Event Grid에는 지원 되지 않음).

이러한 유형의 끝점을 만들려면 앞에서 설명한 것과 동일한 CLI 명령을 사용 하 여에 대 한 JSON 페이로드의 추가 필드가 있는 [id 기반 인증을 사용 하 여 끝점](#create-an-endpoint-with-identity-based-authentication)을 만듭니다 `deadLetterUri` .

명령에서 자리 표시자에 연결 하는 데 필요한 값은 다음과 같습니다.
* Azure Digital Twins 인스턴스의 Azure 리소스 ID
* 끝점 이름
* 끝점 유형
* 끝점 리소스의 네임 스페이스입니다.
* 이벤트 허브 또는 Service Bus 항목의 이름
* **배달 못한 편지 SAS URI** 세부 정보: storage 계정 이름, 컨테이너 이름
* Azure Digital Twins 인스턴스의 위치

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>메시지 저장소 스키마

배달 못 한 편지의 끝점이 설정 되 면 배달 못 한 메시지는 저장소 계정에 다음 형식으로 저장 됩니다.

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

배달 못 한 편지 메시지는 원래 끝점에 전달 하기 위한 원래 이벤트의 스키마와 일치 합니다.

다음은 쌍 [만들기 알림에](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications)대 한 배달 못 한 편지 메시지의 예입니다.

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>이벤트 경로 만들기

**필수 조건**: 경로 만들기로 이동 하기 전에이 문서의 앞부분에서 설명한 대로 끝점을 만들어야 합니다. 끝점 설정이 완료 되 면 이벤트 경로를 계속 만들 수 있습니다.

> [!NOTE]
> 최근에 끝점을 배포한 경우 새 이벤트 경로에 대 한 사용을 시도 **하기 전에** 배포를 완료 했는지 확인 합니다. 끝점이 준비 되지 않았기 때문에 경로 배포가 실패 하면 몇 분 정도 기다렸다가 다시 시도 하세요.
>
> 이 흐름을 스크립팅 하는 경우 경로 설정으로 이동 하기 전에 끝점 서비스의 배포를 완료 하는 데 2-3 분의 대기 시간으로 구축 하 여이를 고려해 볼 수 있습니다.

실제로 Azure Digital Twins에서 끝점으로 데이터를 전송 하려면 **이벤트 경로** 를 정의 해야 합니다. 이벤트 경로는 시스템 및 다운스트림 서비스에서 이벤트 흐름을 연결 하는 데 사용 됩니다.

경로 정의에는 다음 요소가 포함 될 수 있습니다.
* 사용 하려는 경로 이름
* 사용 하려는 끝점의 이름입니다.
* 엔드포인트로 전송되는 이벤트를 정의하는 필터 

경로 이름이 없으면 메시지가 Azure 디지털 쌍 외부에서 라우팅되지 않습니다. 경로 이름이 있고 필터가 이면 `true` 모든 메시지가 끝점으로 라우팅됩니다. 경로 이름이 있고 다른 필터가 추가 된 경우 필터를 기반으로 메시지가 필터링 됩니다.

하나의 경로에서 여러 알림과 이벤트 유형을 선택할 수 있어야 합니다. 

이벤트 경로는 Azure Digital Twins [ **eventroutes** 데이터 평면 api](/rest/api/digital-twins/dataplane/eventroutes) 또는 [ **az dt route** CLI 명령을](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true)사용 하 여 만들 수 있습니다. 이 섹션의 나머지 부분에서는 만들기 프로세스를 안내 합니다.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Api 및 c # SDK를 사용 하 여 경로 만들기

이벤트 경로를 정의 하는 한 가지 방법은 [데이터 평면 api](how-to-use-apis-sdks.md#overview-data-plane-apis)를 사용 하는 것입니다. 이 단원의 샘플에서는 [.net (c #) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)를 사용 합니다.

`CreateOrReplaceEventRouteAsync` 는 이벤트 경로를 추가 하는 데 사용 되는 SDK 호출입니다. 사용법의 예는 다음과 같습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> 모든 SDK 함수는 동기 및 비동기 버전으로 제공 됩니다.

#### <a name="event-route-sample-sdk-code"></a>이벤트 경로 샘플 SDK 코드

다음 샘플 메서드는 c # SDK를 사용 하 여 이벤트 경로를 만들고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>CLI를 사용 하 여 경로 만들기

Azure Digital Twins CLI에 대해 [az dt route](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true) 명령을 사용 하 여 경로를 관리할 수도 있습니다. 

CLI 사용 방법과 사용할 수 있는 명령에 대 한 자세한 내용은 [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)을 참조 하세요.

## <a name="filter-events"></a>이벤트 필터링

필터링이 없으면 끝점은 Azure Digital Twins에서 다양 한 이벤트를 수신 합니다.
* Azure digital 쌍 서비스 API를 사용 하 여 [디지털](concepts-twins-graph.md) 쌍에서 발생 하는 원격 분석
* 쌍 속성 변경 알림, Azure Digital Twins 인스턴스의 모든 쌍에 대 한 속성 변경 시 발생
* 쌍 또는 관계가 만들어지거나 삭제 될 때 발생 하는 수명 주기 이벤트

이벤트 경로에 끝점에 대 한 **필터** 를 추가 하 여 전송 되는 이벤트를 제한할 수 있습니다.

필터를 추가 하려면 다음 본문을 사용 하 여 PUT 요청을 *https:///{azure---------//eventroutes/{event route-name}? api-version = 2020-10-31* 로 사용할 수 있습니다.

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

다음은 지원 되는 경로 필터입니다. *텍스트 스키마 필터* 열의 세부 정보를 사용 하 여 `<filter-text>` 위의 요청 본문에서 자리 표시자를 바꿉니다.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>다음 단계

받을 수 있는 다양 한 이벤트 메시지 유형에 대해 읽어 보세요.
* [*방법: 이벤트 데이터 해석*](how-to-interpret-event-data.md)
