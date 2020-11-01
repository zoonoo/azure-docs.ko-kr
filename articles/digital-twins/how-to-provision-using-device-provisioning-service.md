---
title: DPS를 사용 하 여 장치 자동 관리
titleSuffix: Azure Digital Twins
description: 장치 프로 비전 서비스 (DPS)를 사용 하 여 Azure Digital Twins에서 IoT 장치를 프로 비전 하 고 사용 중지 하는 자동화 된 프로세스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0a18e6cef568afa8a0092fc06d8f6bb526739b2a
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145806"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>DPS (장치 프로 비전 서비스)를 사용 하 여 Azure Digital Twins의 장치 자동 관리

이 문서에서는 Azure Digital Twins를 [DPS (장치 프로 비전 서비스)](../iot-dps/about-iot-dps.md)와 통합 하는 방법에 대해 알아봅니다.

이 문서에서 설명 하는 솔루션을 통해 장치 프로 비전 서비스를 사용 하 여 Azure Digital Twins에서 IoT Hub 장치를 **_프로 비전_** 하 고 사용 **_중지_** 하는 프로세스를 자동화할 수 있습니다. 

_프로 비전_ 및 사용 _중지_ 단계에 대 한 자세한 내용 및 모든 엔터프라이즈 IoT 프로젝트에 공통적인 일반적인 장치 관리 단계 집합을 보다 잘 이해 하려면 IoT Hub의 장치 관리 설명서에서 [ *장치 수명 주기* 섹션](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) 을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

프로 비전을 설정 하려면 먼저 모델 및 쌍을 포함 하는 **Azure Digital twins 인스턴스가** 있어야 합니다. 또한이 인스턴스는 데이터를 기반으로 디지털 쌍 정보를 업데이트 하는 기능을 사용 하 여 설정 해야 합니다. 

이 설정이 아직 없는 경우 Azure Digital Twins [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md)을 따라 만들 수 있습니다. 이 자습서에서는 모델 및 쌍을 사용 하 여 Azure Digital Twins 인스턴스를 설정 하 고, 연결 된 Azure [IoT Hub](../iot-hub/about-iot-hub.md)를 설정 하 고, 데이터 흐름을 전파 하는 여러 [azure 기능](../azure-functions/functions-overview.md) 을 설정 하는 방법을 안내 합니다.

