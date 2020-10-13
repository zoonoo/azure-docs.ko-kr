---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580069"
---
IoT 플러그 앤 플레이를 사용 하 여 Azure IoT 응용 프로그램에 기능을 보급 하는 스마트 장치를 빌드할 수 있습니다. IoT 플러그 앤 플레이 장치는 고객이 IoT 플러그 앤 플레이 지원 응용 프로그램에 연결할 때 수동 구성이 필요 하지 않습니다.

스마트 장치는 직접 구현 하거나 [모듈](../articles/iot-hub/iot-hub-devguide-module-twins.md)을 사용 하거나 [IoT Edge 모듈](../articles/iot-edge/about-iot-edge.md)을 사용할 수 있습니다.

이 가이드에서는 [IoT 플러그 앤 플레이 규칙](../articles/iot-pnp/concepts-convention.md)을 따르는 장치, 모듈 또는 IoT Edge 모듈을 만드는 데 필요한 기본 단계에 대해 설명 합니다.

IoT 플러그 앤 플레이 장치, 모듈 또는 IoT Edge 모듈을 빌드하려면 다음 단계를 수행 합니다.

1. 장치가 MQTT 또는 Websocket을 통한 MQTT 프로토콜을 사용 하 여 Azure IoT Hub에 연결 하는지 확인 합니다.
1. 장치를 설명 하는 [DTDL (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 모델을 만듭니다. 자세히 알아보려면 [IoT 플러그 앤 플레이 모델의 구성 요소 이해](../articles/iot-pnp/concepts-components.md)를 참조 하세요.
1. 장치 연결의 일부로를 알리기 위해 장치 또는 모듈을 업데이트 `model-id` 합니다.
1. [IoT 플러그 앤 플레이 규칙](../articles/iot-pnp/concepts-convention.md) 을 사용 하 여 원격 분석, 속성 및 명령 구현

장치 또는 모듈 구현이 준비 되 면 [Azure iot 탐색기](../articles/iot-pnp/howto-use-iot-explorer.md) 를 사용 하 여 장치가 IoT 플러그 앤 플레이 규칙을 따르는지 확인 합니다.
