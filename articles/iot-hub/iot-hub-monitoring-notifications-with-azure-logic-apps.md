---
title: Azure Logic Apps를 사용하여 IoT 원격 모니터링 및 알림 | Microsoft Docs
description: Azure Logic Apps을 사용하여 IoT Hub에서 IoT 온도를 모니터링하고 감지된 이상에 대한 전자 메일 알림을 자동으로 사서함에 보낼 수 있습니다.
author: robinsh
keywords: iot 모니터링, iot 알림, iot 온도 모니터링
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: ad1fcb67704e79f5aef62a59604e47f477804405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68385723"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Azure Logic Apps으로 IoT Hub와 사서함을 연결하여 IoT 원격 모니터링 및 알림

![엔드투엔드 다이어그램](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps를](https://docs.microsoft.com/azure/logic-apps/) 사용하면 온-프레미스 및 클라우드 서비스, 하나 이상의 기업 및 다양한 프로토콜에서 워크플로를 오케스트레이션할 수 있습니다. 논리 앱은 트리거로 시작한 다음 조건 및 이터레이터와 같은 기본 제공 컨트롤을 사용하여 순서를 정할 수 있는 하나 이상의 작업이 뒤따릅니다. 이러한 유연성으로 로직 앱은 IoT 모니터링 시나리오에 이상적인 IoT 솔루션입니다. 예를 들어 IoT Hub 엔드포인트에 있는 장치에서 원격 분석 데이터가 도착하면 Azure Storage Blob에서 데이터를 웨어하우스하는 논리 앱 워크플로를 시작하고, 전자 메일 경고를 보내 데이터 이상 경고를 보내고, 장치가 오류를 보고하는 경우 기술자 방문을 예약할 수 있습니다. 등등.

## <a name="what-you-learn"></a>학습 내용

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 만드는 방법을 배웁니다.

장치에서 실행되는 클라이언트 코드는 IoT `temperatureAlert`Hub로 전송되는 모든 원격 분석 메시지에서 응용 프로그램 속성, 을 설정합니다. 클라이언트 코드가 30C 이상의 온도를 감지하면 이 속성을 `true`다음과 같이 설정합니다. 그렇지 않으면 속성을 로 `false`설정합니다.

IoT 허브에 도착하는 메시지는 본문에 포함된 원격 분석 데이터와 응용 프로그램 `temperatureAlert` 속성에 포함된 속성(시스템 속성이 표시되지 않음)과 유사합니다.

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

IoT Hub 메시지 형식에 대해 자세히 알아보려면 [IoT Hub 메시지 만들기 및 읽기를](iot-hub-devguide-messages-construct.md)참조하십시오.

이 항목에서는 IoT 허브에서 라우팅을 설정하여 속성이 `temperatureAlert` `true` 서비스 버스 끝점으로 메시지를 보냅니다. 그런 다음 Service Bus 끝점에 도착하는 메시지를 트리거하고 전자 메일 알림을 보내는 논리 앱을 설정합니다.

## <a name="what-you-do"></a>수행할 작업

* Service Bus 네임스페이스를 만들고 서비스 버스 큐를 추가합니다.
* 사용자 지정 끝점과 라우팅 규칙을 IoT 허브에 추가하여 온도 경고가 포함된 메시지를 Service Bus 큐에 라우팅합니다.
* 논리 앱을 생성, 구성 및 테스트하여 Service Bus 큐에서 메시지를 사용하고 원하는 받는 사람에게 알림 이메일을 보냅니다.

## <a name="what-you-need"></a>필요한 항목

* [라즈베리 파이 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서 중 하나를 완료; 예를 들어, [node.js와 라즈베리 파이](iot-hub-raspberry-pi-kit-node-get-started.md). 다음은 다음 요구 사항을 다룹니다.

  * 활성화된 Azure 구독.
  * 구독 중인 Azure IoT Hub
  * Azure IoT 허브로 원격 분석 메시지를 보내는 장치에서 실행 중인 클라이언트 응용 프로그램입니다.

## <a name="create-service-bus-namespace-and-queue"></a>서비스 버스 네임스페이스 및 큐 만들기

Service Bus 네임스페이스 및 큐를 만듭니다. 이 항목의 후반부에서는 IoT Hub에서 논리 앱에서 온도 경고를 포함하는 메시지를 지시하도록 라우팅 규칙을 만들고 논리 앱에서 수신을 선택하고 알림 전자 메일을 보내도록 트리거합니다.

### <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

1. Azure [포털에서](https://portal.azure.com/)+ > 리소스**통합** > **서비스 버스** **만들기를**선택합니다.

1. **네임스페이스 만들기** 창에서 다음 정보를 제공합니다.

   **이름**: 서비스 버스 네임스페이스의 이름입니다. 네임스페이스는 Azure 전체에서 고유해야 합니다.

   **가격 책정 계층**: 드롭다운 목록에서 **기본을** 선택합니다. 이 자습서에서는 기본 계층이면 충분합니다.

   **리소스 그룹**: IoT 허브에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: IoT Hub에서 사용하는 것과 같은 위치를 사용합니다.

   ![Azure Portal에서 Service Bus 네임스페이스 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. **만들기**를 선택합니다. 다음 단계로 넘어가기 전에 배포가 완료될 때까지 기다립니다.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>네임스페이스에 서비스 버스 대기열 추가

1. 서비스 버스 네임스페이스를 엽니다. Service Bus 네임스페이스로 가는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹을** 선택하고 리소스 그룹을 선택한 다음 리소스 목록에서 Service Bus 네임스페이스를 선택하는 것입니다.

1. 서비스 **버스 네임스페이스** 창에서 **+ 큐를**선택합니다.

1. 큐의 이름을 입력한 다음 **을 선택합니다.** 큐가 성공적으로 만들어지면 **만들기 큐** 창이 닫힙습니다.

   ![Azure Portal에서 Service Bus 큐 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. **서비스 버스 네임스페이스** 창에서 **엔터티**아래에서 **큐를 선택합니다.** 목록에서 서비스 버스 큐를 연 다음 **공유 액세스 정책** > **+ 추가**를 선택합니다.

1. 정책의 이름을 입력하고 **관리를**선택한 다음 **을 선택합니다.**

   ![Azure 포털에 서비스 버스 큐 정책 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>IoT 허브에 사용자 지정 끝점 및 라우팅 규칙 추가

서비스 버스 대기열에 대한 사용자 지정 끝점을 IoT 허브에 추가하고 논리 앱에서 선택되는 해당 끝점에 온도 경고가 포함된 메시지를 전달하는 메시지 라우팅 규칙을 만듭니다. 라우팅 규칙은 라우팅 쿼리를 `temperatureAlert = "true"`사용하여 장치에서 실행되는 클라이언트 코드에서 `temperatureAlert` 설정한 응용 프로그램 속성 값을 기반으로 메시지를 전달합니다. 자세한 내용은 [메시지 속성을 기반으로 하는 메시지 라우팅 쿼리를](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)참조하십시오.

### <a name="add-a-custom-endpoint"></a>사용자 지정 끝점 추가

1. IoT Hub를 엽니다. IoT 허브로 이동하는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹을** 선택하고 리소스 그룹을 선택한 다음 리소스 목록에서 IoT 허브를 선택하는 것입니다.

1. **메시징에서** **메시지 라우팅을**선택합니다. 메시지 **라우팅** 창에서 **사용자 지정 끝점** 탭을 선택한 다음 **+ 추가**를 선택합니다. 드롭다운 목록에서 **서비스 버스 큐를**선택합니다.

   ![Azure Portal에서 IoT Hub에 엔드포인트 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. 서비스 **버스 끝점 추가** 창에서 다음 정보를 입력합니다.

   **끝점 이름 : 끝점의**이름입니다.

   **서비스 버스 네임스페이스**: 만든 네임스페이스를 선택합니다.

   **서비스 버스 대기열**: 만든 큐를 선택합니다.

   ![Azure Portal에서 IoT Hub에 엔드포인트 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. **만들기**를 선택합니다. 끝점을 성공적으로 만든 후 다음 단계로 진행합니다.

### <a name="add-a-routing-rule"></a>라우팅 규칙 추가

1. **메시지 라우팅** 창에서 **경로** 탭을 선택한 다음 **+ 추가**를 선택합니다.

1. 경로 **추가** 창에서 다음 정보를 입력합니다.

   **이름**: 라우팅 규칙의 이름입니다.

   **엔드포인트**: 앞에서 만든 엔드포인트를 선택합니다.

   **데이터 원본**: **장치 원격 분석 메시지를**선택합니다.

   **라우팅 쿼리**: `temperatureAlert = "true"`enter.

   ![Azure Portal에서 라우팅 규칙 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. **저장**을 선택합니다. **메시지 라우팅** 창을 닫을 수 있습니다.

## <a name="create-and-configure-a-logic-app"></a>로직 앱 생성 및 구성

이전 섹션에서는 IoT 허브를 설정하여 온도 경고가 포함된 메시지를 Service Bus 큐로 라우팅합니다. 이제 서비스 버스 큐를 모니터링하고 메시지가 큐에 추가될 때마다 전자 메일 알림을 보내는 논리 앱을 설정합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기

1. **리소스** > 통합 논리**앱**만들기를**선택합니다.** > 

1. 다음 정보를 입력합니다.

   **이름**: 논리 앱의 이름입니다.

   **리소스 그룹**: IoT 허브에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: IoT Hub에서 사용하는 것과 같은 위치를 사용합니다.

   ![Azure 포털에서 논리 앱 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. **만들기**를 선택합니다.

### <a name="configure-the-logic-app-trigger"></a>논리 앱 트리거 구성

1. 논리 앱을 엽니다. 논리 앱으로 이동하는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹을** 선택하고 리소스 그룹을 선택한 다음 리소스 목록에서 논리 앱을 선택하는 것입니다. 논리 앱을 선택하면 논리 앱 디자이너가 열립니다.

1. 논리 앱 디자이너에서 템플릿으로 스크롤하고 **빈 논리 앱을** **선택합니다.**

   ![Azure Portal에서 빈 논리 앱 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. **모두** 탭을 선택한 다음 **서비스 버스를 선택합니다.**

   ![Service Bus를 선택하여 Azure Portal에서 논리 앱 만들기 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. 트리거에서 하나 **이상의 메시지가 큐에 도착하는 경우(자동 완성)를** **선택합니다.**

   ![Azure 포털에서 논리 앱에 대한 트리거 선택](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Service Bus 연결을 만듭니다.
   1. 연결 이름을 입력하고 목록에서 서비스 버스 네임스페이스를 선택합니다. 다음 화면이 열립니다.

      ![Azure Portal에서 논리 앱에 대한 Service Bus 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. 서비스 버스 정책(루트관리공유액세스키)을 선택합니다. 그런 다음 **을 선택합니다.**

      ![Azure Portal에서 논리 앱에 대한 Service Bus 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 마지막 화면에서 Queue **이름에**대해 드롭다운에서 만든 큐를 선택합니다. 최대 `175` **메시지 수에**대해 입력합니다.

      ![논리 앱에서 Service Bus 연결의 최대 메시지 수 지정](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. 로직 앱 디자이너 상단의 메뉴에 **저장을** 선택하여 변경 내용을 저장합니다.

### <a name="configure-the-logic-app-action"></a>논리 앱 작업 구성

1. SMTP 서비스 연결을 만듭니다.

   1. **새 단계를**선택합니다. **작업 선택에서** **모두** 탭을 선택합니다.

   1. 검색 `smtp` 상자를 입력하고 검색 결과에서 **SMTP** 서비스를 선택한 다음 **전자 메일 보내기를**선택합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 사서함에 대한 SMTP 정보를 입력한 다음 **을 선택합니다.**

      ![Azure Portal에서 논리 앱에 SMTP 연결 정보 입력](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) 및 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html)에 대한 SMTP 정보를 가져옵니다.

      > [!NOTE]
      > 연결을 설정하려면 SSL을 사용하지 않도록 설정해야 할 수 있습니다. 이 경우 연결이 설정된 후 SSL을 다시 사용하려면 이 섹션의 끝에 있는 선택적 단계를 참조하십시오.

   1. From the **Add new parameter** drop-down on the **Send Email** step, select **From**, **To**, **Subject** and **Body**. 화면의 아무 곳이나 클릭하거나 탭하여 선택 상자를 닫습니다.

      ![SMTP 연결 이메일 필드 선택](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. **보내는 사람** 및 **받는 사람**의 전자 메일 주소를 입력하고, **제목** 및 **본문**에 `High temperature detected`를 입력합니다. 이 흐름 대화 상자에 **사용된 앱 및 커넥터의 동적 콘텐츠 추가가** 열리면 **숨기기를** 선택하여 닫습니다. 이 자습서에서는 동적 콘텐츠를 사용하지 않습니다.

      ![채우기 SMTP 연결 이메일 필드](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. SMTP 연결을 저장하려면 **저장을** 선택합니다.

1. (선택 사항) 이메일 제공업체와 연결을 설정하기 위해 SSL을 사용하지 않도록 설정해야 하고 다시 사용하려면 다음 단계를 따르세요.

   1. **로직 앱** 창에서 **개발 도구에서** **API 연결을**선택합니다.

   1. API 연결 목록에서 SMTP 연결을 선택합니다.

   1. **smtp API 연결** 창에서 **일반**에서 **API 연결 편집을**선택합니다.

   1. API **연결 편집** 창에서 **SSL 사용(SSL 사용)을**선택하고 이메일 계정의 암호를 다시 입력하고 **저장을**선택합니다.

      ![Azure 포털의 논리 앱에서 SMTP API 연결 편집](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

이제 논리 앱이 Service Bus 큐에서 온도 경고를 처리하고 이메일 계정으로 알림을 보낼 준비가 되었습니다.

## <a name="test-the-logic-app"></a>논리 앱 테스트

1. 장치에서 클라이언트 응용 프로그램을 시작합니다.

1. 물리적 장치를 사용하는 경우 온도가 섭씨 30도를 초과할 때까지 열 센서 근처에 열원을 조심스럽게 가져오십시오. 온라인 시뮬레이터를 사용하는 경우 클라이언트 코드는 30C를 초과하는 원격 분석 메시지를 임의로 출력합니다.

1. 논리 앱에서 보낸 이메일 알림을 받기 시작해야 합니다.

   > [!NOTE]
   > 전자 메일을 보낸 사람이 본인이라는 것을 확인하기 위해 전자 메일 서비스 공급자가 발신자의 신분을 확인해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 성공적으로 만들었습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
