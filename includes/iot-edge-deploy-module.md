---
title: 포함 파일
description: 포함 파일
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/7/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 1a750a97cdc940c0f0a3d7e33d6be0d33f811425
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108025"
---
Azure IoT Edge의 주요 기능 중 하나는 클라우드에서 IoT Edge 디바이스에 모듈을 배포할 수 있다는 것입니다. IoT Edge 모듈은 컨테이너로 구현되는 실행 가능한 패키지입니다. 이 섹션에서는 [Azure Marketplace의 IoT Edge 모듈 섹션](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)에서 미리 빌드된 모듈을 배포합니다. 이 모듈은 시뮬레이션된 디바이스에 대한 원격 분석을 생성합니다.

1. Azure Portal의 검색 상자에서 `Simulated Temperature Sensor`를 입력하고 Marketplace 결과를 엽니다.

   ![Azure Portal 검색 상자의 시뮬레이션된 온도 센서](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. **구독** 필드에서 아직 없는 경우 사용 중인 IoT Hub가 있는 구독을 선택합니다.

3. **IoT Hub** 필드에서 아직 없는 경우 사용 중인 IoT Hub의 이름을 선택합니다.

4. **디바이스 찾기**를 클릭하고, IoT Edge 디바이스(`myEdgeDevice`)를 선택한 다음, **만들기**를 선택합니다.

5. 마법사의 **모듈 추가** 단계에서 **SimulatedTemperatureSensor** 모듈을 클릭하여 구성 설정을 확인한 다음, **저장**을 클릭하고, **다음**을 선택합니다.

6. 마법사의 **경로 지정** 단계에서 경로가 모든 모듈의 모든 메시지를 IoT Hub(`$upstream`)로 보내는 기본 경로로 올바르게 설정되어 있는지 확인합니다. 그렇지 않은 경우 다음 코드를 추가한 다음, **다음**을 선택합니다.

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. 마법사의 **배포 검토** 단계에서 **제출**을 선택합니다.

8. 디바이스 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다. 서비스를 처음 시작할 때 만들어진 edgeAgent 모듈 외에도, **edgeHub**라는 다른 런타임 모듈 및 **SimulatedTemperatureSensor** 모듈이 나열되어야 합니다.

   새 모듈이 표시되는 데 몇 분 정도 걸릴 수 있습니다. IoT Edge 디바이스는 클라우드에서 해당 새 배포 정보를 검색하고, 컨테이너를 시작하고, 새 상태를 IoT Hub에 다시 보고해야 합니다. 

   ![배포된 모듈 목록에서 SimulatedTemperatureSensor 보기](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
