---
title: 포함 파일
description: 포함 파일
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: dd4873017105db190f9a468ec1f1f77f4e8c9c0e
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977130"
---
Azure IoT Edge의 주요 기능 중 하나는 클라우드에서 IoT Edge 디바이스에 모듈을 배포할 수 있다는 것입니다. IoT Edge 모듈은 컨테이너로 구현되는 실행 가능한 패키지입니다. 이 섹션에서는 [Azure Marketplace의 IoT Edge 모듈 섹션](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)에서 미리 빌드된 모듈을 배포합니다. 이 모듈은 IoT Edge 디바이스에 대해 시뮬레이션된 원격 분석 데이터를 생성합니다.

1. [Azure Portal](https://portal.azure.com)의 검색 상자에 **시뮬레이션된 온도 센서**를 입력하고 Marketplace 결과를 엽니다.

   ![Azure Portal 검색 상자의 시뮬레이션된 온도 센서](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. 이 모듈을 수신할 IoT Edge 디바이스를 선택합니다. **IoT Edge 모듈의 대상 디바이스**에 다음 정보를 제공합니다.

   1. **구독**: 사용 중인 IoT 허브가 포함된 구독을 선택합니다.

   2. **IoT Hub**: 사용 중인 IoT 허브의 이름을 선택합니다.

   3. **IoT Edge 디바이스 이름**: 이 빠른 시작의 앞부분에 제안된 디바이스 이름을 사용한 경우 **myEdgeDevice**를 입력합니다. 아니면 **디바이스 찾기**를 선택하고 IoT 허브에 있는 디바이스 목록에서 선택합니다. 
   
   4. **만들기**를 선택합니다.

3. Azure Marketplace에서 IoT Edge 모듈을 선택하고 모듈을 수신할 IoT Edge 디바이스를 선택하면 3단계 마법사로 이동하여 모듈 배포 방식을 정확하게 정의할 수 있습니다. 마법사의 **모듈 추가** 단계에서 **SimulatedTemperatureSensor** 모듈이 자동으로 채워지는 것에 유의합니다. 자습서에서 이 페이지를 사용하여 배포에 모듈을 추가합니다. 이 빠른 시작에서는 이 모듈 하나만 배포합니다. **다음**을 선택하여 마법사의 다음 단계로 진행합니다.

4. 마법사의 **경로 지정** 단계에서 모듈과 IoT Hub 사이에서 메시지가 전달되는 방식을 정의합니다. 빠른 시작의 경우, 모든 모듈의 모든 메시지가 IoT Hub(`$upstream`)로 이동하도록 합니다. 자동으로 채워지지 않으면 다음 코드를 추가한 후에 **다음**을 선택합니다.

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

5. 마법사의 **배포 검토** 단계에서 IoT Edge 디바이스에 배포되는 모든 모듈을 정의하는 JSON 파일을 검토할 수 있습니다. **SimulatedTemperatureSensor** 모듈은 물론 **edgeAgent**와 **edgeHub**라는 시스템 모듈 두 개가 추가로 포함됩니다. 검토를 완료하면 **제출**을 선택합니다.

   IoT Edge 디바이스에 새 배포를 제출할 때는 디바이스에 아무것도 푸시되지 않습니다. 대신 디바이스가 IoT Hub에 새로운 지침이 있는지를 정기적으로 쿼리합니다. 새로운 배포 정보가 보이면 디바이스는 이것을 사용하여 클라우드에서 모듈 이미지를 끌어와서 로컬에서 모듈을 실행하기 시작합니다. 이 프로세스에 몇 분 정도 걸릴 수 있습니다. 

6. 모듈 배포 세부 정보를 제출하면 마법사는 IoT 허브의 **IoT Edge** 페이지로 돌아갑니다. IoT Edge 디바이스 목록에서 디바이스를 선택하면 세부 정보를 볼 수 있습니다. 

7. 디바이스 세부 정보 페이지에서 **모듈** 섹션까지 아래로 스크롤합니다. 세 가지 모듈 즉, $edgeAgent, $edgeHub, SimulatedTemperatureSensor가 나열되어 있습니다. 하나 이상의 모듈이 배포에 지정된 대로 나열되지만 디바이스를 통해 보고되지 않으면, IoT Edge 디바이스에서 모듈이 아직 시작되고 있는 경우입니다. 몇 분 정도 기다린 후 페이지 맨 위에서 **새로 고침**을 선택합니다. 

   ![배포된 모듈 목록에서 SimulatedTemperatureSensor 보기](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
