---
title: Azure IoT Edge 및 Azure IoT Central | Microsoft Docs
description: IoT Central 응용 프로그램과 함께 Azure IoT Edge를 사용 하는 방법을 이해 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 5c51649e742f6e314e0e324ae19c38f1a83b02ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016863"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 연결

*이 문서는 솔루션 빌더 및 디바이스 개발자에게 적용됩니다.*

IoT Edge는 다음 세 가지 구성 요소로 구성됩니다.

* **IoT Edge 모듈**은 Azure 서비스, 파트너 서비스 또는 사용자 고유의 코드를 실행하는 컨테이너입니다. 모듈은 IoT Edge 디바이스에 배포되며, 해당 디바이스에서 로컬로 실행됩니다.
* **IoT Edge 런타임은** 각 IoT Edge 장치에서 실행 되며 각 장치에 배포 된 모듈을 관리 합니다.
* **클라우드 기반 인터페이스**를 사용하여 IoT Edge 디바이스를 원격으로 모니터링 및 관리할 수 있습니다. IoT Central은 클라우드 인터페이스입니다.

**Azure IoT Edge** 디바이스는 다운스트림 디바이스가 IoT Edge 디바이스에 연결되는 게이트웨이 디바이스일 수 있습니다. 이 문서에서는 다운스트림 장치 연결 패턴에 대 한 자세한 정보를 공유 합니다.

**디바이스 템플릿**은 디바이스 및 IoT Edge 모듈의 기능을 정의합니다. 기능에는 모듈에서 보내는 원격 분석 데이터, 디바이스 속성 및 디바이스에서 응답하는 명령이 포함됩니다.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>게이트웨이 및 모듈과의 다운스트림 디바이스 관계

다운스트림 디바이스는 `$edgeHub` 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 이 시나리오의 IoT Edge 디바이스는 투명한 게이트웨이가 됩니다.

![투명한 게이트웨이의 다이어그램](./media/concepts-iot-edge/gateway-transparent.png)

다운스트림 디바이스는 사용자 지정 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수도 있습니다. 다음 시나리오에서 다운스트림 디바이스는 Modbus 사용자 지정 모듈을 통해 연결합니다.

![사용자 지정 모듈 연결의 다이어그램](./media/concepts-iot-edge/gateway-module.png)

다음 다이어그램에서는 두 가지 유형의 모듈(사용자 지정 및 `$edgeHub`)을 통한 IoT Edge 게이트웨이 디바이스에 대한 연결을 보여 줍니다.  

![두 연결 모듈을 통한 연결의 다이어그램](./media/concepts-iot-edge/gateway-module-transparent.png)

마지막으로, 다운스트림 디바이스는 여러 사용자 지정 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 다음 다이어그램에서는 Modbus 사용자 지정 모듈, BLE 사용자 지정 모듈 및 `$edgeHub` 모듈을 통해 연결하는 다운스트림 디바이스를 보여 줍니다. 

![여러 사용자 지정 모듈을 통한 연결의 다이어그램](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>배포 매니페스트 및 장치 템플릿

IoT Edge에서는 비즈니스 논리를 모듈 형태로 배포하고 관리할 수 있습니다. IoT Edge 모듈은 IoT Edge에서 관리하는 가장 작은 계산 단위이며, Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션 관련 코드를 포함할 수 있습니다. 모듈을 개발, 배포 및 유지 관리하는 방법을 이해하려면 [IoT Edge 모듈](../../iot-edge/iot-edge-modules.md)을 참조하세요.

상위 수준에서 배포 매니페스트는 원하는 속성으로 구성된 모듈 쌍의 목록입니다. 배포 매니페스트는 설치할 모듈과 이를 구성하는 방법을 IoT Edge 디바이스(또는 디바이스 그룹)에 알려줍니다. 배포 매니페스트에는 각 모듈 쌍의 원하는 속성이 포함되어 있습니다. IoT Edge 디바이스는 각 모듈에 대해 보고된 속성을 다시 보고합니다.

Visual Studio Code를 사용하여 배포 매니페스트 만들기 자세한 내용은 [Visual Studio Code용 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)를 참조하세요.

Azure IoT Central에서 배포 매니페스트를 가져와서 장치 템플릿을 만들 수 있습니다. 다음 순서도는 IoT Central의 배포 매니페스트 수명 주기를 보여 줍니다.

![배포 매니페스트 수명 주기 순서도](./media/concepts-iot-edge/dmflow.png)

IoT Central은 다음과 같이 IoT Edge 장치를 모델링 합니다.

* 모든 IoT Edge 디바이스 템플릿에는 디바이스 기능 모델이 있습니다.
* 배포 매니페스트에 나열된 모든 사용자 지정 모듈에 대한 모듈 기능 모델이 생성됩니다.
* 각 모듈 기능 모델과 디바이스 기능 모델 간의 관계가 설정됩니다.
* 모듈 기능 모델에서 모듈 인터페이스를 구현합니다.
* 각 모듈 인터페이스에는 원격 분석, 속성 및 명령이 포함됩니다.

![IoT Edge 모델링 다이어그램](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT Edge 게이트웨이 장치

IoT Edge 디바이스를 게이트웨이 디바이스로 선택한 경우 게이트웨이 디바이스에 연결하려는 디바이스의 디바이스 기능 모델에 다운스트림 관계를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

장치 개발자 인 경우 제안 되는 다음 단계는 [IoT Central의 게이트웨이 장치 유형에](./tutorial-define-gateway-device-type.md)대해 배우는 것입니다.
