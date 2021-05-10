---
ms.openlocfilehash: 882ba60e16f770651a1d9fe3b02b61be2b4c34c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99531589"
---
Live Video Analytics on IoT Edge 모듈을 사용하여 들어오는 라이브 비디오 스트림에서 동작을 감지하고 IoT Hub에 이벤트를 전송합니다. 이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 탐색기 창을 열고, 왼쪽 아래 모서리에서 Azure IoT Hub를 찾습니다.
1. **디바이스** 노드를 확장합니다.
1. **lva-sample-device** 를 마우스 오른쪽 단추로 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

    ![기본 제공 이벤트 엔드포인트 모니터링 시작](../../../media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> IoT Hub에 대한 기본 제공 엔드포인트 정보를 제공하라는 메시지가 표시될 수 있습니다. 해당 정보를 가져오려면 Azure Portal에서 IoT Hub로 이동하여 왼쪽 탐색 창에서 **기본 제공 엔드포인트** 옵션을 찾습니다. 여기를 클릭하고 **Event Hub 호환 엔드포인트** 섹션에서 **Event Hub 호환 엔드포인트** 를 찾습니다. 상자의 텍스트를 복사하여 사용합니다. 엔드포인트는 다음과 같이 표시됩니다.  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
