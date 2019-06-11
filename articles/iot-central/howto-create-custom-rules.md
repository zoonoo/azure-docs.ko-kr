---
title: 사용자 지정 규칙 및 알림을 사용 하 여 Azure IoT Central 확장 | Microsoft Docs
description: 솔루션 개발자는 장치 원격 분석 보내기를 중지 될 때 전자 메일 알림을 보내도록 IoT Central 응용 프로그램을 구성 합니다. 이 솔루션에서는 Azure Stream Analytics 및 Azure Functions를 사용 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5248b9546ffe931b72123778d0d23574e5238405
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742406"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>알림을 전송 하는 사용자 지정 규칙을 사용 하 여 Azure IoT Central 확장

이 방법 가이드에서는, 솔루션 개발자는 사용자 지정 규칙 및 알림을 사용 하 여 IoT Central 응용 프로그램을 확장 하는 방법입니다. 장치 원격 분석 보내기를 중지 될 때 운영자에 게 알림을 보내는 보여 줍니다. 솔루션은는 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) 장치 원격 분석 보내기를 중지 하는 경우를 검색 하기 위해 쿼리 합니다. Stream Analytics 작업 사용 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 사용 하 여 전자 메일 알림을 보내도록 [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)합니다.

이 방법 가이드 수행할 수 있는 이미 기본 제공 규칙 및 작업을 사용 하 여 초과 IoT Central을 확장 하는 방법을 보여 줍니다.

이 방법 가이드에 알아봅니다 방법:

* 사용 하 여 IoT Central 응용 프로그램에서 원격 분석을 Stream *연속 데이터 내보내기*합니다.
* 장치 데이터를 보내는 중지 하는 경우를 감지 하는 Stream Analytics 쿼리를 만듭니다.
* Azure Functions 및 SendGrid 서비스를 사용 하 여 전자 메일 알림을 보냅니다.

## <a name="prerequisites"></a>필수 조건

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="iot-central-application"></a>IoT Central 응용 프로그램