인스턴스를 설정 하는 경우이 문서의 뒷부분에서 다음 값이 필요 합니다. 이러한 값을 다시 수집 해야 하는 경우 아래 링크를 사용 하 여 지침을 따르세요.
* Azure Digital Twins 인스턴스 **_호스트 이름_** ( [포털에서 찾기](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure Event Hubs 연결 문자열 **_연결 문자열_** ( [포털에서 찾기](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

또한이 샘플에서는 장치 프로 비전 서비스를 사용 하 여 프로 비전을 포함 하는 **장치 시뮬레이터** 를 사용 합니다. 장치 시뮬레이터는 [Azure Digital Twins 및 IoT Hub 통합 샘플](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)에 있습니다. 샘플 링크로 이동 하 고 제목 아래에서 *ZIP 다운로드* 단추를 선택 하 여 컴퓨터에서 샘플 프로젝트를 가져옵니다. 다운로드 한 폴더의 압축을 풉니다.

장치 시뮬레이터는 **Node.js** 버전 10.0. x 이상을 기반으로 합니다. [*개발 환경 준비*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) Windows 또는 Linux에서이 자습서에 대 한 Node.js를 설치 하는 방법을 설명 합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

아래 이미지는 장치 프로 비전 서비스와 함께 Azure Digital Twins를 사용 하는이 솔루션의 아키텍처를 보여 줍니다. 장치 프로 비전 및 사용 중지 흐름이 모두 표시 됩니다.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="종단 간 시나리오에서 장치 및 여러 Azure 서비스의 뷰입니다. 데이터는 자동 온도 조절기 장치와 DPS 사이에서 앞뒤로 흐릅니다. 또한 데이터는 DPS에서 IoT Hub로, ' 할당 ' 레이블이 지정 된 Azure 함수를 통해 Azure Digital Twins로 흐릅니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub > Event Hubs > Azure Functions Azure Digital Twins를 통해 흐릅니다.":::

이 문서는 다음 두 개의 섹션으로 구분됩니다.
* [*장치 프로 비전 서비스를 사용 하 여 장치 자동 프로 비전*](#auto-provision-device-using-device-provisioning-service)
* [*IoT Hub 수명 주기 이벤트를 사용 하 여 장치 자동 사용 중지*](#auto-retire-device-using-iot-hub-lifecycle-events)

아키텍처의 각 단계에 대 한 자세한 설명은이 문서의 뒷부분에 있는 개별 섹션을 참조 하십시오.

## <a name="auto-provision-device-using-device-provisioning-service"></a>장치 프로 비전 서비스를 사용 하 여 장치 자동 프로 비전

이 섹션에서는 장치 프로 비전 서비스를 Azure Digital Twins에 연결 하 여 아래 경로를 통해 장치를 자동으로 프로 비전 합니다. 이는 [앞](#solution-architecture)에서 설명한 전체 아키텍처에서 발췌 한 것입니다.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="종단 간 시나리오에서 장치 및 여러 Azure 서비스의 뷰입니다. 데이터는 자동 온도 조절기 장치와 DPS 사이에서 앞뒤로 흐릅니다. 또한 데이터는 DPS에서 IoT Hub로, ' 할당 ' 레이블이 지정 된 Azure 함수를 통해 Azure Digital Twins로 흐릅니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub > Event Hubs > Azure Functions Azure Digital Twins를 통해 흐릅니다.":::

프로세스 흐름에 대 한 설명은 다음과 같습니다.
1. 장치는 DPS 끝점에 연결 하 고 식별 정보를 전달 하 여 id를 증명 합니다.
2. DPS는 등록 목록에 대해 등록 ID 및 키의 유효성을 검사 하 여 장치 id의 유효성을 검사 하 고 [Azure function](../azure-functions/functions-overview.md) 을 호출 하 여 할당을 수행 합니다.
3. Azure function은 장치에 대 한 Azure Digital Twins [에 새 쌍](concepts-twins-graph.md) 을 만듭니다.
4. DPS는 IoT hub에 장치를 등록 하 고 장치의 원하는 쌍 상태를 채웁니다.
5. IoT hub는 장치 ID 정보 및 IoT hub 연결 정보를 장치에 반환 합니다. 이제 장치에서 IoT hub에 연결할 수 있습니다.

다음 섹션에서는이 자동 프로 비전 장치 흐름을 설정 하는 단계를 안내 합니다.

### <a name="create-a-device-provisioning-service"></a>Device Provisioning Service 만들기

장치 프로 비전 서비스를 사용 하 여 새 장치를 프로 비전 하는 경우 해당 장치에 대 한 새 쌍을 Azure Digital Twins에서 만들 수 있습니다.

IoT 장치를 프로 비전 하는 데 사용 되는 장치 프로 비전 서비스 인스턴스를 만듭니다. 아래 Azure CLI 지침을 사용 하거나 Azure Portal를 사용할 수 있습니다. 빠른 시작 [*: Azure Portal*](../iot-dps/quick-setup-auto-provision.md)를 사용 하 여 IoT Hub Device Provisioning Service를 설정 합니다.

다음 Azure CLI 명령은 장치 프로 비전 서비스를 만듭니다. 이름, 리소스 그룹 및 지역을 지정 해야 합니다. [컴퓨터에](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)Azure CLI가 설치 되어 있는 경우이 명령은 [Cloud Shell](https://shell.azure.com)또는 로컬로 실행할 수 있습니다.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Azure Function 만들기

다음에는 함수 앱 내에서 HTTP 요청 트리거 함수를 만듭니다. 종단 간 자습서에서 만든 함수 앱 ( [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md)) 또는 자체를 사용할 수 있습니다.

이 함수는 [사용자 지정 할당 정책의](../iot-dps/how-to-use-custom-allocation-policies.md) 장치 프로 비전 서비스에서 새 장치를 프로 비전 하는 데 사용 됩니다. Azure 함수에서 HTTP 요청을 사용 하는 방법에 대 한 자세한 내용은 [*Azure Functions에 대 한 Azure http 요청 트리거*](../azure-functions/functions-bindings-http-webhook-trigger.md)를 참조 하세요.

함수 앱 프로젝트 내에서 새 함수를 추가 합니다. 또한 프로젝트에 새 NuGet 패키지를 추가 `Microsoft.Azure.Devices.Provisioning.Service` 합니다.

새로 만든 함수 코드 파일에서 다음 코드를 붙여 넣습니다.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);

            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);

            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(dtId, twinProps);
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

파일을 저장 한 다음 함수 앱을 다시 게시 합니다. 함수 앱을 게시 하는 방법에 대 한 지침은 종단 간 자습서의 [*응용 프로그램 게시*](tutorial-end-to-end.md#publish-the-app) 섹션을 참조 하세요.

### <a name="configure-your-function"></a>함수 구성

다음으로 이전에 만든 Azure Digital Twins 인스턴스에 대 한 참조를 포함 하 여 함수 앱에서 환경 변수를 설정 해야 합니다. 종단 간 자습서 ( [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md))를 사용한 경우 설정이 이미 구성 됩니다.

이 Azure CLI 명령을 사용 하 여 설정을 추가 합니다.

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

종단 간 자습서에서 [*함수 앱에 대 한 사용 권한 할당*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) 섹션에 설명 된 대로 함수 앱에 대 한 권한 및 관리 id 역할 할당이 올바르게 구성 되어 있는지 확인 합니다.

### <a name="create-device-provisioning-enrollment"></a>장치 프로 비전 등록 만들기

다음으로 **사용자 지정 할당 함수** 를 사용 하 여 장치 프로 비전 서비스에서 등록을 만들어야 합니다. 지침에 따라 사용자 지정 할당 정책에 대 한 장치 프로 비전 서비스 문서의 [*등록 만들기*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) 및 [*고유 장치 키 파생*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) 섹션에서이 작업을 수행 합니다.

이 흐름을 진행 하는 동안 **허브에 장치를 할당 하는 방법을 선택** 하는 단계에서 함수를 선택 하 여 방금 만든 함수에 등록을 연결 합니다. 등록을 만든 후에는 나중에 등록 이름과 기본 또는 보조 SAS 키를 사용 하 여이 문서에 대 한 장치 시뮬레이터를 구성 합니다.

### <a name="set-up-the-device-simulator"></a>장치 시뮬레이터 설정

이 샘플에서는 장치 프로 비전 서비스를 사용 하 여 프로 비전을 포함 하는 장치 시뮬레이터를 사용 합니다. 장치 시뮬레이터는 [Azure Digital Twins 및 IoT Hub 통합 샘플](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)에 있습니다. 샘플을 아직 다운로드 하지 않은 경우 샘플 링크로 이동 하 여 제목 아래에 있는 *ZIP 다운로드* 단추를 선택 하 여 지금 가져옵니다. 다운로드 한 폴더의 압축을 풉니다.

명령 창을 열고 다운로드 한 폴더로 이동한 다음, *장치 시뮬레이터* 디렉터리로 이동 합니다. 다음 명령을 사용 하 여 프로젝트에 대 한 종속성을 설치 합니다.

```cmd
npm install
```

다음으로 *, env 파일* 을 *.env* 라는 새 파일에 복사 하 고 다음 설정을 입력 합니다.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

파일을 저장하고 닫습니다.

### <a name="start-running-the-device-simulator"></a>장치 시뮬레이터 실행 시작

명령 창의 *device 시뮬레이터* 디렉터리에서 다음 명령을 사용 하 여 장치 시뮬레이터를 시작 합니다.

```cmd
node .\adt_custom_register.js
```

장치를 등록 하 고 IoT Hub에 연결한 다음 메시지 보내기를 시작 해야 합니다.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="종단 간 시나리오에서 장치 및 여러 Azure 서비스의 뷰입니다. 데이터는 자동 온도 조절기 장치와 DPS 사이에서 앞뒤로 흐릅니다. 또한 데이터는 DPS에서 IoT Hub로, ' 할당 ' 레이블이 지정 된 Azure 함수를 통해 Azure Digital Twins로 흐릅니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub > Event Hubs > Azure Functions Azure Digital Twins를 통해 흐릅니다.":::

### <a name="validate"></a>유효성 검사

이 문서에서 설정한 흐름의 결과로 장치가 Azure Digital Twins에 자동으로 등록 됩니다. 다음 [Azure Digital TWINS CLI](how-to-use-cli.md) 명령을 사용 하 여 사용자가 만든 Azure Digital twins 인스턴스에서 장치의 쌍을 찾습니다.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Azure Digital Twins 인스턴스에서 발견 되는 장치의 쌍이 표시 되어야 합니다.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="종단 간 시나리오에서 장치 및 여러 Azure 서비스의 뷰입니다. 데이터는 자동 온도 조절기 장치와 DPS 사이에서 앞뒤로 흐릅니다. 또한 데이터는 DPS에서 IoT Hub로, ' 할당 ' 레이블이 지정 된 Azure 함수를 통해 Azure Digital Twins로 흐릅니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub > Event Hubs > Azure Functions Azure Digital Twins를 통해 흐릅니다.":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>IoT Hub 수명 주기 이벤트를 사용 하 여 장치 자동 사용 중지

이 섹션에서는 Azure Digital Twins에 IoT Hub 수명 주기 이벤트를 연결 하 여 아래 경로를 통해 장치의 자동 사용을 중지 합니다. 이는 [앞](#solution-architecture)에서 설명한 전체 아키텍처에서 발췌 한 것입니다.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="종단 간 시나리오에서 장치 및 여러 Azure 서비스의 뷰입니다. 데이터는 자동 온도 조절기 장치와 DPS 사이에서 앞뒤로 흐릅니다. 또한 데이터는 DPS에서 IoT Hub로, ' 할당 ' 레이블이 지정 된 Azure 함수를 통해 Azure Digital Twins로 흐릅니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub > Event Hubs > Azure Functions Azure Digital Twins를 통해 흐릅니다.":::

프로세스 흐름에 대 한 설명은 다음과 같습니다.
1. 외부 또는 수동 프로세스는 IoT Hub에서 장치 삭제를 트리거합니다.
2. IoT Hub는 장치를 삭제 하 고 [이벤트 허브](../event-hubs/event-hubs-about.md)로 라우팅되는 [장치 수명 주기](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) 이벤트를 생성 합니다.
3. Azure function은 Azure Digital Twins에서 장치 쌍을 삭제 합니다.

다음 섹션에서는이 자동 사용 중지 장치 흐름을 설정 하는 단계를 안내 합니다.

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

이제 IoT Hub 수명 주기 이벤트를 수신 하는 데 사용 되는 Azure [event hub](../event-hubs/event-hubs-about.md)를 만들어야 합니다. 

다음 정보를 사용 하 여 [*이벤트 허브 만들기*](../event-hubs/event-hubs-create.md) 빠른 시작에 설명 된 단계를 진행 합니다.
* 종단 간 자습서 ( [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md))를 사용 하는 경우 종단 간 자습서에 대해 만든 리소스 그룹을 다시 사용할 수 있습니다.
* 이벤트 허브의 이름을 *lifecycleevents* , 사용자가 선택한 다른 항목의 이름을 선택 하 고, 만든 네임 스페이스를 기억할 수 있습니다. 다음 섹션에서 수명 주기 함수를 설정 하 고 경로를 IoT Hub 때이를 사용 합니다.

### <a name="create-an-azure-function"></a>Azure Function 만들기

다음으로 함수 앱 내에서 Event Hubs 트리거 함수를 만듭니다. 종단 간 자습서에서 만든 함수 앱 ( [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md)) 또는 자체를 사용할 수 있습니다. 

이벤트 허브의 이름을 *lifecycleevents* , 이벤트 허브 트리거를 이전 단계에서 만든 이벤트 허브에 연결 합니다. 다른 이벤트 허브 이름을 사용한 경우 아래의 트리거 이름에서 일치 하도록 변경 합니다.

이 함수는 IoT Hub 장치 수명 주기 이벤트를 사용 하 여 기존 장치를 사용 중지 합니다. 수명 주기 이벤트에 대 한 자세한 내용은 [*IoT Hub 비 원격 분석 이벤트*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조 하세요. Azure 함수에서 Event Hubs를 사용 하는 방법에 대 한 자세한 내용은 [*Azure Functions에 대 한 azure Event Hubs 트리거*](../azure-functions/functions-bindings-event-hubs-trigger.md)를 참조 하세요.

게시 된 함수 앱 내에서 *Event Hub 트리거* 형식의 새 함수 클래스를 추가 하 고 아래 코드를 붙여넣습니다.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

프로젝트를 저장 한 다음 함수 앱을 다시 게시 합니다. 함수 앱을 게시 하는 방법에 대 한 지침은 종단 간 자습서의 [*응용 프로그램 게시*](tutorial-end-to-end.md#publish-the-app) 섹션을 참조 하세요.

### <a name="configure-your-function"></a>함수 구성

다음에는 앞서 만든 Azure Digital Twins 인스턴스와 이벤트 허브에 대 한 참조가 포함 된 함수 앱에서 환경 변수를 설정 해야 합니다. 종단 간 자습서 ( [*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md))를 사용 하는 경우 첫 번째 설정이 이미 구성 됩니다.

이 Azure CLI 명령을 사용 하 여 설정을 추가 합니다. [컴퓨터에](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)Azure CLI가 설치 되어 있는 경우이 명령은 [Cloud Shell](https://shell.azure.com)또는 로컬로 실행할 수 있습니다.

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

다음에는 새로 만든 이벤트 허브에 연결 하기 위한 함수 환경 변수를 구성 해야 합니다.

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

종단 간 자습서에서 [*함수 앱에 대 한 사용 권한 할당*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) 섹션에 설명 된 대로 함수 앱에 대 한 권한 및 관리 id 역할 할당이 올바르게 구성 되어 있는지 확인 합니다.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>수명 주기 이벤트에 대 한 IoT Hub 경로 만들기

이제 장치 수명 주기 이벤트를 라우팅하기 위해 IoT Hub 경로를 설정 해야 합니다. 이 경우에는에 의해 식별 되는 장치 삭제 이벤트를 특별히 수신 대기 `if (opType == "deleteDeviceIdentity")` 합니다. 그러면 디지털 쌍 항목의 삭제를 트리거하고 장치와 디지털 쌍의 사용 중지를 종료 합니다.

IoT Hub 경로 만들기에 대 한 지침은이 문서에 설명 되어 있습니다. [*IoT Hub 메시지 라우팅을 사용 하 여 장치-클라우드 메시지를 다른 끝점으로 보냅니다*](../iot-hub/iot-hub-devguide-messages-d2c.md). *비 원격 분석 이벤트* 섹션에서는 **장치 수명 주기 이벤트** 를 경로에 대 한 데이터 원본으로 사용할 수 있음을 설명 합니다.

이 설정에 대해 수행 해야 하는 단계는 다음과 같습니다.
1. 사용자 지정 IoT Hub 이벤트 허브 끝점을 만듭니다. 이 끝점은 [*event Hub 만들기*](#create-an-event-hub) 섹션에서 만든 이벤트 허브를 대상으로 해야 합니다.
2. *장치 수명 주기 이벤트* 경로를 추가 합니다. 이전 단계에서 만든 끝점을 사용 합니다. 라우팅 쿼리를 추가 하 여 삭제 이벤트만 전송 하도록 장치 수명 주기 이벤트를 제한할 수 있습니다 `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="종단 간 시나리오에서 장치 및 여러 Azure 서비스의 뷰입니다. 데이터는 자동 온도 조절기 장치와 DPS 사이에서 앞뒤로 흐릅니다. 또한 데이터는 DPS에서 IoT Hub로, ' 할당 ' 레이블이 지정 된 Azure 함수를 통해 Azure Digital Twins로 흐릅니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub > Event Hubs > Azure Functions Azure Digital Twins를 통해 흐릅니다.":::

이 흐름을 완료 한 후에는 모든 것이 종단 간 장치 사용 중지로 설정 됩니다.

### <a name="validate"></a>유효성 검사

사용 중지 프로세스를 트리거하려면 IoT Hub에서 장치를 수동으로 삭제 해야 합니다.

[이 문서의 처음 절반](#auto-provision-device-using-device-provisioning-service)에서는 IoT Hub 및 해당 디지털 쌍으로 장치를 만들었습니다. 

이제 IoT Hub로 이동 하 여 해당 장치를 삭제 합니다 ( [Azure CLI 명령](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest&preserve-view=true#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) 또는 [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)를 사용 하 여이 작업을 수행할 수 있음). 

장치가 Azure Digital Twins에서 자동으로 제거 됩니다. 

다음 [Azure Digital TWINS CLI](how-to-use-cli.md) 명령을 사용 하 여 Azure Digital twins 인스턴스에서 장치 쌍이 삭제 되었는지 확인 합니다.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

장치 쌍이 더 이상 Azure Digital Twins 인스턴스에서 검색 되지 않는 것을 볼 수 있습니다.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="종단 간 시나리오에서 장치 및 여러 Azure 서비스의 뷰입니다. 데이터는 자동 온도 조절기 장치와 DPS 사이에서 앞뒤로 흐릅니다. 또한 데이터는 DPS에서 IoT Hub로, ' 할당 ' 레이블이 지정 된 Azure 함수를 통해 Azure Digital Twins로 흐릅니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub > Event Hubs > Azure Functions Azure Digital Twins를 통해 흐릅니다.":::

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 더 이상 필요 하지 않은 경우 다음 단계에 따라 삭제 합니다.

Azure Cloud Shell 또는 로컬 Azure CLI를 사용 하 여 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) 명령을 사용 하 여 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 그러면 리소스 그룹이 제거 됩니다. Azure Digital Twins 인스턴스 IoT hub 및 허브 장치 등록 event grid 토픽 및 관련 구독 event hubs 네임 스페이스 및 저장소와 같은 관련 된 리소스를 포함 하 여 Azure Functions 앱입니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

그런 다음 로컬 컴퓨터에서 다운로드 한 프로젝트 샘플 폴더를 삭제 합니다.

## <a name="next-steps"></a>다음 단계

장치에 대해 생성 된 디지털 쌍은 Azure digital 쌍에 플랫 계층으로 저장 되지만 모델 정보 및 조직에 대 한 다중 수준 계층 구조를 사용 하 여 보강 수 있습니다. 이 개념에 대 한 자세한 내용은 다음을 참조 하세요.

* [*개념: 디지털 쌍 및 쌍 그래프*](concepts-twins-graph.md)

Azure Digital Twins에 이미 저장 된 모델 및 그래프 데이터를 사용 하 여이 정보를 자동으로 제공 하는 사용자 지정 논리를 작성할 수 있습니다. 쌍 그래프에서 정보를 관리, 업그레이드 및 검색 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

* [*방법: 디지털 쌍 관리*](how-to-manage-twin.md)
* [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)