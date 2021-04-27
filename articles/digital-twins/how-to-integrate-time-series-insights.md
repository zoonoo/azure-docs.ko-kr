---
title: Azure Time Series Insights와 통합
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에서 Azure Time Series Insights로 이벤트 경로를 설정하는 방법을 참조하세요.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 608f883304dbc8e1ea8b0127668125ae50ca0b11
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564945"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure Digital Twins를 Azure Time Series Insights와 통합

이 문서에서는 Azure Digital Twins를 [Azure TSI(Time Series Insights)](../time-series-insights/overview-what-is-tsi.md)와 통합하는 방법을 알아봅니다.

이 문서에서 설명하는 솔루션을 통해 IoT 솔루션에 대한 기록 데이터를 수집하고 분석할 수 있습니다. Azure Digital Twins는 여러 데이터 스트림의 상관 관계를 발견하고 정보를 Time Series Insights로 전송하기 전에 표준화할 수 있으므로 Time Series Insights에 데이터를 공급하는 좋은 방법이 됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

Time Series Insights와의 관계를 설정하려면 먼저 **Azure Digital Twins 인스턴스** 가 있어야 합니다. Time Series Insights에서 추적되는 데이터를 확인하기 위해 트윈 정보를 몇 번 업데이트해야 하므로 이 인스턴스는 데이터를 기반으로 디지털 트윈 정보를 업데이트하는 기능을 사용하여 설정해야 합니다. 

이 설정이 아직 없는 경우 Azure Digital Twins [*자습서: 엔드투엔드 솔루션 연결*](./tutorial-end-to-end.md)에 따라 만들 수 있습니다. 이 자습서에서는 디지털 트윈 업데이트를 트리거하는 가상 IoT 디바이스에서 작동하는 Azure Digital Twins 인스턴스를 설정하는 과정을 안내합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

아래 경로를 통해 Azure Digital Twins에 Time Series Insights를 연결합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="엔드투엔드 시나리오에서 Azure 서비스 보기, Time Series Insights 강조 표시" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>트윈 업데이트 알림을 위한 경로 및 필터 만들기

Azure Digital Twins 인스턴스는 트윈의 상태가 업데이트될 때마다 [트윈 업데이트 이벤트](how-to-interpret-event-data.md)를 내보낼 수 있습니다. 이 섹션에서는 추가 처리를 위해 이러한 업데이트 이벤트를 Azure [Event Hubs](../event-hubs/event-hubs-about.md)로 이동하는 Azure Digital Twins [**이벤트 경로**](concepts-route-events.md)를 만듭니다.

Azure Digital Twins [*자습서: 엔드투엔드 솔루션 연결*](./tutorial-end-to-end.md)은 온도계를 사용하여 방을 나타내는 디지털 트윈의 온도 특성을 업데이트하는 시나리오를 안내합니다. 이 패턴은 IoT 디바이스에서 원격 분석을 전달하는 대신 트윈 업데이트를 사용하므로 Time Series Insights 논리를 업데이트할 필요 없이 기본 데이터 원본을 변경할 수 있는 유연성을 제공합니다.

