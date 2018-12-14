---
title: 포함 파일
description: 포함 파일
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 536857a5fe3ec3cc037f21835a4152f93197bcb8
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977400"
---
Azure IoT Edge의 주요 기능 중 하나는 클라우드에서 IoT Edge 디바이스에 모듈을 배포할 수 있다는 것입니다. IoT Edge 모듈은 컨테이너로 구현되는 실행 가능한 패키지입니다. 이 섹션에서는 시뮬레이트된 디바이스에 대한 원격 분석을 생성하는 모듈을 배포합니다.

1. Azure Portal에서 IoT Hub로 이동합니다.

2. **자동 장치 관리**의 **IoT Edge**로 이동하여 IoT Edge 장치를 선택합니다.

3. **모듈 설정**을 선택합니다. 포털에서는 모듈 추가, 경로 지정 및 배포 검토 과정을 안내하는 3단계 마법사가 열립니다. 

4. 마법사의 **모듈 추가** 단계에서 **배포 모듈** 섹션을 찾습니다. **추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.

   ![새 IoT Edge 모듈 추가](./media/iot-edge-deploy-module/add-module.png)

5. **이름** 필드에 `tempSensor`을 입력합니다.

6. **이미지 URI** 필드에 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`를 입력합니다.

7. 다른 설정은 변경하지 않고 **저장**을 선택합니다.

   ![이름 및 이미지 URI 입력 후 IoT Edge 모듈 저장](./media/iot-edge-deploy-module/name-image.png)

8. 마법사의 첫 번째로 돌아가 **다음**을 선택합니다.

9. 마법사의 **경로 지정** 단계에서 모든 메시지를 모든 모듈에서 IoT Hub로 보내는 기본 경로가 있어야 합니다. 그렇지 않은 경우 다음 코드를 추가한 다음, **다음**을 선택합니다.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

10. 마법사의 **배포 검토** 단계에서 **제출**을 선택합니다.

11. 디바이스 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다. 서비스를 처음 시작할 때 만들어진 edgeAgent 모듈 외에도 **edgeHub**라는 다른 런타임 모듈 및 **tempSensor** 모듈이 나열되어야 합니다.

   새 모듈이 표시되는 데 몇 분 정도 걸릴 수 있습니다. IoT Edge 디바이스는 클라우드에서 해당 새 배포 정보를 검색하고, 컨테이너를 시작하고, 새 상태를 IoT Hub에 다시 보고해야 합니다. 

   ![배포된 모듈 목록에서 tempSensor 보기](./media/iot-edge-deploy-module/deployed-modules.png)
