---
title: 사용자 지정 규칙 및 알림을 사용하여 Azure IoT Central 확장 | Microsoft Docs
description: 솔루션 개발자로서 디바이스에서 원격 분석 전송을 중지할 때 이메일로 알림을 보내도록 IoT Central 애플리케이션을 구성합니다. 이 솔루션은 Azure Stream Analytics, Azure Functions 및 SendGrid를 사용합니다.
author: philmea
ms.author: philmea
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 2eb776add1e4a53edc6f4f57983074af31d8f52d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108750044"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Stream Analytics, Azure Functions 및 SendGrid를 사용하여 사용자 지정 규칙으로 Azure IoT Central 확장

이 방법 가이드에서는 사용자 지정 규칙 및 알림을 사용하여 IoT Central 애플리케이션을 확장하는 방법을 보여줍니다. 이 예에서는 디바이스가 원격 분석 전송을 중지할 때 운영자에게 알림을 보내는 방법을 보여 줍니다. 솔루션은 [Azure Stream Analytics](../../stream-analytics/index.yml) 쿼리를 사용하여 디바이스에서 원격 분석 보내기가 중지된 시기를 검색합니다. Stream Analytics 작업은 [Azure Functions](../../azure-functions/index.yml)를 사용하여 [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)로 이메일 알림을 보냅니다.

이 방법 가이드에서는 기본 제공 규칙 및 작업으로 이미 수행할 수 있는 작업 이상으로 IoT Central 확장하는 방법을 보여 줍니다.

이 방법 가이드에서 배울 내용은 다음과 같습니다.

* *연속 데이터 내보내기* 를 사용하여 IoT Central 애플리케이션에서 원격 분석을 스트림합니다.
* 디바이스가 데이터 전송을 중지한 경우를 검색하는 Stream Analytics 쿼리를 만듭니다.
* Azure Functions 및 SendGrid 서비스를 사용하여 이메일 알림을 보냅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="iot-central-application"></a>IoT Central 애플리케이션

