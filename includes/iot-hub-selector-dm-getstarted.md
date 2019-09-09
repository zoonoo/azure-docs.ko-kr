---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558808"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

백 엔드 앱 [은 장치 쌍 및][lnk-devtwin] [직접 메서드와][lnk-c2dmethod]같은 Azure IoT Hub 기본 형식을 사용 하 여 장치에서 장치 관리 작업을 원격으로 시작 하 고 모니터링할 수 있습니다. 이 자습서에서는 백 엔드 앱 및 디바이스 앱이 함께 작동하여 IoT Hub를 사용하여 원격 디바이스 다시 부팅을 시작하고 모니터링하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

직접 메서드를 사용하여 클라우드의 백 엔드 앱에서 디바이스 관리 작업(예: 재부팅, 초기화 및 펌웨어 업데이트)을 시작합니다. 디바이스는 다음과 같은 역할을 합니다.

* IoT Hub에서 보낸 메서드 요청 처리.

* 디바이스에서 해당하는 디바이스 특정 작업 시작.

* *reported 속성*을 통해 IoT Hub에 상태 업데이트 제공.

클라우드에서 백 엔드 앱을 사용하여 디바이스 쌍 쿼리를 실행하고 디바이스 관리 작업의 진행 상태를 보고할 수 있습니다.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
