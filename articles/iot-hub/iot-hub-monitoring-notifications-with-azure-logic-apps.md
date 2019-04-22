---
title: Azure Logic Apps를 사용하여 IoT 원격 모니터링 및 알림 | Microsoft Docs
description: Azure Logic Apps을 사용하여 IoT Hub에서 IoT 온도를 모니터링하고 감지된 이상에 대한 전자 메일 알림을 자동으로 사서함에 보낼 수 있습니다.
author: robinsh
keywords: iot 모니터링, iot 알림, iot 온도 모니터링
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 5a277ac18bcbcb7e7acc6faf52f7bc72759c82a7
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678006"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Azure Logic Apps으로 IoT Hub와 사서함을 연결하여 IoT 원격 모니터링 및 알림

![종단 간 다이어그램](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Azure Logic Apps은 일련의 단계로 프로세스를 자동화하는 방법을 제공합니다. 논리 앱은 다양한 서비스 및 프로토콜을 통해 연결할 수 있습니다. '계정이 추가되면' 같은 트리거로 시작하여 '푸시 알림 전송' 같은 작업 조합이 이어집니다. 이 기능 덕분에 Logic Apps은 다른 여러 사용 시나리오도 지원하지만 특히 이상을 지속적으로 감지할 수 있다는 점에서 IoT 모니터링을 위한 완벽한 IoT 솔루션입니다.

## <a name="what-you-learn"></a>학습 내용

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 만드는 방법을 배웁니다. 온도가 30C를 넘으면 클라이언트 애플리케이션은 IoT Hub로 보내는 메시지에 `temperatureAlert = "true"`를 표시합니다. 이 메시지는 사용자에게 전자 메일 알림을 보내도록 논리 앱을 트리거합니다.

## <a name="what-you-do"></a>수행할 작업

* Service Bus 네임스페이스를 만들고 큐를 추가합니다.
* IoT Hub에 엔드포인트와 라우팅 규칙을 추가합니다.
* 논리 앱을 만들고, 구성하고, 테스트합니다.

## <a name="what-you-need"></a>필요한 항목

* 완료 합니다 [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서; 중 하나 예를 들어 [node.js 사용 하 여 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)합니다. 이러한 다음 요구 사항을 다룹니다.

  * 활성 Azure 구독.
  * 구독 중인 Azure IoT Hub
  * 메시지를 Azure IoT Hub로 보내는 클라이언트 애플리케이션

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Service Bus 네임스페이스를 만들고 큐 추가

### <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

1. 에 [Azure portal](https://portal.azure.com/)를 선택 **리소스를 만듭니다** > **엔터프라이즈 통합** > **Service Bus**합니다.

2. 다음 정보를 지정합니다.

   **이름**: Service bus의 이름입니다.

   **가격 책정 계층**: 선택 **기본적인** > **선택**합니다. 이 자습서에서는 기본 계층이면 충분합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **Location**: IoT hub를 사용 하는 동일한 위치를 사용 합니다.

3. **만들기**를 선택합니다.

   ![Azure Portal에서 Service Bus 네임스페이스 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Service Bus 큐 추가

1. Service bus 네임 스페이스를 열고 선택한 **+ 큐**합니다.

1. 큐에 대 한 이름을 입력 하 고 선택한 **만들기**합니다.

1. Service bus 큐를 열고 선택한 **공유 액세스 정책** > **+ 추가**합니다.

1. 검사 정책에 대 한 이름을 입력 **관리**를 선택한 후 **만들기**합니다.

   ![Azure Portal에서 Service Bus 큐 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-query-to-your-iot-hub"></a>IoT hub에 끝점과 라우팅 쿼리 추가

이제 Iot hub에 끝점과 라우팅 쿼리를 추가 합니다.

### <a name="add-an-endpoint"></a>엔드포인트 추가

1. IoT hub를 열고 **끝점** > **+ 추가**합니다.

1. 다음 정보를 입력합니다.

   **이름**: 엔드포인트의 이름입니다.

   **끝점 유형을**: **Service Bus 큐**를 선택합니다.

   **Service Bus 네임 스페이스**: 만든 네임 스페이스를 선택 합니다.

   **Service Bus 큐**: 만든 큐를 선택 합니다.

3. **확인**을 선택합니다.

   ![Azure Portal에서 IoT Hub에 엔드포인트 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>라우팅 규칙 추가

1. IoT hub 선택 **경로** > **+ 추가**합니다.

2. 다음 정보를 입력합니다.

   **이름**: 라우팅 규칙의 이름입니다.

   **데이터 원본**: 선택 **DeviceMessages**합니다.

   **엔드포인트**: 만든 끝점을 선택 합니다.

   **쿼리 문자열**: [https://slack.botframework.com](`temperatureAlert = "true"`) 을 입력합니다.

3. **저장**을 선택합니다.

   ![Azure Portal에서 라우팅 규칙 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>논리 앱을 만들고 구성

다음으로 만들고 논리 앱을 구성 합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기

1. 에 [Azure portal](https://portal.azure.com/)를 선택 **리소스를 만듭니다** > **엔터프라이즈 통합** > **논리 앱**합니다.

2. 다음 정보를 입력합니다.

   **이름**: 논리 앱의 이름입니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **Location**: IoT hub를 사용 하는 동일한 위치를 사용 합니다.

3. **만들기**를 선택합니다.

### <a name="configure-the-logic-app"></a>논리 앱 구성

1. 논리 앱을 열면 Logic Apps 디자이너가 열립니다.

2. 논리 앱 디자이너에서 선택 **빈 Logic App**합니다.

   ![Azure Portal에서 빈 논리 앱 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

3. **Service Bus**를 선택합니다.

   ![Service Bus를 선택하여 Azure Portal에서 논리 앱 만들기 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

4. 선택 **(자동 완성) 큐에 하나 이상의 메시지가 도착 하면 Service Bus –** 합니다.

5. Service Bus 연결을 만듭니다.

   1. 연결 이름을 입력합니다.

   2. Service bus 네임 스페이스 선택 > service bus 정책 > **만들기**합니다.

      ![Azure Portal에서 논리 앱에 대한 Service Bus 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   3. 선택 **계속** service bus 연결이 만들어진 후 합니다.

   4. 사용자가 만든 큐를 선택 하 고 입력 `175` 에 대 한 **최대 메시지 수**입니다.

      ![논리 앱에서 Service Bus 연결의 최대 메시지 수 지정](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   5. 변경 내용을 저장 하려면 "저장" 단추를 선택 합니다.

6. SMTP 서비스 연결을 만듭니다.

   1. **새 단계** > **작업 추가**를 선택합니다.

   2. 형식 `SMTP`를 선택 합니다 **SMTP** 검색 결과에서 서비스를 선택한 **SMTP-전자 메일 보내기**합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   3. 사용자 사서함의 SMTP 정보를 입력 한 다음 선택 **만들기**합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 정보 입력](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) 및 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html)에 대한 SMTP 정보를 가져옵니다.

   4. **보내는 사람** 및 **받는 사람**의 전자 메일 주소를 입력하고, **제목** 및 **본문**에 `High temperature detected`를 입력합니다.

   5. **저장**을 선택합니다.

논리 앱을 저장할 때 논리 앱이 정상적으로 작동합니다.

## <a name="test-the-logic-app"></a>논리 앱 테스트

1. [Azure IoT Hub에 ESP8266 연결](iot-hub-arduino-huzzah-esp8266-get-started.md)에서 장치에 배포한 클라이언트 애플리케이션을 시작합니다.

2. SensorTag 주변의 환경 온도를 30C 이상으로 높입니다. 예를 들어 SensorTag 주변에 촛불을 켭니다.

3. 논리 앱에서 보낸 전자 메일 알림을 수신하게 됩니다.

   > [!NOTE]
   > 전자 메일을 보낸 사람이 본인이라는 것을 확인하기 위해 전자 메일 서비스 공급자가 발신자의 신분을 확인해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 성공적으로 만들었습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
