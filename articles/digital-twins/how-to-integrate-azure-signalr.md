---
title: Azure SignalR 서비스와 통합
titleSuffix: Azure Digital Twins
description: Azure SignalR를 사용 하 여 Azure Digital Twins 원격 분석을 클라이언트로 스트리밍하는 방법을 참조 하세요.
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 38e3526627eb4191643f8bc86b9ce5f49e41a71f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564409"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Azure SignalR Service와 Azure Digital Twins 통합

이 문서에서는 azure [SignalR Service](../azure-signalr/signalr-overview.md)와 Azure Digital twins를 통합 하는 방법에 대해 알아봅니다.

이 문서에서 설명 하는 솔루션을 사용 하면 단일 웹 페이지 또는 모바일 응용 프로그램과 같은 연결 된 클라이언트에 디지털 쌍 원격 분석 데이터를 푸시할 수 있습니다. 결과적으로 클라이언트는 서버를 폴링하고 업데이트에 대 한 새 HTTP 요청을 제출할 필요 없이 IoT 장치에서 실시간 메트릭 및 상태로 업데이트 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

계속 하기 전에 완료 해야 하는 필수 구성 요소는 다음과 같습니다.

* 이 문서에서는 솔루션을 Azure SignalR service와 통합 하기 전에 Azure Digital Twins [_**자습서: 종단 간 솔루션 연결**_](tutorial-end-to-end.md)을 완료 해야 합니다. 이 자습서에서는 디지털 쌍 업데이트를 트리거하는 가상 IoT 장치와 작동 하는 Azure Digital Twins 인스턴스를 설정 하는 과정을 안내 합니다. 이 방법은 Azure SignalR Service를 사용 하 여 이러한 업데이트를 샘플 웹 앱에 연결 합니다.
    - 자습서에서 만든 **event grid 토픽** 의 이름이 필요 합니다.
