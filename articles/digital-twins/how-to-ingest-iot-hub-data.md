---
title: IoT Hub에서 원격 분석 수집
titleSuffix: Azure Digital Twins
description: IoT Hub에서 장치 원격 분석 메시지를 수집 하는 방법을 참조 하세요.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7c73f007f85a963a09de4e05222082fd52f784c0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131568"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Azure Digital Twins에 IoT Hub 원격 분석 수집

Azure Digital Twins는 IoT 장치 및 기타 원본의 데이터를 기반으로 합니다. Azure Digital Twins에서 사용할 장치 데이터의 공통 소스는 [IoT Hub](../iot-hub/about-iot-hub.md)됩니다.

미리 보기 중에 데이터를 azure Digital 쌍로 수집 하는 프로세스는 [azure function](../azure-functions/functions-overview.md)과 같은 외부 계산 리소스를 설정 하 여 데이터를 수신 하 고, [DigitalTwins api](how-to-use-apis-sdks.md) 를 사용 하 여 속성을 설정 하거나 [디지털](concepts-twins-graph.md) 쌍에서 원격 분석 이벤트를 발생 시킵니다. 

이 방법 문서에서는 IoT Hub에서 원격 분석을 수집할 수 있는 Azure 함수를 작성 하는 프로세스를 안내 합니다.

## <a name="example-telemetry-scenario"></a>원격 분석 시나리오 예제

이 방법에서는 Azure function을 사용 하 여 IoT Hub에서 Azure Digital Twins로 메시지를 보내는 방법을 간략하게 설명 합니다. 이 작업에 사용할 수 있는 여러 가지 구성 및 일치 전략이 있지만이 문서의 예제에는 다음 부분이 포함 되어 있습니다.
* IoT Hub의 온도계 장치로, 알려진 장치 ID가 있습니다.
* 일치 하는 ID를 사용 하 여 장치를 나타내는 디지털 쌍
* 대화방을 나타내는 디지털 쌍

> [!NOTE]
> 이 예에서는 장치 ID와 해당 하는 디지털 쌍 ID 사이에 간단한 ID 일치를 사용 하지만, 장치에서 해당 쌍 (예: 매핑 테이블)으로 보다 정교한 매핑을 제공할 수 있습니다.

온도계 장치에서 온도 원격 분석 이벤트를 보낼 때마다 *대화방* 쌍의 *온도* 속성이 업데이트 되어야 합니다. 이러한 작업을 수행 하려면 장치의 원격 분석 이벤트를 디지털 쌍의 속성 setter에 매핑합니다. 쌍 [그래프](concepts-twins-graph.md) 의 토폴로지 정보를 사용 하 여 *방* 쌍을 찾은 다음 쌍의 속성을 설정할 수 있습니다. 다른 시나리오에서는 사용자가 일치 하는 쌍에 대 한 속성을 설정할 수 있습니다 (이 예제에서는 ID가 *123*인 쌍). Azure Digital Twins는 원격 분석 데이터가 쌍으로 매핑되는 방식을 결정 하는 많은 유연성을 제공 합니다. 

이 시나리오는 아래 다이어그램에 설명 되어 있습니다.

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="IoT Hub 장치는 IoT Hub, Event Grid 또는 시스템 항목을 통해 온도 원격 분석을 azure 함수에 전송 하며, azure 함수는 azure 디지털 쌍의 쌍에서 온도 속성을 업데이트 합니다." border="false":::

## <a name="prerequisites"></a>필수 구성 요소

