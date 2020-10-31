---
title: 모니터링 Azure IoT Hub
description: 여기에서 시작 하 여 Azure IoT Hub를 모니터링 하는 방법을 알아보세요.
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: 71a7041ec02da9a85de411f1113814311c21cd4f
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128882"
---
# <a name="monitoring-azure-iot-hub"></a>모니터링 Azure IoT Hub

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure IoT Hub에서 생성 되는 모니터링 데이터 및 Azure Monitor의 기능을 사용 하 여이 데이터를 분석 하 고 경고 하는 방법에 대해 설명 합니다.

## <a name="monitor-overview"></a>모니터링 개요

각 IoT hub에 대 한 Azure Portal의 **개요** 페이지에는 사용 되는 메시지 수와 IoT hub에 연결 된 장치 수와 같은 일부 사용 메트릭을 제공 하는 차트가 포함 되어 있습니다.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="IoT hub 개요 페이지의 기본 메트릭 차트":::

메시지 수 값은 1 분으로 지연 될 수 있으며, IoT Hub 서비스 인프라와 관련 된 이유로 새로 고칠 때 값이 더 높거나 낮은 값 사이를 바운스 하는 경우도 있습니다. 이 카운터는 마지막 1 분 동안 계산 된 값에 대해서만 정확 하 게 지정 해야 합니다.

개요 창에 표시 되는 정보는 유용 하지만 IoT hub에 사용할 수 있는 적은 양의 모니터링 데이터만 나타냅니다. 일부 모니터링 데이터는 자동으로 수집 되며, IoT hub를 만드는 즉시 분석에 사용할 수 있습니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure IoT Hub는 Azure에서 전체 stack 모니터링 서비스인 [Azure Monitor](/azure/azure-monitor/overview)를 사용 하 여 모니터링 데이터를 만들며,이를 통해 다른 클라우드 및 온-프레미스의 리소스 외에도 azure 리소스를 모니터링할 수 있는 완전 한 기능 집합을 제공 합니다.

다음 개념을 설명 하는 [Azure Monitor을 사용 하 여 Azure 리소스 모니터링](/azure/azure-monitor/insights/monitor-azure-resource)문서부터 시작 합니다.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

다음 섹션에서는 Azure IoT Hub에 대해 수집 된 특정 데이터를 설명 하 고 Azure tools를 사용 하 여 데이터 수집을 구성 하 고이 데이터를 분석 하기 위한 예제를 제공 하 여이 문서를 작성

## <a name="monitoring-data"></a>데이터 모니터링

Azure IoT Hub는 [azure 리소스의 데이터 모니터링](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources)에 설명 된 다른 azure 리소스와 동일한 종류의 모니터링 데이터를 수집 합니다.

Azure IoT Hub에서 만든 메트릭 및 로그에 대 한 자세한 내용은 [모니터링 Azure IoT Hub 데이터 참조](monitor-iot-hub-reference.md) 를 참조 하세요.

> [!IMPORTANT]
> Azure Monitor 리소스 로그를 사용 하 여 IoT Hub 서비스에서 내보내는 이벤트는 안정적이 지 않거나 정렬 되지 않을 수도 있습니다. 일부 이벤트는 손실되거나 순서 없이 제공될 수 있습니다. 리소스 로그는 또한 실시간이 아니며 이벤트를 선택한 대상에 기록 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="collection-and-routing"></a>컬렉션 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집 되 고 저장 되지만 진단 설정을 사용 하 여 다른 위치로 라우팅될 수 있습니다.

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장 되지 않습니다.

메트릭 및 로그는 다음을 비롯 한 여러 위치로 라우팅할 수 있습니다.
- Azure Monitor 로그는 연결 된 Log Analytics 작업 영역을 통해 저장 됩니다. Log Analytics를 사용 하 여 분석할 수 있습니다.
- 보관 및 오프 라인 분석을 위한 Azure Storage 
- 외부 응용 프로그램 (예: 타사 SIEM 도구)에서 읽을 수 있는 Event Hubs 끝점입니다.