* 컴퓨터에 [**Node.js**](https://nodejs.org/) 설치 되어 있어야 합니다.

계속 해 서 Azure 계정으로 [Azure Portal](https://portal.azure.com/) 에 로그인 할 수도 있습니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

아래 경로를 통해 azure SignalR Service를 Azure Digital Twins에 연결 합니다. 다이어그램의 A, B 및 C 섹션은 [종단 간 자습서 필수 구성](tutorial-end-to-end.md)요소의 아키텍처 다이어그램에서 가져옵니다. 이 방법에서는 섹션 D를 추가 하 여이를 빌드합니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>샘플 응용 프로그램 다운로드

먼저 필요한 샘플 앱을 다운로드 합니다. 다음 두 가지가 모두 필요 합니다.
* [**Azure Digital twins 샘플**](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/):이 샘플은 Azure Digital twins 인스턴스를 중심으로 데이터를 이동 하기 위한 두 개의 azure 기능을 보유 하는 *AdtSampleApp* 을 포함 합니다. [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md)에서이 시나리오에 대해 자세히 알아볼 수 있습니다. 또한 IoT 장치를 시뮬레이션 하 고 초 마다 새 온도 값을 생성 하는 *DeviceSimulator* 샘플 응용 프로그램이 포함 되어 있습니다. 
    - 샘플 링크로 이동 하 고 *ZIP 다운로드* 단추를 클릭 하 _**Azure_Digital_Twins_samples.zip**_ 하 여 샘플의 복사본을 컴퓨터에 다운로드 합니다. 폴더의 압축을 풉니다.
* [**SignalR integration 웹 앱 샘플**](https://docs.microsoft.com/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): azure SignalR 서비스에서 Azure Digital twins 원격 분석 데이터를 사용 하는 샘플 반응 웹 앱입니다.
    -  샘플 링크로 이동 하 고 *ZIP 다운로드* 단추를 클릭 하 _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_ 하 여 샘플의 복사본을 컴퓨터에 다운로드 합니다. 폴더의 압축을 풉니다.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

다음 섹션에서 다시 사용할 수 있으므로 브라우저 창을 Azure Portal에 열어 둡니다.

## <a name="configure-and-run-the-azure-functions-app"></a>Azure Functions 앱 구성 및 실행

이 섹션에서는 두 개의 Azure 함수를 설정 합니다.
* **negotiate** -HTTP 트리거 함수입니다. *SignalRConnectionInfo* 입력 바인딩을 사용 하 여 유효한 연결 정보를 생성 하 고 반환 합니다.
* **브로드캐스트** - [Event Grid](../event-grid/overview.md) 트리거 함수입니다. Event grid를 통해 Azure Digital Twins 원격 분석 데이터를 수신 하 고 이전 단계에서 만든 *SignalR* 인스턴스의 출력 바인딩을 사용 하 여 연결 된 모든 클라이언트 응용 프로그램에 메시지를 브로드캐스트합니다.

먼저 Azure Portal 열리는 브라우저로 이동 하 고, 다음 단계를 수행 하 여 설정한 SignalR 인스턴스에 대 한 **연결 문자열** 을 가져옵니다. 함수를 구성 하는 데 필요 합니다.
1. 이전에 배포한 SignalR 서비스 인스턴스가 성공적으로 만들어졌는지 확인 합니다. 포털 맨 위에 있는 검색 상자에서 이름을 검색 하 여이 작업을 수행할 수 있습니다. 인스턴스를 선택하여 엽니다.

1. 인스턴스 메뉴에서 **키** 를 선택 하 여 SignalR 서비스 인스턴스에 대 한 연결 문자열을 확인 합니다.

1. 아이콘을 선택 하 여 기본 연결 문자열을 복사 합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

그런 다음 Visual Studio (또는 원하는 다른 코드 편집기)를 시작 하 고 *Azure_Digital_Twins_samples > ADTSampleApp* 폴더에서 코드 솔루션을 엽니다. 그런 다음 함수를 만들려면 다음 단계를 수행 합니다.

1. *SampleFunctionsApp* 프로젝트에서 **SignalRFunctions.cs** 라는 새 c # sharp 클래스를 만듭니다.

1. 클래스 파일의 내용을 다음 코드로 바꿉니다.

    ```C#
    using System;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System.Collections.Generic;
    
    namespace SampleFunctionsApp
    {
        public static class SignalRFunctions
        {
            public static double temperature;
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo GetSignalRInfo(
                [HttpTrigger(AuthorizationLevel.Anonymous, "post")] HttpRequest req,
                [SignalRConnectionInfo(HubName = "dttelemetry")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static Task SendMessage(
                [EventGridTrigger] EventGridEvent eventGridEvent,
                [SignalR(HubName = "dttelemetry")] IAsyncCollector<SignalRMessage> signalRMessages,
                ILogger log)
            {
                JObject eventGridData = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
    
                log.LogInformation($"Event grid message: {eventGridData}");
    
                var patch = (JObject)eventGridData["data"]["patch"][0];
                if (patch["path"].ToString().Contains("/Temperature"))
                {
                    temperature = Math.Round(patch["value"].ToObject<double>(), 2);
                }
    
                var message = new Dictionary<object, object>
                {
                    { "temperatureInFahrenheit", temperature},
                };
        
                return signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { message }
                    });
            }
        }
    }
    ```

1. Visual Studio의 *패키지 관리자 콘솔* 창 또는 *Azure_Digital_Twins_samples \adtsampleapp\samplefunctionsapp* 폴더에 있는 컴퓨터의 명령 창에서 다음 명령을 실행 하 여 `SignalRService` NuGet 패키지를 프로젝트에 설치 합니다.
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    이렇게 하면 클래스의 모든 종속성 문제가 해결 됩니다.

다음으로 *종단 간 솔루션 연결* 자습서의 [ *앱 게시* 섹션](tutorial-end-to-end.md#publish-the-app) 에 설명 된 단계를 사용 하 여 함수를 Azure에 게시 합니다. 종단 간 자습서 필수 구성 요소에서 사용한 것과 동일한 app service/함수 앱에 게시 하거나 새 자습서를 만들 수 있지만 중복을 최소화 하기 위해 동일한 앱을 사용 하는 것이 좋습니다. 또한 다음 단계를 사용 하 여 앱 게시를 완료 합니다.
1. *Negotiate* 함수의 **HTTP 끝점 URL**을 수집 합니다. 이렇게 하려면 Azure Portal의 [함수 앱](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) 페이지로 이동 하 여 목록에서 함수 앱을 선택 합니다. 앱 메뉴에서 *함수* 를 선택 하 고 *negotiate* 함수를 선택 합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다.":::

    *함수 URL 가져오기* 를 누르고/api를 통해 값을 복사 합니다 ** _/api_ (last _/negotiate_를 포함 하지 않음)**. 이는 나중에 사용 합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다.":::

1. 마지막으로 다음 Azure CLI 명령을 사용 하 여 이전의 Azure SignalR **연결 문자열** 을 함수의 앱 설정에 추가 합니다. 이 명령은 [Azure Cloud Shell](https://shell.azure.com)에서 실행 하거나, [컴퓨터에 Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)되어 있는 경우 로컬로 실행할 수 있습니다.
 
    ```azurecli
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    이 명령의 출력은 Azure function에 설정 된 모든 앱 설정을 인쇄 합니다. `AzureSignalRConnectionString`목록의 맨 아래에서를 찾아 추가 되었는지 확인 합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다.":::

#### <a name="connect-the-function-to-event-grid"></a>Event Grid에 함수 연결

다음으로, [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md) 필수 구성 요소에서 만든 **event grid 토픽** 에 *브로드캐스트* Azure 함수를 구독 합니다. 이렇게 하면 원격 분석 데이터가 event grid 토픽을 통해 *thermostat67* 쌍에서 함수로 전달 되어 모든 클라이언트에 브로드캐스트할 수 있습니다.

이렇게 하려면 Event Grid 토픽에서 *브로드캐스트* Azure 함수에 끝점으로 **Event Grid 구독** 을 만듭니다.

[Azure Portal](https://portal.azure.com/)의 위쪽 검색 창에서 해당 이름을 검색하여 이벤트 그리드 토픽으로 이동합니다. *+ 이벤트 구독*을 선택합니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다.":::

*이벤트 구독 만들기* 페이지에서 다음과 같이 필드를 입력합니다(기본적으로 채워진 필드는 언급되지 않음).
* *이벤트 구독 정보* > **이름**: 이벤트 구독 이름을 지정합니다.
* *엔드포인트 정보* > **엔드포인트 유형**: 메뉴 옵션에서 *Azure 함수*를 선택합니다.
* *엔드포인트 정보* > **엔드포인트**: *엔드포인트 선택* 링크를 누릅니다. 그러면 *Azure 함수 선택* 창이 열립니다.
    - **구독**, **리소스 그룹**, **함수 앱** 및 **함수** (*브로드캐스트*)를 입력 합니다. 구독을 선택하면 이 중 일부가 자동으로 채워질 수 있습니다.
    - **선택 확인**을 누릅니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다.":::

*이벤트 구독 만들기* 페이지에서 **만들기**를 누릅니다.

## <a name="configure-and-run-the-web-app"></a>웹 앱 구성 및 실행

이 섹션에서는 결과를 작업에 표시 합니다. 먼저 Azure Digital Twins 인스턴스를 통해 원격 분석 데이터를 전송 하는 **시뮬레이션 된 장치 샘플 앱** 을 시작 합니다. 그런 다음 설정 된 Azure SignalR flow에 연결 하도록 **샘플 클라이언트 웹 앱** 을 구성 합니다. 그런 다음 샘플 웹 앱을 실시간으로 업데이트 하는 데이터를 볼 수 있어야 합니다.

### <a name="run-the-device-simulator"></a>장치 시뮬레이터 실행

종단 간 자습서 필수 구성 요소에서 IoT Hub 및 Azure Digital Twins 인스턴스로 데이터를 보내도록 [장치 시뮬레이터를 구성](tutorial-end-to-end.md#configure-and-run-the-simulation) 했습니다.

이제 *Azure_Digital_Twins_samples > DeviceSimulator > DeviceSimulator*에 있는 시뮬레이터 프로젝트를 시작 해야 합니다. Visual Studio를 사용 하는 경우 프로젝트를 열고 도구 모음에서이 단추를 사용 하 여 실행할 수 있습니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다.":::

콘솔 창이 열리고 시뮬레이션된 온도 원격 분석 메시지가 표시됩니다. 이러한 인스턴스는 azure Digital Twins 인스턴스를 통해 전송 되며, 여기서 Azure 함수 및 SignalR에서 선택 됩니다.

이 콘솔에서 다른 작업을 수행할 필요는 없지만 다음 단계를 완료하는 동안 콘솔을 실행 상태로 둡니다.

### <a name="configure-the-sample-client-web-app"></a>샘플 클라이언트 웹 앱 구성

다음 단계를 수행 하 여 **SignalR integration 웹 앱 샘플** 을 설정 합니다.
1. 원하는 Visual Studio 또는 코드 편집기를 사용 하 여 [*전제 조건*](#prerequisites) 섹션에서 다운로드 한 압축을 푼 _**Azure_Digital_Twins_SignalR_integration_web_app_sample**_ 폴더를 엽니다.

1. *Src/App.js* 파일을 열고에서 URL을 `HubConnectionBuilder` 이전에 저장 한 **NEGOTIATE** 함수의 HTTP 끝점 url로 바꿉니다.

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. Visual Studio의 *개발자 명령 프롬프트* 또는 컴퓨터의 모든 명령 창에서 *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* 폴더로 이동 합니다. 다음 명령을 실행 하 여 종속 노드 패키지를 설치 합니다.

    ```cmd
    npm install
    ```

다음으로 Azure Portal의 함수 앱에서 사용 권한을 설정 합니다.
1. Azure Portal의 [함수 앱](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) 페이지에서 함수 앱 인스턴스를 선택 합니다.
1. 인스턴스 메뉴에서 아래로 스크롤하고 *CORS*를 선택 합니다. CORS 페이지에서를 `http://localhost:3000` 빈 상자에 입력 하 여 허용 된 원본으로 추가 합니다. *액세스 허용-자격 증명 사용* 확인란을 선택 하 고 *저장*을 누릅니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다.":::

### <a name="see-the-results"></a>결과 보기

작업의 결과를 확인 하려면 **SignalR 통합 웹 앱 샘플**을 시작 합니다. 다음 명령을 실행 하 여 *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* 위치의 모든 콘솔 창에서이 작업을 수행할 수 있습니다.

```cmd
npm start
```

그러면 샘플 앱이 실행 되는 브라우저 창이 열리며이는 시각적 온도 계기를 표시 합니다. 앱이 실행 되 면 웹 앱에서 실시간으로 반영 하는 Azure 디지털 쌍을 통해 전파 되는 장치 시뮬레이터의 온도 원격 분석 값이 표시 되기 시작 해야 합니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="종단 간 시나리오의 Azure 서비스 뷰입니다. Azure 함수 (화살표 B)를 통해 azure 디지털 쌍 인스턴스 (섹션 A)로 이동한 다음, 처리를 위해 다른 Azure 함수 (화살표 C)로 Event Grid 하 여 장치에서 IoT Hub로 흐르는 데이터를 나타냅니다. D 섹션에는 동일한 Event Grid에서 이동 하는 데이터가 표시 됩니다. ' 브로드캐스트 '는 ' negotiate ' 레이블이 지정 된 다른 Azure 함수와 통신 하며, ' 브로드캐스트 ' 및 ' 협상 '은 모두 컴퓨터 장치와 통신 합니다.":::

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 더 이상 필요 하지 않은 경우 다음 단계에 따라 삭제 합니다. 

Azure Cloud Shell 또는 로컬 Azure CLI를 사용 하 여 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용 하 여 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 리소스 그룹을 제거 하면 제거 됩니다.
* Azure Digital Twins 인스턴스 (종단 간 자습서에서)
* IoT hub 및 허브 장치 등록 (종단 간 자습서에서)
* event grid 토픽 및 관련 구독
* 3 개의 함수 및 저장소와 같은 관련 리소스를 모두 포함 하는 Azure Functions 앱
* Azure SignalR 인스턴스

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

```azurecli
az group delete --name <your-resource-group>
```

Azure Digital Twins 인스턴스를 삭제 하는 경우 다음 명령을 사용 하 여 종단 간 자습서에서 만든 Azure AD 앱 등록을 삭제할 수도 있습니다.

```azurecli
az ad app delete --id <your-application-ID>
```

마지막으로, 로컬 컴퓨터에 다운로드 한 프로젝트 샘플 폴더 (*Azure_Digital_Twins_samples.zip* 및 *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*)를 삭제 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure SignalR를 사용 하 여 azure 함수를 설정 하 여 Azure Digital Twins 원격 분석 이벤트를 샘플 클라이언트 응용 프로그램에 브로드캐스트합니다.

다음으로, Azure SignalR Service에 대해 자세히 알아보세요.
* [*Azure SignalR Service란?*](../azure-signalr/signalr-overview.md)

또는 Azure Functions를 사용 하 여 Azure SignalR Service 인증에 대해 자세히 알아보세요.
* [*Azure SignalR Service 인증*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)