이 예를 계속 하기 전에 다음 필수 구성 요소를 완료 해야 합니다.
1. IoT Hub를 만듭니다. 지침은 [이 IoT Hub 빠른](../iot-hub/quickstart-send-telemetry-cli.md) 시작의 *IoT Hub 만들기* 섹션을 참조 하세요.
2. IoT Hub에서 이벤트를 처리 하는 Azure 함수를 하나 이상 만듭니다. [*방법: 데이터를 처리 하는 azure 함수를 설정*](how-to-create-azure-function.md) 하 여 Azure Digital twins에 연결 하 고 Azure Digital TWINS API 함수를 호출 하는 기본 azure 함수를 작성 하는 방법을 참조 하세요. 이 방법의 나머지 부분에서는이 함수를 빌드합니다.
3. 허브 데이터의 이벤트 대상을 설정 합니다. [Azure Portal](https://portal.azure.com/)에서 IoT Hub 인스턴스로 이동 합니다. *이벤트*아래에서 Azure function에 대 한 구독을 만듭니다. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure Portal: 이벤트 구독 추가":::

4. *이벤트 구독 만들기* 페이지에서 다음과 같이 필드를 채웁니다.
   * *이벤트 구독 세부 정보*에서 구독 이름을 원하는 대로 선택 합니다.
   * *이벤트 유형*에서 필터링 할 이벤트 유형으로 *장치 원격 분석* 을 선택 합니다.
      - 원하는 경우 다른 이벤트 유형에 필터를 추가 합니다.
   * *끝점 세부 정보*에서 끝점으로 Azure 함수를 선택 합니다.

## <a name="create-an-azure-function-in-visual-studio"></a>Visual Studio에서 Azure function 만들기

이 섹션에서는 동일한 Visual Studio 시작 단계와 [*방법: 데이터 처리를 위한 azure 함수 설정*](how-to-create-azure-function.md)의 azure function 기초를 사용 합니다. 이 기초는 인증을 처리 하 고 서비스 클라이언트를 만들어 데이터를 처리 하 고 Azure Digital Twins Api를 응답으로 호출할 수 있도록 준비 했습니다. 

해골 함수의 핵심은 다음과 같습니다.

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

다음 단계에서는 IoT Hub에서 IoT 원격 분석 이벤트를 처리 하기 위해 특정 코드를 추가 합니다.  

## <a name="add-telemetry-processing"></a>원격 분석 처리 추가

원격 분석 이벤트는 장치의 메시지 형식으로 제공 됩니다. 원격 분석 처리 코드를 추가 하는 첫 번째 단계는 Event Grid 이벤트에서이 장치 메시지의 관련 부분을 추출 하는 것입니다. 

장치 마다 메시지를 다르게 구성할 수 있으므로이 단계의 코드는 연결 된 장치에 따라 다릅니다. 

다음 코드는 원격 분석을 JSON으로 전송 하는 간단한 장치에 대 한 예를 보여 줍니다. 샘플은 메시지를 보낸 장치의 장치 ID 및 온도 값을 추출 합니다.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

이 연습의 목적은 쌍 그래프 내에서 *방의* 온도를 업데이트 하는 것입니다. 즉, 메시지의 대상은이 장치와 연결 된 디지털 쌍이 아니라 부모 인 *대화방* 쌍 임을 의미 합니다. 위의 코드를 사용 하 여 원격 분석 메시지에서 추출한 장치 ID 값을 사용 하 여 부모 쌍을 찾을 수 있습니다.

이렇게 하려면 Azure Digital Twins Api를 사용 하 여 장치를 나타내는 쌍 (이 경우 장치와 동일한 ID)에 대 한 들어오는 관계에 액세스 합니다. 들어오는 관계에서 아래 코드 조각을 사용 하 여 부모의 ID를 찾을 수 있습니다.

아래 코드 조각에서는 쌍의 들어오는 관계를 검색 하는 방법을 보여 줍니다.

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

쌍의 부모는 관계의 *SourceId* 속성에 있습니다.

장치를 나타내는 쌍의 모델에는 하나의 들어오는 관계만 포함 하는 것이 매우 일반적입니다. 이 경우 반환 되는 첫 번째 (및 유일한) 관계를 선택할 수 있습니다. 모델에서이 쌍에 대 한 여러 유형의 관계를 허용 하는 경우 여러 개의 들어오는 관계 중에서 선택 하도록 추가로 지정 해야 할 수 있습니다. 이 작업을 수행 하는 일반적인 방법은를 기준으로 관계를 선택 하는 것입니다 `RelationshipName` . 

*방을*나타내는 부모 쌍의 ID가 있으면 해당 쌍을 "패치" (업데이트) 할 수 있습니다. 이렇게 하려면 다음 코드를 사용 합니다.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>전체 Azure 함수 코드

다음은 이전 샘플의 코드를 사용 하 여 컨텍스트에서 전체 Azure 함수를 사용 하는 것입니다.

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

들어오는 관계를 찾는 유틸리티 함수:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

쌍을 패치 하는 유틸리티 함수는 다음과 같습니다.
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

이제 IoT Hub에서 제공 하는 시나리오 데이터를 읽고 해석 하는 데 사용할 수 있는 Azure 함수가 있습니다.

## <a name="debug-azure-function-apps-locally"></a>로컬에서 Azure 함수 앱 디버그

로컬에서 Event Grid 트리거를 사용 하 여 Azure 함수를 디버그할 수 있습니다. 이에 대 한 자세한 내용은 [*로컬에서 디버그 Event Grid 트리거*](../azure-functions/functions-debug-event-grid-trigger-local.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins를 사용 하 여 데이터 수신 및 송신에 대해 읽기:
* [*개념: 다른 서비스와 통합*](concepts-integration.md)