Azure Portal에서 IoT hub의 왼쪽 창에 있는 **모니터링** 에서 **진단** 설정을 선택 하 고, **진단 설정 추가** 를 선택 하 여 iot hub에서 내보낸 로그 및 플랫폼 메트릭으로 범위가 지정 된 진단 설정을 만들 수 있습니다.

다음 스크린샷은 리소스 로그 유형 *연결 작업* 및 모든 플랫폼 메트릭을 Log Analytics 작업 영역으로 라우팅하는 진단 설정을 보여 줍니다.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="IoT hub 개요 페이지의 기본 메트릭 차트":::

Azure Portal, CLI 또는 PowerShell을 사용 하 여 진단 설정을 만드는 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기를](/azure/azure-monitor/platform/diagnostic-settings) 참조 하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정 합니다. Azure IoT Hub에 대 한 범주는 [모니터링 Azure IoT Hub 데이터 참조의 리소스 로그에](monitor-iot-hub-reference.md#resource-logs)나열 됩니다.

IoT Hub 플랫폼 메트릭을 다른 위치로 라우팅하는 경우 다음에 유의 하세요.

- 진단 설정, *연결 된 장치 (미리 보기)* 및 *총 장치 (미리 보기)* 를 통해 다음 플랫폼 메트릭을 내보낼 수 없습니다.

- 예를 들어, 다차원 메트릭 (예: 일부 [라우팅 메트릭](monitor-iot-hub-reference.md#routing-metrics))은 현재 차원 값에 걸쳐 집계 된 평면화 된 단일 차원 메트릭으로 내보내집니다. 자세한 내용은 [플랫폼 메트릭을 다른 위치로 내보내기](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations)를 참조 하세요.

## <a name="analyzing-metrics"></a>메트릭 분석

**Azure Monitor** 메뉴에서 **메트릭을 열어 메트릭 탐색기를 사용** 하 여 다른 Azure 서비스의 메트릭과 함께 Azure IoT Hub에 대 한 메트릭을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](/azure/azure-monitor/platform/metrics-getting-started)을 참조하세요.

Azure Portal에서 IoT hub의 왼쪽 창에 있는 **모니터링** 아래에서 **메트릭** 을 선택 하 여 기본적으로 메트릭 탐색기 범위를 iot hub에서 내보낸 플랫폼 메트릭에 열 수 있습니다.

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="IoT hub 개요 페이지의 기본 메트릭 차트":::

Azure IoT Hub에 대해 수집 되는 플랫폼 메트릭의 목록은 [모니터링 Azure IoT Hub 데이터 참조의 메트릭](monitor-iot-hub-reference.md#metrics)을 참조 하세요. 모든 Azure 서비스에 대해 수집 되는 플랫폼 메트릭 목록은 [Azure Monitor에서 지원 되는 메트릭](/azure/azure-monitor/platform/metrics-supported)을 참조 하세요.

개수 단위로 수집 되는 IoT Hub 플랫폼 메트릭의 경우 일부 집계를 사용 하지 않거나 사용할 수 없는 경우도 있습니다. 자세히 알아보려면 [모니터링 Azure IoT Hub 데이터 참조에서 지원 되는 집계](monitor-iot-hub-reference.md#supported-aggregations)를 참조 하세요.

[라우팅 메트릭과](monitor-iot-hub-reference.md#routing-metrics)같은 일부 IoT Hub 메트릭은 다차원입니다. 이러한 메트릭에 대해 차원을 기반으로 하는 차트에 [필터](/azure/azure-monitor/platform/metrics-charts#apply-filters-to-charts) 및 [분할](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart) 을 적용할 수 있습니다.

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor 로그의 데이터는 테이블에 저장 됩니다 .이 테이블에는 각 테이블에 고유한 속성 집합이 있습니다. 이러한 테이블의 데이터는 Log Analytics 작업 영역과 연결 되며 Log Analytics에서 쿼리할 수 있습니다. Azure Monitor 로그에 대 한 자세한 내용은 Azure Monitor 설명서에서 [Azure Monitor 로그 개요](/azure/azure-monitor/platform/data-platform-logs) 를 참조 하세요. 

Azure Monitor 로그에 데이터를 라우팅하려면 Log Analytics 작업 영역에 리소스 로그 또는 플랫폼 메트릭을 전송 하는 진단 설정을 만들어야 합니다. 자세히 알아보려면 [수집 및 라우팅](#collection-and-routing)을 참조 하세요.

Azure Portal에서 iot hub의 왼쪽 창에 있는 **모니터링** 에서 **로그** 를 선택 하 여 기본적으로 범위 지정 된 Log Analytics 쿼리를 iot hub에 대 한 Azure Monitor 로그에 수집 된 로그 및 메트릭으로 수행할 수 있습니다.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="IoT hub 개요 페이지의 기본 메트릭 차트":::

Azure Monitor 로그 및 Log Analytics에서 쿼리할 때 사용 하는 테이블 목록은 [모니터링 Azure IoT Hub 데이터 참조에서 Azure Monitor Logs 테이블](monitor-iot-hub-reference.md#azure-monitor-logs-tables)을 참조 하세요.

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-schema#top-level-resource-logs-schema)에 설명 되어 있습니다. [모니터링 Azure IoT Hub 데이터 참조에서 리소스 로그](monitor-iot-hub-reference.md#resource-logs)의 Azure IoT Hub에 대해 수집 된 리소스 로그의 스키마 및 범주를 찾을 수 있습니다.

[활동 로그](/azure/azure-monitor/platform/activity-log) 는 구독 수준 이벤트에 대 한 통찰력을 제공 하는 Azure의 플랫폼 로그입니다. 독립적으로 보거나 Azure Monitor 로그로 라우팅할 수 있습니다. 그러면 Log Analytics를 사용 하 여 훨씬 더 복잡 한 쿼리를 수행할 수 있습니다.  

IoT Hub 플랫폼 메트릭을 Azure Monitor 로그에 라우팅하는 경우 다음에 유의 하세요.

- 진단 설정, *연결 된 장치 (미리 보기)* 및 *총 장치 (미리 보기)* 를 통해 다음 플랫폼 메트릭을 내보낼 수 없습니다.

- 예를 들어, 다차원 메트릭 (예: 일부 [라우팅 메트릭](monitor-iot-hub-reference.md#routing-metrics))은 현재 차원 값에 걸쳐 집계 된 평면화 된 단일 차원 메트릭으로 내보내집니다. 자세한 내용은 [플랫폼 메트릭을 다른 위치로 내보내기](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations)를 참조 하세요.

IoT Hub를 사용 하는 몇 가지 일반적인 쿼리는 [샘플 Kusto 쿼리](#sample-kusto-queries)를 참조 하세요. Log Analytics 쿼리를 사용 하는 방법에 대 한 자세한 내용은 [Azure Monitor 로그 쿼리 개요](/azure/azure-monitor/log-query/log-query-overview)를 참조 하세요.

### <a name="sdk-version-in-iot-hub-logs"></a>IoT Hub 로그의 SDK 버전

IoT Hub 리소스 로그의 일부 작업 `sdkVersion` 은 개체의 속성을 반환 `properties` 합니다. 이러한 작업의 경우 장치 또는 백 엔드 앱이 Azure IoT Sdk 중 하나를 사용 하는 경우이 속성에는 사용 되는 SDK, SDK 버전 및 SDK가 실행 되는 플랫폼에 대 한 정보가 포함 됩니다. 다음 예제에서는 `sdkVersion` [`deviceConnect`](monitor-iot-hub-reference.md#connections) Node.js 장치 SDK를 사용 하는 경우 작업에 대해 내보내는 속성을 보여 줍니다 `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . 다음은 .NET (c #) SDK에 대해 내보내는 값의 예입니다. `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"`

다음 표에서는 다양 한 Azure IoT Sdk에 사용 되는 SDK 이름을 보여 줍니다.

| SdkVersion 속성의 SDK 이름 | 언어 |
|----------|----------|
| .NET | .NET (C#) |
| microsoft. azure. 장치 | .NET (c #) 서비스 SDK |
| microsoft. azure. 클라이언트 | .NET (c #) 장치 SDK |
| iothubclient | C 또는 Python v1 (사용 되지 않음) 장치 SDK |
| iothubserviceclient | C 또는 Python v1 (사용 되지 않음) 서비스 SDK |
| iothub-py | Python 장치 SDK |
| azure-iot-device | Node.js 장치 SDK |
| azure-iothub | Node.js 서비스 SDK |
| iothub-클라이언트입니다. | Java 장치 SDK |
| iothub. c a c. | Java 서비스 SDK |
| com.. c a t. a c c. | Java 장치 SDK |
| .com.. c a t. a c. | Java 서비스 SDK |
| C | 임베디드 C |
| C + (OSSimplified = Azure RTOS) | Azure RTOS |

IoT Hub 리소스 로그에 대해 쿼리를 수행 하는 경우 SDK 버전 속성을 추출할 수 있습니다. 예를 들어 다음 쿼리는 연결 작업에서 반환 된 속성에서 SDK 버전 속성 및 장치 ID를 추출 합니다. 이러한 두 속성은 작업 시간 및 장치가 연결 되는 IoT hub의 리소스 ID와 함께 결과에 기록 됩니다.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> IoT hub 메뉴에서 **로그** 를 선택 하면 현재 iot hub로 설정 된 쿼리 범위를 사용 하 여 Log Analytics 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 IoT hub의 데이터 또는 다른 Azure 서비스의 데이터를 포함 하는 쿼리를 실행 하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택 합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope/)를 참조하세요.

IoT hub를 모니터링 하는 데 사용할 수 있는 쿼리는 다음과 같습니다.

- 연결 오류: 장치 연결 오류를 식별 합니다.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- 제한 오류: 대부분의 요청으로 인해 제한 오류가 발생 한 장치를 식별 합니다.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- 배달 되지 않은 끝점: 문제가 보고 된 횟수 및 이유를 기준으로 비정상 또는 비정상 끝점을 식별 합니다.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- 오류 요약: 유형별 모든 작업에서 발생 한 오류 수입니다.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- 최근에 연결 된 장치: 지정 된 기간에 연결 IoT Hub 확인 하는 장치 목록입니다.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- 장치의 SDK 버전: 장치 연결 또는 장치-클라우드 쌍 작업을 위한 장치 및 해당 SDK 버전의 목록입니다.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Azure Event Hubs의 로그 읽기

진단 설정을 통해 이벤트 로깅을 설정한 후에는 로그의 정보를 기반으로 조치를 취할 수 있도록 로그를 읽는 애플리케이션을 만들 수 있습니다. 다음 샘플 코드는 이벤트 허브에서 로그를 검색합니다.

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요 한 조건이 발견 될 때 사용자에 게 사전에 알려 줍니다. 이를 통해 사용자는 시스템에서 문제를 파악 하 고 해결할 수 있습니다. [메트릭](/azure/azure-monitor/platform/alerts-metric-overview), [로그](/azure/azure-monitor/platform/alerts-unified-log)및 [활동 로그](/azure/azure-monitor/platform/activity-log-alerts)에 대 한 경고를 설정할 수 있습니다. 서로 다른 유형의 경고에는 장점과 단점이 있습니다.

플랫폼 메트릭을 기반으로 경고 규칙을 만들 때 개수 단위로 수집 되는 IoT Hub 플랫폼 메트릭의 경우 일부 집계를 사용 하지 않거나 사용할 수 없는 경우도 있습니다. 자세히 알아보려면 [모니터링 Azure IoT Hub 데이터 참조에서 지원 되는 집계](monitor-iot-hub-reference.md#supported-aggregations)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Service name]에서 만든 메트릭, 로그 및 기타 중요 한 값에 대 한 참조는 [모니터링 Azure IoT Hub 데이터 참조](monitor-iot-hub-reference.md) 를 참조 하세요.

- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](/azure/azure-monitor/insights/monitor-azure-resource)을 참조하세요.
