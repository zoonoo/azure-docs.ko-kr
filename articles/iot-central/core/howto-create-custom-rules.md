---
title: 사용자 지정 규칙 및 알림으로 Azure IoT Central 확장 | 마이크로 소프트 문서
description: 솔루션 개발자는 장치가 원격 분석 전송을 중지할 때 전자 메일 알림을 보내도록 IoT Central 응용 프로그램을 구성합니다. 이 솔루션은 Azure 스트림 분석, Azure 함수 및 SendGrid를 사용합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158149"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>스트림 분석, Azure 기능 및 SendGrid를 사용하여 사용자 지정 규칙으로 Azure IoT Central 확장



이 방법 가이드는 솔루션 개발자로서 사용자 지정 규칙 및 알림을 사용하여 IoT Central 응용 프로그램을 확장하는 방법을 보여 줍니다. 이 예제에서는 장치가 원격 분석 전송을 중지할 때 운영자에게 알림을 보내는 것을 보여 주었습니다. 이 솔루션은 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) 쿼리를 사용하여 장치가 원격 분석 전송을 중지한 시기를 검색합니다. 스트림 분석 작업은 [Azure 함수를](https://docs.microsoft.com/azure/azure-functions/) 사용하여 [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)를 사용하여 알림 이메일을 보냅니다.

이 방법 가이드에서는 IoT Central이 기본 제공 규칙 및 작업으로 이미 수행할 수 있는 것 이상으로 확장하는 방법을 보여 주었습니다.

이 방법 가이드에서는 다음 방법을 배웁니다.

* *연속 데이터 내보내기를*사용하여 IoT Central 응용 프로그램에서 원격 분석을 스트리밍합니다.
* 기기가 데이터 전송을 중지한 시기를 감지하는 스트림 분석 쿼리를 만듭니다.
* Azure 함수 및 SendGrid 서비스를 사용하여 전자 메일 알림을 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="iot-central-application"></a>IoT Central 애플리케이션

Azure IoT Central 응용 프로그램 관리자 웹 사이트에서 다음 설정을 사용하여 [IoT 중앙 응용 프로그램을](https://aka.ms/iotcentral) 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 요금제 | Standard |
| 애플리케이션 템플릿 | 매장 내 분석 - 상태 모니터링 |
| 애플리케이션 이름 | 기본값 수락 또는 사용자 고유의 이름 선택 |
| URL | 기본값 수락 또는 고유한 URL 접두사 선택 |
| 디렉터리 | Azure Active 디렉터리 테넌트 |
| Azure 구독 | Azure 구독 |
| 지역 | 가장 가까운 지역 |

이 문서의 예제 및 스크린샷은 **미국** 지역을 사용합니다. 가까운 위치를 선택하고 동일한 지역에 있는 모든 리소스를 만들어야 합니다.

이 응용 프로그램 템플릿에는 원격 분석을 보내는 두 개의 시뮬레이션된 온도 조절 장치가 포함되어 있습니다.

### <a name="resource-group"></a>Resource group

Azure [포털을](https://portal.azure.com/#create/Microsoft.ResourceGroup) 사용하여 만드는 다른 리소스를 포함하도록 **DetectStoppedDevices라는** 리소스 그룹을 만듭니다. IoT Central 응용 프로그램과 동일한 위치에 Azure 리소스를 만듭니다.

### <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스

Azure 포털을 사용하여 다음 설정을 사용하여 [이벤트 허브 네임스페이스를 만듭니다.](https://portal.azure.com/#create/Microsoft.EventHub)

| 설정 | 값 |
| ------- | ----- |
| 이름    | 네임스페이스 이름 선택 |
| 가격 책정 계층 | Basic |
| Subscription | 사용자의 구독 |
| Resource group | 감지 중지장치 |
| 위치 | 미국 동부 |
| 처리량 단위 | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics 작업

Azure [포털을](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) 사용하여 다음 설정을 사용하여 스트림 분석 작업을 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 이름    | 작업 이름 선택 |
| Subscription | 사용자의 구독 |
| Resource group | 감지 중지장치 |
| 위치 | 미국 동부 |
| 호스팅 환경 | 클라우드 |
| 스트리밍 단위 | 3 |

### <a name="function-app"></a>함수 앱

Azure [포털을](https://portal.azure.com/#create/Microsoft.FunctionApp) 사용하여 다음 설정을 사용하여 함수 앱을 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 앱 이름    | 함수 앱 이름 선택 |
| Subscription | 사용자의 구독 |
| Resource group | 감지 중지장치 |
| OS | Windows |
| 호스팅 계획 | 소비 계획 |
| 위치 | 미국 동부 |
| 런타임 스택 | .NET |
| 스토리지 | 새로 만들기 |

### <a name="sendgrid-account"></a>센드그리드 계정

Azure 포털을 사용하여 다음 설정을 사용하여 [SendGrid 계정을 만듭니다.](https://portal.azure.com/#create/Sendgrid.sendgrid)

| 설정 | 값 |
| ------- | ----- |
| 이름    | SendGrid 계정 이름 선택 |
| 암호 | 암호 만들기 |
| Subscription | 사용자의 구독 |
| Resource group | 감지 중지장치 |
| 가격 책정 계층 | F1 무료 |
| 연락처 정보 | 필요한 정보 작성 |

필요한 모든 리소스를 만든 경우 **DetectStoppedDevices** 리소스 그룹은 다음과 같은 스크린샷과 같습니다.

![중지된 장치 리소스 그룹 검색](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이벤트 허브로 원격 분석을 지속적으로 내보내도록 IoT Central 응용 프로그램을 구성할 수 있습니다. 이 섹션에서는 IoT Central 응용 프로그램에서 원격 분석을 수신하는 이벤트 허브를 만듭니다. 이벤트 허브는 처리를 위해 스트림 애널리틱스 작업에 원격 분석을 제공합니다.

1. Azure 포털에서 이벤트 허브 네임스페이스로 이동하여 **+ 이벤트 허브**를 선택합니다.
1. 이벤트 허브의 이름을 **중앙내보내기에**이름을 지정하고 **만들기를**선택합니다.

이벤트 허브 네임스페이스는 다음과 같은 스크린샷과 같습니다.

![Event Hubs 네임스페이스](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid API 키 받기

함수 앱에는 이메일 메시지를 보내려면 SendGrid API 키가 필요합니다. SendGrid API 키를 만들려면 다음을 수행하십시오.

1. Azure 포털에서 SendGrid 계정으로 이동합니다. 그런 다음 SendGrid 계정에 액세스하려면 **관리를** 선택합니다.
1. SendGrid 계정에서 **설정,** API **키를**선택합니다. **API 키 만들기**선택:

    ![SendGrid API 키 만들기](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. API **키 만들기** 페이지에서 **전체 액세스** 권한을 사용하여 **AzureFunctionAccess라는** 키를 만듭니다.
1. API 키를 기록하면 함수 앱을 구성할 때 API 키가 필요합니다.

## <a name="define-the-function"></a>함수 정의

이 솔루션은 Azure Functions 앱을 사용하여 Stream Analytics 작업이 중지된 장치를 검색할 때 전자 메일 알림을 보냅니다. 함수 앱을 만들려면 다음을 수행합니다.

1. Azure 포털에서 **DetectStoppedDevices** 리소스 그룹의 **앱 서비스** 인스턴스로 이동합니다.
1. 새 **+** 함수를 만들려면 선택합니다.
1. 개발 **환경 선택** 페이지에서 **포털 에서** 를 선택한 다음 **계속을**선택합니다.
1. 함수 **만들기** 페이지에서 **Webhook + API를** 선택한 다음 **을 선택합니다.**

포털은 **HttpTrigger1이라는**기본 함수를 만듭니다.

![기본 HTTP 트리거 기능](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>함수 바인딩 구성

SendGrid를 사용하여 이메일을 보내려면 다음과 같이 함수에 대한 바인딩을 구성해야 합니다.

1. **통합을**선택하고 출력 **HTTP($return)를**선택한 다음 **삭제를**선택합니다.
1. **+ 새 출력을**선택한 다음 **SendGrid를**선택한 다음 **을 선택합니다.** SendGrid 확장을 설치하려면 **설치를** 선택합니다.
1. 설치가 완료되면 함수 **반환 값 사용을**선택합니다. 유효한 보낸 전화 **주소를** 추가하여 이메일 알림을 수신합니다.  유효한 **보낸 보낸 주소를** 추가하여 이메일 발신자로 사용할 수 있습니다.
1. **SendGrid API 키 앱 설정**옆에 있는 **새** 옵션을 선택합니다. **SendGridAPIKey를** 키로 입력하고 이전에 값으로 언급한 SendGrid API 키를 입력합니다. 그런 다음 **을 선택합니다.**
1. 함수에 대한 SendGrid 바인딩을 저장하려면 **저장을** 선택합니다.

통합 설정은 다음과 같은 스크린샷과 같습니다.

![함수 앱 통합](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>함수 코드 추가

함수를 구현하려면 C# 코드를 추가하여 들어오는 HTTP 요청을 구문 분석하고 다음과 같이 전자 메일을 보냅니다.

1. 함수 앱에서 **HttpTrigger1** 함수를 선택하고 C# 코드를 다음 코드로 바꿉니다.

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    새 코드를 저장할 때까지 오류 메시지가 표시될 수 있습니다.

1. 함수를 저장하려면 **저장을** 선택합니다.

### <a name="test-the-function-works"></a>함수 작동 테스트

포털에서 함수를 테스트하려면 먼저 코드 편집기 하단의 **로그를 선택합니다.** 그런 다음 코드 편집기 오른쪽에서 **테스트를** 선택합니다. 다음 JSON을 **요청 본문으로**사용합니다.

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

함수 로그 메시지는 **로그** 패널에 나타납니다.

![함수 로그 출력](media/howto-create-custom-rules/function-app-logs.png)

몇 분 후 **To** 전자 메일 주소는 다음과 같은 내용이 있는 전자 메일을 받습니다.

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>스트림 분석 쿼리 추가

이 솔루션은 Stream Analytics 쿼리를 사용하여 디바이스가 120초 이상 원격 분석 전송을 중지하는 시기를 감지합니다. 쿼리는 이벤트 허브의 원격 분석을 입력으로 사용합니다. 작업은 쿼리 결과를 함수 앱으로 보냅니다. 이 섹션에서는 분석 스트림 작업을 구성합니다.

1. Azure 포털에서 스트림 분석 작업으로 이동, **작업 토폴로지** 선택 **입력에서 입력을**선택 , **+ 스트림 입력 추가**를 선택 합니다. **Event Hub**
1. 다음 표의 정보를 사용하여 이전에 만든 이벤트 허브를 사용하여 입력을 구성한 다음 **저장을**선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 입력 별칭 | 중앙 텔레텔레메타 |
    | Subscription | 사용자의 구독 |
    | 이벤트 허브 네임스페이스 | 이벤트 허브 네임스페이스 |
    | 이벤트 허브 이름 | 기존 사용 - **중앙 내보내기** |

1. **작업 토폴로지에서** **출력을**선택하고 **+ 추가**를 선택한 다음 **Azure 함수를**선택합니다.
1. 다음 표의 정보를 사용하여 출력을 구성한 다음 **저장을**선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 출력 별칭 | 이메일 알림 |
    | Subscription | 사용자의 구독 |
    | 함수 앱 | 함수 앱 |
    | 함수  | Http트리거1 |

1. **작업 토폴로지에서** **쿼리를** 선택하고 기존 쿼리를 다음 SQL로 바꿉습니다.

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. **저장**을 선택합니다.
1. 분석 스트림 작업을 시작하려면 **개요를**선택한 다음 **시작**한 다음 **지금**, **다음을 시작합니다.**

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>IoT Central에서 내보내기 구성

Azure [IoT 중앙 응용 프로그램 관리자](https://aka.ms/iotcentral) 웹 사이트에서 Contoso 템플릿에서 만든 IoT 중앙 응용 프로그램으로 이동합니다. 이 섹션에서는 시뮬레이션된 장치에서 이벤트 허브로 원격 분석을 스트리밍하도록 응용 프로그램을 구성합니다. 내보내기를 구성하려면 다음을 수행하십시오.

1. **데이터 내보내기** 페이지로 **이동하여 + 새**를 선택한 다음 Azure 이벤트 **허브를 선택합니다.**
1. 다음 설정을 사용하여 내보내기를 구성한 다음 **저장을**선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 표시 이름 | 이벤트 허브로 내보내기 |
    | 사용 | 설정 |
    | Event Hubs 네임스페이스 | 이벤트 허브 네임스페이스 이름 |
    | 이벤트 허브 | 중앙 수출 |
    | 측정값 | 설정 |
    | 디바이스 | 꺼짐 |
    | 디바이스 템플릿 | 꺼짐 |

![연속 데이터 내보내기 구성](media/howto-create-custom-rules/cde-configuration.png)

계속하기 전에 내보내기 상태가 **실행 중일** 때까지 기다립니다.

## <a name="test"></a>테스트

솔루션을 테스트하려면 IoT Central에서 시뮬레이트된 중지된 장치로의 연속 데이터 내보내기를 비활성화할 수 있습니다.

1. IoT 중앙 응용 프로그램에서 **데이터 내보내기** 페이지로 이동하여 **이벤트 허브내보내기** 구성을 선택합니다.
1. **사용하도록** 설정 **해제하고** **저장을**선택합니다.
1. 2분 이상 이후에 **받는 사람** 이메일 주소는 다음 예제 콘텐츠와 같은 하나 이상의 전자 메일을 수신합니다.

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>정리

이 방법 이후에 정리하고 불필요한 비용을 방지하려면 Azure 포털에서 **DetectStoppedDevices** 리소스 그룹을 삭제합니다.

응용 프로그램 내의 **관리** 페이지에서 IoT 중앙 응용 프로그램을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

* *연속 데이터 내보내기를*사용하여 IoT Central 응용 프로그램에서 원격 분석을 스트리밍합니다.
* 기기가 데이터 전송을 중지한 시기를 감지하는 스트림 분석 쿼리를 만듭니다.
* Azure 함수 및 SendGrid 서비스를 사용하여 전자 메일 알림을 보냅니다.

이제 사용자 지정 규칙 및 알림을 만드는 방법을 배웠으니 다음 단계는 사용자 지정 분석을 사용하여 [Azure IoT Central을 확장하는](howto-create-custom-analytics.md)방법을 알아보는 것입니다.