1. 먼저, Azure Digital Twins 인스턴스에서 이벤트를 수신하는 이벤트 허브 네임스페이스를 만듭니다. 아래 Azure CLI 지침을 사용하거나 Azure Portal: [*빠른 시작: Azure Portal을 사용하여 이벤트 허브 만들기*](../event-hubs/event-hubs-create.md)를 사용할 수 있습니다. Event Hubs를 지원하는 지역을 확인하려면 [*지역별 사용 가능한 Azure 제품*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)을 참조하세요.

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. 트윈 변경 이벤트를 수신하는 네임스페이스 내에 이벤트 허브를 만듭니다. 이벤트 허브의 이름을 지정합니다.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. 보내기 및 받기 권한을 사용하여 [권한 부여 규칙](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create)을 만듭니다. 규칙의 이름을 지정합니다.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Azure Digital Twins 인스턴스에 이벤트 허브를 연결하는 Azure Digital Twins [엔드포인트](concepts-route-events.md#create-an-endpoint)를 만듭니다.

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. Azure Digital Twins에서 엔드포인트에 트윈 업데이트 이벤트를 전송하는 [경로](concepts-route-events.md#create-an-event-route)를 만듭니다. 이 경로의 필터는 트윈 업데이트 메시지만 엔드포인트에 전달되도록 허용합니다.

    >[!NOTE]
    >Cloud Shell에는 `az dt route`, `az dt model`, `az dt twin` 명령 그룹에 영향을 주는 **알려진 문제** 가 있습니다.
    >
    >이 문제를 해결하려면 명령을 실행하기 전에 Cloud Shell에서 `az login`을 실행하거나 Cloud Shell 대신 [로컬 CLI](/cli/azure/install-azure-cli)를 사용합니다. 이에 대한 자세한 내용은 [*문제 해결: Azure Digital Twins의 알려진 문제*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)를 참조하세요.

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

계속하기 전에 *Event Hubs 네임스페이스* 및 *리소스 그룹* 을 기록해 둡니다. 나중에 이 문서의 뒷부분에서 다시 사용하여 다른 이벤트 허브를 만듭니다.

## <a name="create-a-function-in-azure"></a>Azure에서 함수 만들기

다음으로, Azure Functions를 사용하여 함수 앱 내부에 **Event Hubs에서 트리거되는 함수** 를 만듭니다. 엔드투엔드 자습서([*자습서: 엔드투엔드 솔루션 연결*](./tutorial-end-to-end.md))에서 만든 함수 앱 또는 자체 앱을 사용할 수 있습니다. 

이 함수는 이러한 트윈 업데이트 이벤트를 JSON 패치 문서인 원래 형태에서 트윈의 업데이트된 값 및 추가된 값만 포함하는 JSON 개체로 변환합니다.

Azure Functions와 함께 Event Hubs를 사용하는 방법에 대한 자세한 내용은 [*Azure Functions에 대한 Azure Event Hubs 트리거*](../azure-functions/functions-bindings-event-hubs-trigger.md)를 참조하세요.

게시된 함수 앱 내에서 다음 코드를 사용하여 **ProcessDTUpdatetoTSI** 라는 새 함수를 추가합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>`dotnet add package` 명령 또는 Visual Studio NuGet 패키지 관리자를 사용하여 프로젝트에 패키지를 추가해야 할 수도 있습니다.

다음으로 새 Azure 함수를 **게시** 합니다. 이 작업을 수행하는 방법에 대한 지침은 [*방법: 데이터 처리를 위한 Azure 함수 설정*](how-to-create-azure-function.md#publish-the-function-app-to-azure)을 참조하세요.

앞서 살펴보면 이 함수는 생성하는 JSON 개체를 두 번째 이벤트 허브로 전송하여 Time Series Insights에 연결합니다. 다음 섹션에서 해당 이벤트 허브를 만듭니다.

나중에 이 함수가 고유한 이벤트 허브에 연결하는 데 사용할 환경 변수를 설정할 수도 있습니다.

## <a name="send-telemetry-to-an-event-hub"></a>이벤트 허브에 원격 분석 전송

이제 두 번째 이벤트 허브를 만들고 해당 출력을 이벤트 허브에 스트리밍하도록 함수를 구성합니다. 그러면 이 이벤트 허브가 Time Series Insights에 연결됩니다.

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

두 번째 이벤트 허브를 만들려면 아래 Azure CLI 지침을 사용하거나 Azure Portal: [*빠른 시작: Azure Portal을 사용하여 이벤트 허브 만들기*](../event-hubs/event-hubs-create.md)를 사용할 수 있습니다.

1. 이 문서의 앞부분에서 *Event Hubs 네임스페이스* 및 *리소스 그룹* 을 준비합니다.

2. 새 이벤트 허브를 만듭니다. 이벤트 허브의 이름을 지정합니다.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. 보내기 및 받기 권한을 사용하여 [권한 부여 규칙](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create)을 만듭니다. 규칙의 이름을 지정합니다.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>함수 구성

다음으로, 이전의 함수 앱에서 만든 이벤트 허브에 대한 연결 문자열을 포함하여 환경 변수를 설정해야 합니다.

### <a name="set-the-twins-event-hub-connection-string"></a>Twins 이벤트 허브 연결 문자열 설정

1. Twins 허브에 관해 위에서 만든 권한 부여 규칙을 사용하여 Twins [이벤트 허브 연결 문자열](../event-hubs/event-hubs-get-connection-string.md)을 가져옵니다.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. 결과로 얻은 *primaryConnectionString* 값을 사용하여 함수 앱에서 해당 연결 문자열을 포함하는 앱 설정을 만듭니다.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Time Series Insights 이벤트 허브 연결 문자열 설정

1. Time Series Insights 허브에 관해 위에서 만든 권한 부여 규칙을 사용하여 TSI [이벤트 허브 연결 문자열](../event-hubs/event-hubs-get-connection-string.md)을 가져옵니다.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. 결과로 얻은 *primaryConnectionString* 값을 사용하여 함수 앱에서 해당 연결 문자열을 포함하는 앱 설정을 만듭니다.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights 인스턴스 만들기 및 연결

다음으로 두 번째 (TSI) 이벤트 허브에서 데이터를 받도록 Time Series Insights 인스턴스를 설정합니다. 아래 단계를 수행합니다. 이 프로세스에 대한 자세한 내용은 [*자습서: Azure Time Series Insights Gen2 PAYG 환경 설정*](../time-series-insights/tutorial-set-up-environment.md)을 참조하세요.

1. Azure Portal에서 Time Series Insights 환경 만들기를 시작합니다. 
    1. **Gen2(L1)** 가격 책정 계층을 선택합니다.
    2. 이 환경에 대한 **시계열 ID** 를 선택해야 합니다. 시계열 ID는 Time Series Insights에서 데이터를 검색하는 데 사용할 최대 3개의 값이 될 수 있습니다. 이 자습서에서는 **$dtId** 를 사용할 수 있습니다. 시계열 ID 선택에 대한 자세한 내용은 [*시계열 ID 선택 모범 사례*](../time-series-insights/how-to-select-tsid.md)를 참조하세요.
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Time Series Insights 환경의 생성 포털 UX입니다. 해당 드롭다운에서 구독, 리소스 그룹 및 위치를 선택하고 사용자 환경의 이름을 선택합니다." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Time Series Insights 환경의 생성 포털 UX입니다. Gen2(L1) 가격 책정 계층이 선택되고 시계열 ID 속성 이름은 $dtId입니다." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. **다음: 이벤트 원본** 을 선택하고 이전의 TSI 이벤트 허브 정보를 선택합니다. 새 Event Hubs 소비자 그룹도 만들어야 합니다.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Time Series Insights 환경 이벤트 원본에 대한 생성 포털 UX입니다. 위의 이벤트 허브 정보를 사용하여 이벤트 원본을 만듭니다. 또한 새 소비자 그룹도 만듭니다." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>IoT 데이터를 Azure Digital Twins로 보내기 시작

Time Series Insights로 데이터 보내기를 시작하려면 Azure Digital Twins에서 변경 데이터 값을 사용하여 디지털 트윈 속성 업데이트를 시작해야 합니다. [az dt twin update](/cli/azure/ext/azure-iot/dt/twin#ext-azure-iot-az-dt-twin-update) 명령을 사용합니다.

환경 설정을 위해 엔드투엔드 자습서([*자습서: 엔드투엔드 솔루션 연결*](tutorial-end-to-end.md))를 사용하는 경우 샘플에서 *DeviceSimulator* 프로젝트를 실행하여 시뮬레이트된 IoT 데이터 보내기를 시작할 수 있습니다. 지침은 자습서의 [*시뮬레이션 구성 및 실행*](tutorial-end-to-end.md#configure-and-run-the-simulation) 섹션에 있습니다.

## <a name="visualize-your-data-in-time-series-insights"></a>Time Series Insights에서 데이터 시각화

이제 데이터가 Time Series Insights 인스턴스로 이동하여 분석할 준비가 되어야 합니다. 다음 단계에 따라 들어오는 데이터를 탐색합니다.

1. [Azure Portal](https://portal.azure.com)에서 Time Series Insights 환경을 엽니다. 포털 검색 표시줄에서 사용자 환경의 이름을 검색할 수 있습니다. 인스턴스 개요에 표시된 Time Series Insights 탐색기 URL을 방문합니다.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Time Series Insights 환경의 개요 탭에서 Time Series Insights 탐색기 URL을 선택합니다.":::

2. 탐색기의 왼쪽에 Azure Digital Twins의 트윈 3개가 표시됩니다. _**thermostat67**_ 을 선택하고 **temperature** 를 선택하고 **추가** 를 누릅니다.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="**thermostat67**을 선택하고 **temperature**를 선택하고 **추가**를 누릅니다.":::

3. 이제 아래와 같이 자동 온도 조절기의 초기 온도 판독값이 표시됩니다. *room21* 및 *floor1* 에 대한 동일한 온도 판독값이 업데이트되며 이러한 데이터 스트림을 동시에 시각화할 수 있습니다.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="초기 온도 데이터는 TSI 탐색기에서 그래프로 표현됩니다. 68과 85 사이의 임의 값으로 표시되는 선입니다.":::

4. 시뮬레이션을 훨씬 더 오랫동안 실행하도록 허용하는 경우 시각화는 다음과 같습니다.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="각 트윈에 대한 온도 데이터가 서로 다른 색상의 세 평행선으로 그래프에 표현됩니다.":::

## <a name="next-steps"></a>다음 단계

디지털 트윈은 기본적으로 Time Series Insights의 플랫 계층으로 저장되지만 모델 정보와 조직의 다단계 계층 구조를 사용하여 보강할 수 있습니다. 이 프로세스에 대한 자세한 내용은 다음을 참조하세요. 

* [*자습서: 모델 정의 및 적용*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Azure Digital Twins에 이미 저장된 모델 및 그래프 데이터를 사용하여 이 정보를 자동으로 제공하는 사용자 지정 논리를 작성할 수 있습니다. 트윈 그래프에서 정보를 관리, 업그레이드 및 검색하는 방법에 대한 자세한 내용은 다음 참조를 참조하세요.

* [*방법: 디지털 트윈 관리*](./how-to-manage-twin.md)
* [*방법: 트윈 그래프 쿼리*](./how-to-query-graph.md)