다음 설정을 사용하여 [Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 애플리케이션을 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 요금제 | Standard |
| 애플리케이션 템플릿 | 스토어 내 분석 - 조건 모니터링 |
| 애플리케이션 이름 | 기본값을 그대로 적용하거나 고유한 이름 선택 |
| URL | 기본값을 그대로 적용하거나 고유한 URL 접두사 선택 |
| 디렉터리 | Azure Active Directory 테넌트 |
| Azure 구독 | Azure 구독 |
| 지역 | 가장 가까운 지역 |

이 문서의 예제와 스크린샷은 **미국** 지역을 사용합니다. 가까운 위치를 선택하고 동일한 지역에 모든 리소스를 만들어 두어야 합니다.

이 애플리케이션 템플릿에는 원격 분석을 보내는 시뮬레이션된 자동 온도 조절 디바이스 두 개가 포함되어 있습니다.

### <a name="resource-group"></a>Resource group

만들어진 기타 리소스를 포함하는 **DetectStoppedDevices** 라는 [리소스 그룹을 만드는 데 Azure Portal을 사용](https://portal.azure.com/#create/Microsoft.ResourceGroup)합니다. IoT Central 애플리케이션과 동일한 위치에 Azure 리소스를 만듭니다.

### <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스

[Azure Portal를 사용하여 Event Hubs 네임스페이스를 만들](https://portal.azure.com/#create/Microsoft.EventHub) 때 다음 설정을 따릅니다.

| 설정 | 값 |
| ------- | ----- |
| Name    | 네임스페이스 이름 선택 |
| 가격 책정 계층 | Basic |
| Subscription | 사용자의 구독 |
| Resource group | DetectStoppedDevices |
| 위치 | 미국 동부 |
| 처리량 단위 | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics 작업

[Azure Portal를 사용하여 Stream Analytics 작업을 만들](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) 때 다음 설정을 따릅니다.

| 설정 | 값 |
| ------- | ----- |
| Name    | 작업 이름 선택 |
| Subscription | 사용자의 구독 |
| Resource group | DetectStoppedDevices |
| 위치 | 미국 동부 |
| 호스팅 환경 | 클라우드 |
| 스트리밍 단위 | 3 |

### <a name="function-app"></a>함수 앱

[Azure Portal을 사용하여 함수 앱을 만들](https://portal.azure.com/#create/Microsoft.FunctionApp) 때 다음 설정을 따릅니다.

| 설정 | 값 |
| ------- | ----- |
| 앱 이름    | 함수 앱 이름 선택 |
| Subscription | 사용자의 구독 |
| Resource group | DetectStoppedDevices |
| OS | Windows |
| 호스팅 계획 | 소비 계획 |
| 위치 | 미국 동부 |
| 런타임 스택 | .NET |
| 스토리지 | 새로 만들기 |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid 계정 및 API 키

Sendgrid 계정이 없는 경우 시작하기 전에 [체험 계정](https://app.sendgrid.com/)을 만듭니다.

1. 왼쪽 메뉴의 Sendgrid 대시보드 설정에서 **API 키** 를 선택합니다.
1. **API 키 만들기** 를 클릭합니다.
1. 새 API 키의 이름을 **AzureFunctionAccess** 로 입력합니다.
1. **만들기 및 보기** 를 클릭합니다.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Create SendGrid API 키의 스크린샷":::

그런 다음 API 키가 제공됩니다. 이 이름은 나중에 사용되므로 저장합니다.

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

IoT Central 애플리케이션을 구성하여 원격 분석을 이벤트 허브로 내보낼 수 있습니다. 이 섹션에서는 IoT Central 애플리케이션에서 원격 분석을 수신하는 이벤트 허브를 만듭니다. 이벤트 허브는 프로세스를 위해 원격 분석을 Stream Analytics 작업에 전달합니다.

1. Azure Portal에서 Event Hubs 네임스페이스로 이동하고 **+이벤트 허브** 를 선택합니다.
1. 이벤트 허브의 이름을 **centralexport** 로 선택하고 **만들기** 를 선택합니다.

Event Hubs 네임스페이스는 다음 스크린샷과 같습니다. 

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
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

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

함수 로그 메시지는 **로그** 패널에 표시됩니다.

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics 쿼리 추가

이 솔루션은 Stream Analytics 쿼리를 사용하여 디바이스가 120초 넘게 원격 분석 전송을 중지할 때를 검색합니다. 쿼리에서는 이벤트 허브의 원격 분석을 입력으로 사용합니다. 작업은 함수 앱에 쿼리 결과를 보냅니다. 이 섹션에서는 Azure Stream Analytics 작업을 만듭니다.

1. Azure Portal에서 Stream Analytics 작업으로 이동하여 **작업 토폴로지** 에서 **입력** 을 선택하고 **+스트림 입력 추가** 를 선택한 다음 **이벤트 허브** 를 선택합니다.
1. 이전에 만든 이벤트 허브를 사용하여 입력을 구성하려면 다음 표의 정보를 사용하고 **저장** 을 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 입력 별칭 | centraltelemetry |
    | Subscription | 사용자의 구독 |
    | 이벤트 허브 네임스페이스 | 이벤트 허브 네임스페이스 |
    | 이벤트 허브 이름 | 기존 - **centralexport** 사용 |

1. **작업 토폴로지** 에서 **출력** 을 선택하고 **+추가** 를 선택한 다음, **Azure 함수** 를 선택합니다.
1. 다음 표의 정보를 사용하여 출력을 구성한 후 **저장** 을 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 출력 별칭 | emailnotification |
    | Subscription | 사용자의 구독 |
    | 함수 앱 | 함수 앱 |
    | 함수  | HttpTrigger1 |

1. **작업 토폴로지** 에서 **쿼리** 를 선택하고 기존 쿼리를 다음 SQL로 바꿉니다.

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

1. **저장** 을 선택합니다.
1. Stream Analytics 작업을 시작하려면 **개요**, **시작**, **지금** 을 차례로 선택한 다음 **시작** 을 선택합니다.

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Stream Analytics의 스크린샷":::

## <a name="configure-export-in-iot-central"></a>IoT Central에서 내보내기 구성 

[Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 사용자가 만든 IoT Central 애플리케이션으로 이동합니다.

이 섹션에서는 시뮬레이션된 디바이스에서 이벤트 허브로 원격 분석을 스트림하는 애플리케이션을 구성합니다. 내보내기를 구성하려면:

1. **데이터 내보내기** 페이지로 이동하고, **+새로 만들기** 를 선택하고, **Azure Event Hubs** 를 선택합니다.
1. 내보내기를 구성하려면 다음 설정을 사용하고 **저장** 을 선택합니다. 

    | 설정 | 값 |
    | ------- | ----- |
    | 표시 이름 | Event Hubs로 내보내기 |
    | 사용 | 켜기 |
    | 내보낼 데이터 형식 | 원격 분석 |
    | 보강 | 내보낸 데이터의 구성 방향에 따른 원하는 키/값 입력 | 
    | 대상 | 새 데이터를 만들고 데이터를 내보낼 위치에 대한 정보 입력 |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="데이터 내보내기의 스크린샷":::

계속 하기 전에 내보내기 상태가 **실행 중** 이 될 때까지 기다립니다.

## <a name="test"></a>테스트

솔루션을 테스트하려면 IoT Central에서 시뮬레이션된 중지 디바이스로 연속 데이터 내보내기를 사용하지 않도록 설정할 수 있습니다.

1. IoT Central 애플리케이션에서 **데이터 내보내기** 페이지로 이동하고 **Event Hubs로 내보내기** 를 선택하여 Event Hubs 내보내기 구성으로 이동합니다.
1. **사용** 을 **Off** 로 설정하고 **저장** 을 선택합니다.
1. 최소 2분 후, **To** 이메일 주소에 다음 예제 콘텐츠와 같은 이메일이 하나 이상 수신됩니다.

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>정리

이 가이드 후 정리를 하고 불필요한 비용이 생기는 것을 방지하려면 Azure Portal에서 **DetectStoppedDevices** 리소스 그룹을 삭제합니다.

애플리케이션 내의 **관리** 페이지에서 IoT Central 애플리케이션을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

* *연속 데이터 내보내기* 를 사용하여 IoT Central 애플리케이션에서 원격 분석을 스트림합니다.
* 디바이스가 데이터 전송을 중지한 경우를 검색하는 Stream Analytics 쿼리를 만듭니다.
* Azure Functions 및 SendGrid 서비스를 사용하여 이메일 알림을 보냅니다.

이제 사용자 지정 규칙 및 알림을 만드는 방법을 알게 되었으니 제안하는 다음 단계는 [사용자 지정 분석을 사용하여 Azure IoT Central를 확장](howto-create-custom-analytics.md)하는 방법을 배우는 것입니다.