IoT Central 응용 프로그램에서 만들기를 [Azure IoT Central-내 응용 프로그램](https://aka.ms/iotcentral) 다음 설정으로 페이지:

| 설정 | 값 |
| ------- | ----- |
| 결제 계획 | Pay-As-You-Go |
| 애플리케이션 템플릿 | 샘플 Contoso |
| 응용 프로그램 이름 | 기본값을 적용 하거나 고유한 이름을 선택합니다 |
| URL | 기본값 또는 자신의 고유 URL 접두사를 선택 합니다. |
| 디렉터리 | Azure Active Directory 테 넌 트 |
| Azure 구독 | Azure 구독 |
| 지역 | 미국 동부 |

예제 및이 스크린샷을 사용 하 여 문서를 **미국 동부** 지역입니다. 가까운 위치를 선택 하 고 동일한 지역에 모든 리소스를 만드는 하는지 확인 합니다.

### <a name="resource-group"></a>리소스 그룹

사용 합니다 [리소스 그룹을 만들려면 Azure portal](https://portal.azure.com/#create/Microsoft.ResourceGroup) 호출 **DetectStoppedDevices** 만든 다른 리소스를 포함 하도록 합니다. IoT Central 응용 프로그램과 동일한 위치에서 Azure 리소스를 만듭니다.

### <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스

사용 된 [Event Hubs 네임 스페이스를 만들려면 Azure portal](https://portal.azure.com/#create/Microsoft.EventHub) 다음 설정을 사용 하 여:

| 설정 | 값 |
| ------- | ----- |
| 이름    | 네임 스페이스 이름을 선택합니다 |
| 가격 책정 계층 | Basic |
| 구독 | 사용자의 구독 |
| 리소스 그룹 | DetectStoppedDevices |
| 위치 | 미국 동부 |
| 처리량 단위 | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics 작업

사용 된 [Stream Analytics 작업을 만들려면 Azure portal](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) 다음 설정을 사용 하 여:

| 설정 | 값 |
| ------- | ----- |
| 이름    | 작업 이름을 선택합니다 |
| 구독 | 사용자의 구독 |
| 리소스 그룹 | DetectStoppedDevices |
| 위치 | 미국 동부 |
| 호스팅 환경 | 클라우드 |
| 스트리밍 단위 | 3 |

### <a name="function-app"></a>함수 앱

사용 된 [함수 앱을 만들려면 Azure portal](https://portal.azure.com/#create/Microsoft.FunctionApp) 다음 설정을 사용 하 여:

| 설정 | 값 |
| ------- | ----- |
| 앱 이름    | 함수 앱 이름 선택 |
| 구독 | 사용자의 구독 |
| 리소스 그룹 | DetectStoppedDevices |
| OS | Windows |
| 호스팅 계획 | 소비 계획 |
| 위치 | 미국 동부 |
| 런타임 스택 | .NET |
| Storage | 새로 만들기 |

### <a name="sendgrid-account"></a>SendGrid 계정

사용 된 [SendGrid 계정을 만들려면 Azure portal](https://portal.azure.com/#create/Sendgrid.sendgrid) 다음 설정을 사용 하 여:

| 설정 | 값 |
| ------- | ----- |
| 이름    | SendGrid 계정 이름을 선택합니다 |
| 암호 | 암호 만들기 |
| 구독 | 사용자의 구독 |
| 리소스 그룹 | DetectStoppedDevices |
| 가격 책정 계층 | F1 무료 |
| 연락처 정보 | 필수 정보 입력 |

필요한 모든 리소스를 만들었으면 하 **DetectStoppedDevices** 다음 스크린샷과 같은 리소스 그룹은:

![중지 된 장치 리소스 그룹 검색](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이벤트 허브로 원격 분석을 지속적으로 내보낼 IoT Central 응용 프로그램을 구성할 수 있습니다. 이 섹션에서는 IoT Central 응용 프로그램에서 원격 분석을 받기 위해 이벤트 허브를 만들 수 있습니다. 이벤트 허브는 처리를 위해 Stream Analytics 작업으로 원격 분석을 제공합니다.

1. Azure portal에서 Event Hubs 네임 스페이스로 이동 및 선택 **+ 이벤트 허브**합니다.
1. 이벤트 허브 이름 **centralexport**, 선택한 **만들기**합니다.

Event Hubs 네임 스페이스는 다음 스크린샷과 같습니다.

![Event Hubs 네임스페이스](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid API 키 가져오기

함수 앱에는 전자 메일 메시지를 보내려면 SendGrid API 키를 해야 합니다. SendGrid API 키를 만들려면:

1. Azure portal에서 SendGrid 계정으로 이동 합니다. 선택한 **관리** SendGrid 계정에 액세스할 수 있습니다.
1. SendGrid 계정에서 선택 **설정을**, 한 다음 **API 키**합니다. 선택할 **API 키 만들기**:

    ![SendGrid API 키 만들기](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. 에 **API 키 만들기** 페이지에서 명명 된 키를 만듭니다 **AzureFunctionAccess** 사용 하 여 **대 한 모든 권한을** 권한.
1. API 키를 기록해, 함수 앱을 구성할 때 필요 합니다.

## <a name="define-the-function"></a>함수를 정의 합니다.

이 솔루션은 Stream Analytics 작업에서 중지 된 장치를 검색 하는 경우 전자 메일 알림을 보내도록 Azure Functions 앱을 사용 합니다. 함수 앱을 만들려면:

1. Azure portal로 이동 합니다 **App Service** 의 인스턴스를 **DetectStoppedDevices** 리소스 그룹입니다.
1. 선택 **+** 새 함수를 만듭니다.
1. 에 **는 개발 환경 선택** 페이지에서 **포털 내** 선택한 후 **계속**합니다.
1. 에 **는 CREATE FUNCTION** 페이지에서 **Webhook + API** 선택한 후 **만들기**합니다.

포털을 호출 하는 기본 함수를 만듭니다 **HttpTrigger1**:

![기본 HTTP 트리거 함수](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>함수 바인딩 구성

SendGrid 사용 하 여 전자 메일을 보내려면 함수에 대 한 바인딩을 다음과 같이 구성 해야 합니다.

1. 선택 **통합**, 출력 선택 **HTTP ($return)** 를 선택한 후 **삭제**합니다.
1. 선택 **+ 새 출력**, 선택한 **SendGrid**를 선택한 후 **선택**합니다. 선택할 **설치** SendGrid 확장을 설치 합니다.
1. 설치가 완료 되 면 선택 **함수 반환 값을 사용 하 여**입니다. 유효한 추가 **주소로** 전자 메일 알림을 받을 수 있습니다.  유효한 추가 **주소의** 전자 메일 보낸 사람으로 사용 하도록 합니다.
1. 선택 **새** 옆에 **SendGrid API 키 앱 설정**합니다. 입력 **SendGridAPIKey** 키와 값으로 앞에서 설명한 SendGrid API 키입니다. 그런 다음 **만들기**를 선택합니다.
1. 선택할 **저장** 함수에 대 한 SendGrid 바인딩 저장 합니다.

통합 설정을 다음 스크린샷과 같습니다.

![함수 앱 통합](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>함수 코드를 추가 합니다.

추가 함수를 구현 하는 C# 들어오는 HTTP 요청을 구문 분석 하 고 다음과 같이 전자 메일을 보내는 코드:

1. 선택 된 **HttpTrigger1** 바꾸고 함수 앱에서 함수를 C# 다음 코드를 사용 하 여 코드:

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

    새 코드를 저장할 때까지 오류 메시지가 표시 될 수 있습니다.

1. 선택 **저장할** 함수를 저장 합니다.

### <a name="test-the-function-works"></a>함수가 작동 테스트

포털에서 함수를 테스트 하려면 먼저 선택할 **로그** 코드 편집기의 맨 아래에 있습니다. 선택한 **테스트** 코드 편집기의 오른쪽에 있습니다. 다음 JSON을 사용 합니다 **요청 본문**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

함수 로그 메시지에 표시 된 **로그** 패널:

![함수 로그 출력](media/howto-create-custom-rules/function-app-logs.png)

몇 분 후는 **를** 전자 메일 주소에 다음 내용이 포함 된 전자 메일을 받습니다.

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics 쿼리 추가

이 솔루션 120 초 이상에 대 한 원격 분석을 보내는 장치를 중지 하는 시기를 감지 하는 Stream Analytics 쿼리를 사용 합니다. 쿼리 입력으로 이벤트 허브에서 원격 분석을 사용합니다. 작업 함수 앱에는 쿼리 결과 보냅니다. 이 섹션에서는 Stream Analytics 작업을 구성할 수 있습니다.

1. Azure portal로 이동 하 여 Stream analytics 작업을 아래 **작업 토폴로지** 선택 **입력**, 선택 **+ 스트림 입력 추가**를 선택한 후 **이벤트 허브**합니다.
1. 다음 표의 정보를 사용 하 여 이전에 만든 이벤트 허브를 사용 하 여 입력을 구성 하려면 선택한 **저장할**:

    | 설정 | 값 |
    | ------- | ----- |
    | 입력 별칭 | centraltelemetry |
    | 구독 | 사용자의 구독 |
    | 이벤트 허브 네임스페이스 | 이벤트 허브 네임 스페이스 |
    | 이벤트 허브 이름 | -기존 항목 사용 **centralexport** |

1. 아래 **작업 토폴로지**를 선택 **출력**, 선택 **+ 추가**를 선택한 후 **Azure 함수**합니다.
1. 다음 표의 정보를 사용 하 여 출력을 구성 하 고 선택 **저장할**:

    | 설정 | 값 |
    | ------- | ----- |
    | 출력 별칭 | emailnotification |
    | 구독 | 사용자의 구독 |
    | 함수 앱 | 함수 앱 |
    | 함수  | HttpTrigger1 |

1. 아래 **토폴로지 작업**를 선택 **쿼리** 및 다음 SQL을 사용 하 여 기존 쿼리 바꾸기:

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
1. Stream Analytics 작업을 시작 하려면 **개요**, 한 다음 **시작**, 다음 **이제**를 차례로 **시작**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>IoT Central의 내보내기 구성

로 이동 합니다 [IoT Central 응용 프로그램](https://aka.ms/iotcentral) Contoso 템플릿에서 생성 합니다. 이 섹션에서는 event hub에 시뮬레이션 된 장치에서 원격 분석을 스트리밍하려면 응용 프로그램을 구성 합니다. 내보내기를 구성 합니다.

1. 로 이동 합니다 **연속 데이터 내보내기** 페이지에서 **+ 새로 만들기**를 차례로 **Azure Event Hubs**합니다.
1. 다음 설정을 사용 하 여 내보내기를 구성 하 고 선택 **저장할**:

    | 설정 | 값 |
    | ------- | ----- |
    | 표시 이름 | Event Hubs로 내보내기 |
    | 사용 | 켜짐 |
    | Event Hubs 네임스페이스 | Event Hubs 네임 스페이스 이름 |
    | 이벤트 허브 | centralexport |
    | 측정값 | 켜짐 |
    | 디바이스 | 꺼짐 |
    | 디바이스 템플릿 | 꺼짐 |

![연속 데이터 내보내기 구성](media/howto-create-custom-rules/cde-configuration.png)

내보내기 상태가 될 때까지 기다립니다 **실행** 계속 하기 전에 합니다.

## <a name="test"></a>테스트

솔루션을 테스트 하려면 연속 데이터 내보내기 IoT Central에서 중지 된 시뮬레이션 된 장치를 비활성화할 수 있습니다.

1. IoT Central 응용 프로그램으로 이동 합니다 **연속 데이터 내보내기** 페이지를 선택 합니다 **Event Hubs로 내보내기** 구성 내보내기.
1. 설정 **Enabled** 하 **해제** 선택한 **저장**합니다.
1. 적어도 2 분 후 합니다 **에** 콘텐츠는 다음 예제와 같이 표시 하는 하나 이상의 전자 메일을 수신 하는 전자 메일 주소:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>정리

이 방법은 후 정리 하 고 불필요 한 비용이 발생 하지 않도록 하려면 삭제 합니다 **DetectStoppedDevices** Azure portal에서 리소스 그룹입니다.

IoT Central 응용 프로그램을 삭제할 수 있습니다 합니다 **관리** 응용 프로그램 내의 페이지입니다.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

* 사용 하 여 IoT Central 응용 프로그램에서 원격 분석을 Stream *연속 데이터 내보내기*합니다.
* 장치 데이터를 보내는 중지 하는 경우를 감지 하는 Stream Analytics 쿼리를 만듭니다.
* Azure Functions 및 SendGrid 서비스를 사용 하 여 전자 메일 알림을 보냅니다.

제안 된 다음 단계에 알아보려면는 알림과 사용자 지정 규칙을 만드는 방법을 배웠으므로 하는 방법 [확장 Azure IoT Central 사용자 지정 분석을 사용 하 여](howto-create-custom-analytics.md)입니다.
