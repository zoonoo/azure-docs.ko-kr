---
title: 자습서 - IoT Hub 이벤트를 사용하여 Azure Logic Apps 트리거
description: 이 자습서에서는 Azure Event Grid의 이벤트 라우팅 서비스를 사용하여 IoT Hub 이벤트에 따라 Azure Logic Apps 작업을 수행하는 자동화된 프로세스를 만드는 방법을 보여줍니다.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5092aa0b5b23f04af1f49933bca234815f03f454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604598"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>자습서: Event Grid 및 Logic Apps를 사용하여 Azure IoT Hub 이벤트에 대한 이메일 알림 보내기

Azure Event Grid를 사용하면 다운스트림 비즈니스 애플리케이션에서 작업을 트리거하여 IoT Hub에서 이벤트에 대응할 수 있습니다.

이 문서에서는 IoT Hub 및 Event Grid를 사용하는 샘플 구성을 안내합니다. 마지막으로 디바이스가 IoT 허브에 연결되거나 연결이 끊길 때마다 Azure 논리 앱에서 알림 이메일을 보내도록 설정합니다. Event Grid를 사용하여 중요한 디바이스 연결 해제에 대한 알림을 적시에 받을 수 있습니다. 메트릭 및 진단은 로그/경고에 표시되는 데 몇 분(예: 20분 이상 -- 숫자를 입력하고 싶지 않음)이 걸릴 수 있습니다. 이는 중요한 인프라에서는 허용되지 않을 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성화된 Azure 구독. 구독이 없는 경우 [체험 Azure 계정을 만들](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

* Office 365 Outlook 또는 Outlook.com과 같이 Azure Logic Apps에서 지원하는 이메일 공급자의 이메일 계정입니다. 이 이메일 계정은 이벤트 알림을 보내는 데 사용됩니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>IoT 허브 만들기

포털에서 Azure Cloud Shell 터미널을 사용하여 새 IoT 허브를 빠르게 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. 페이지 오른쪽 위에 있는 Cloud Shell 단추를 선택합니다.

   ![Cloud Shell 단추](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. 다음 명령을 실행하여 새 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. 다음 명령을 실행하여 IoT 허브를 만듭니다.

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Cloud Shell 터미널을 최소화합니다. 이 자습서의 뒷부분에서 셸로 돌아갑니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기

그런 다음, 논리 앱을 만들고 IoT 허브에서 들어오는 요청을 처리하는 HTTP 이벤트 그리드 트리거를 추가합니다. 

### <a name="create-a-logic-app-resource"></a>논리 앱 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기**를 선택한 다음, 검색 상자에서 "논리 앱"을 입력하고, Enter 키를 선택합니다. 결과에서 **논리 앱**을 선택합니다.

   ![논리 앱 만들기](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. 다음 화면에서 **만들기**를 선택합니다. 

1. 구독에서 논리 앱에 고유한 이름을 제공하고 동일한 구독과 리소스 그룹 및 위치를 IoT 허브로 선택합니다. 

   ![논리 앱을 만들기 위한 필드](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. **검토 + 만들기**를 선택합니다.

1. 설정을 확인한 다음, **만들기**를 선택합니다.

1. 리소스가 생성되면 **리소스로 이동**을 선택합니다. 

1. Logic Apps 디자이너에서 페이지 아래쪽으로 이동하여 **템플릿**을 확인합니다. 논리 앱을 처음부터 빌드할 수 있도록 **비어 있는 논리 앱**을 선택합니다.

### <a name="select-a-trigger"></a>트리거 선택

트리거는 논리 앱을 시작하는 특정 이벤트입니다. 이 자습서에서는 워크플로를 시작하는 트리거를 HTTP를 통해 요청을 받습니다.  

1. 커넥터 및 트리거 검색 표시줄에서 **HTTP**를 입력합니다.

1. 결과를 스크롤하고 **요청 - HTTP 요청을 수신한 경우**를 트리거로 선택합니다. 

   ![HTTP 요청 트리거 선택](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다. 

   ![샘플 페이로드 사용](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. *디바이스 연결 이벤트 스키마* JSON을 텍스트 상자에 붙여넣은 다음, **완료**를 선택합니다.

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   이 이벤트는 디바이스가 IoT 허브에 연결될 때 게시됩니다.

> [!NOTE]
> **요청할 때 Content-Type 헤더 집합을 애플리케이션/json에 포함해야 합니다**라고 표시되는 팝업 알림을 받을 수도 있습니다. 이 제안을 안전하게 무시하고 다음 섹션으로 이동할 수 있습니다. 

### <a name="create-an-action"></a>작업 만들기

트리거가 논리 앱 워크플로를 시작한 후에 발생하는 모든 단계가 작업할 대상입니다. 이 자습서의 경우 작업은 메일 공급자의 이메일 알림을 보내는 것입니다. 

1. **새 단계**를 선택합니다. 그러면 **작업 선택** 창이 열립니다.

1. **Outlook**을 검색합니다.

1. 전자 메일 공급자에 따라 일치하는 커넥터를 찾아 선택합니다. 이 자습서에서는 **Outlook.com**을 사용합니다. 다른 이메일 공급자를 위한 단계들도 비슷합니다. 

   ![이메일 공급자 커넥터 선택](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. **이메일 보내기(V2)** 작업을 선택합니다. 

1. **로그인**을 선택하고 이메일 계정에 로그인합니다. **예**를 선택하여 앱이 사용자의 정보에 액세스할 수 있도록 합니다.

1. 이메일 템플릿을 빌드합니다. 

   * **받는 사람**: 알림 메일을 받을 메일 주소를 입력합니다. 이 자습서에서는 테스트를 위해 액세스할 수 있는 메일 계정을 사용합니다. 

   * **주체**: 제목의 텍스트를 입력합니다. [제목] 텍스트 상자를 클릭하면 포함할 동적 콘텐츠를 선택할 수 있습니다. 예를 들어 이 자습서에서는 `IoT Hub alert: {eventType}`을 사용합니다. 동적 콘텐츠가 표시되지 않으면 **동적 콘텐츠 추가** 하이퍼링크를 선택합니다. 그러면 이 링크가 설정/해제됩니다.

   * **본문**: 이메일의 텍스트를 작성합니다. 이벤트 데이터를 기반으로 하는 동적 콘텐츠를 포함하도록 선택기 도구에서 JSON 속성을 선택합니다. 동적 콘텐츠가 표시되지 않으면 **본문** 텍스트 상자에서 **동적 콘텐츠 추가** 하이퍼링크를 선택합니다. 원하는 필드가 표시되지 않으면 동적 콘텐츠 화면에서 *자세히*를 클릭하여 이전 작업의 필드를 포함시킵니다.

   이메일 템플릿은 다음 예와 같습니다.

   ![이메일 정보 입력](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Logic Apps 디자이너에서 **저장**을 선택합니다.  

### <a name="copy-the-http-url"></a>HTTP URL 복사

Logic Apps Designer를 나가기 전에 논리 앱이 트리거에 대해 수신 대기하는 URL을 복사합니다. 이 URL을 사용하여 Event Grid를 구성합니다. 

1. **HTTP 요청을 받을 경우** 트리거 구성 상자를 클릭하여 확장합니다. 

1. **HTTP POST URL**의 값을 그 옆에 있는 복사 단추를 선택하여 복사합니다. 

   ![HTTP POST URL 복사](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. 다음 섹션에서 참조할 수 있도록 이 URL을 저장합니다. 

## <a name="configure-subscription-for-iot-hub-events"></a>IoT Hub 이벤트에 대한 구독 구성

이 섹션에서는 일어나는 순서대로 이벤트를 게시하도록 Azure IoT Hub 를 구성합니다. 

1. Azure Portal에서 IoT Hub로 이동합니다. 이 작업은 **리소스 그룹**을 선택하여 수행할 수 있습니다. 그러면 이 자습서의 리소스 그룹을 선택한 다음, 리소스 목록에서 IoT 허브를 선택합니다.

1. **이벤트**를 선택합니다.

   ![Event Grid 세부 정보 열기](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. **이벤트 구독**을 선택합니다. 

   ![새 이벤트 구독 만들기](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. 다음 값을 가진 이벤트 구독을 만듭니다. 

   1. **이벤트 구독 세부 정보** 섹션에서 다음을 수행합니다.
      1. 이벤트 구독의 **이름**을 입력합니다. 
      2. **이벤트 그리드**에 대해 **이벤트 그리드 스키마**를 선택합니다. 
   2. **항목 세부 정보** 섹션에서 다음을 수행합니다.
      1. **토픽 유형**이 **IoT Hub**로 설정되어 있는지 확인합니다. 
      2. IoT hub 이름이 **원본 리소스** 필드의 값으로 설정되어 있는지 확인합니다. 
      3. 생성될 **시스템 토픽**의 이름을 입력합니다. 시스템 항목에 대한 자세한 내용은 [시스템 항목 개요](system-topics.md)를 참조하십시오.
   3. **이벤트 유형** 섹션에서 다음을 수행합니다.
      1. **이벤트 유형 필터링** 드롭다운을 선택합니다.
      1. **디바이스 연결됨** 및 **디바이스 연결이 해제됨** 확인란만 선택된 상태로 두고 **디바이스 생성됨** 및 **디바이스 삭제됨** 확인란을 선택 취소합니다.

         ![구독 이벤트 유형 선택](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. **엔드포인트 세부 정보** 섹션에서 다음을 수행합니다. 
       1. **엔드포인트 유형**을 **webhook**으로 선택합니다.
       2. **엔드포인트 선택하기**를 클릭하고 논리 앱에서 복사한 URL을 붙여넣은 다음 선택 사항을 확인합니다.

         ![엔드포인트 URL 선택](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         완료되면 창이 다음 예와 같이 표시됩니다. 

         ![샘플 이벤트 구독 양식](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  **만들기**를 선택합니다.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>원격 분석을 연결하고 보내는 새 디바이스 시뮬레이션

Azure CLI를 사용하여 디바이스 연결을 신속하게 시뮬레이션하여 논리 앱을 테스트합니다. 

1. Cloud Shell 단추를 선택하여 터미널을 다시 엽니다.

1. 다음 명령을 실행하여 시뮬레이션된 디바이스 ID를 만듭니다.
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. 다음 명령을 실행하여 디바이스를 IoT Hub에 연결하고 원격 분석을 보내는 것을 시뮬레이션합니다.

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. 시뮬레이션된 디바이스가 IoT Hub에 연결되면 "DeviceConnected" 이벤트를 알리는 이메일을 받게 됩니다.

1. 시뮬레이션이 완료되면 "DeviceDisconnected" 이벤트를 알리는 이메일을 받게 됩니다. 

    ![경고 메일 예제](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 Azure 구독에 요금을 부과하는 리소스를 사용했습니다. 자습서를 사용해 보고 결과 테스트를 완료한 경우 유지하지 않으려는 리소스를 사용하지 않도록 설정하거나 삭제합니다. 

이 자습서에서 만든 리소스를 모두 삭제하려면 리소스 그룹을 삭제합니다. 

1. **리소스 그룹**을 선택한 다음, 이 자습서에서 만든 리소스 그룹을 선택합니다.

2. [리소스 그룹] 창에서 **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하라는 메시지가 표시되면 해당 그룹을 삭제할 수 있습니다. 여기에 포함된 리소스도 모두 제거됩니다.

## <a name="next-steps"></a>다음 단계

* [작업을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응](../iot-hub/iot-hub-event-grid.md)에 대해 자세히 알아봅니다.
* [디바이스 연결 및 연결 해제 이벤트를 정렬하는 방법](../iot-hub/iot-hub-how-to-order-connection-state-events.md)에 대해 알아봅니다.
* [Event Grid](overview.md)를 사용하여 다른 무엇을 할 수 있는지에 대해 알아봅니다.

지원되는 Logic App 커넥터의 전체 목록은 [커넥터 개요](/connectors/)를 참조하세요.
