---
title: 포함 파일
description: 포함 파일
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: abbde0cb8137650faad563248c8fafe7dc0f469a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75564766"
---
Azure IoT Edge의 주요 기능 중 하나는 클라우드의 IoT Edge 디바이스에 코드를 배포하는 것입니다. **IoT Edge 모듈**은 컨테이너로 구현된 실행 패키지입니다. 이 섹션에서는 Azure IoT Hub에서 직접 [Azure Marketplace의 IoT Edge 모듈 섹션](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)에서 미리 빌드된 모듈을 배포합니다.

이 섹션에서 배포하는 모듈은 센서를 시뮬레이션하고 생성된 데이터를 보냅니다. 이 모듈은 시뮬레이션된 데이터를 개발 및 테스트에 사용할 수 있으므로 IoT Edge를 시작할 때 유용한 코드 조각입니다. [시뮬레이션된 온도 센서 소스 코드](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)를 보면 이 모듈이 하는 일을 정확하게 이해할 수 있습니다.

Azure Marketplace에서 첫 번째 모듈을 배포하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창 메뉴의 **자동 디바이스 관리**에서 **IoT Edge**를 선택합니다.

1. 디바이스 목록에서 대상 디바이스의 디바이스 ID를 클릭합니다.

1. 위쪽 막대에서 **모듈 설정**을 선택합니다.

1. 페이지의 **IoT Edge 모듈** 섹션에서 **추가**를 클릭합니다.

1. 드롭다운 메뉴에서 **Marketplace 모듈**을 선택합니다.

   ![Azure Portal 검색 상자의 시뮬레이션된 온도 센서](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. **IoT Edge 모듈 마켓플레이스**에서 "시뮬레이션된 온도 센서"를 검색하고 해당 모듈을 선택합니다.

1. SimulatedTemperatureSensor 모듈은 자동으로 채워집니다. 자습서의 이 페이지를 사용하여 배포에 모듈을 추가합니다. 이 빠른 시작에서는 이 모듈 하나만 배포합니다. 공용이기 때문에 자격 증명이 필요하지 않습니다.

   ![디바이스에서 모듈 설정](./media/iot-edge-deploy-module/set-modules-on-device.png)

   완료되면 **다음: 경로**를 선택하여 마법사의 다음 단계를 계속 진행합니다.

1. 마법사의 **경로** 탭에서 모듈과 IoT Hub 사이에서 메시지가 전달되는 방식을 정의합니다. 메시지는 이름/값 쌍을 사용하여 생성됩니다. 빠른 시작의 경우, 모든 모듈의 모든 메시지가 IoT Hub(`$upstream`)로 이동하도록 합니다. 자동으로 채워지지 않는 경우 **이름** `upstream`에 대한 **값**에 대해 다음 코드를 추가합니다.

   ```sql
    FROM /messages/* INTO $upstream
   ```

   완료되면 **다음: 검토 + 만들기**를 선택하여 마법사의 다음 단계를 계속 진행합니다.

1. 마법사의 **검토 + 만들기** 탭에서 IoT Edge 디바이스에 배포되는 모든 모듈을 정의하는 JSON 파일을 검토할 수 있습니다. **SimulatedTemperatureSensor** 모듈 외에도 **edgeAgent**와 **edgeHub**라는 시스템 모듈 두 개가 추가로 포함됩니다. 검토가 완료되면 **만들기**를 선택합니다.

   IoT Edge 디바이스에 새 배포를 제출할 때는 디바이스에 아무것도 푸시되지 않습니다. 대신 디바이스가 IoT Hub에 새로운 지침이 있는지를 정기적으로 쿼리합니다. 디바이스는 업데이트된 배포 매니페스트를 찾으면 새 배포에 대한 정보를 사용하여 클라우드에서 모듈 이미지를 끌어온 다음, 모듈을 로컬로 실행하기 시작합니다. 이 프로세스에 몇 분 정도 걸릴 수 있습니다.

1. 모듈 배포 세부 정보를 만들면 마법사는 IoT 허브의 **IoT Edge** 페이지로 돌아갑니다. IoT Edge 디바이스 목록에서 디바이스를 선택하면 세부 정보를 볼 수 있습니다.

1. 디바이스 세부 정보 페이지에서 **모듈** 탭까지 아래로 스크롤합니다. 세 가지 모듈 즉, $edgeAgent, $edgeHub, SimulatedTemperatureSensor가 나열되어 있습니다. 하나 이상의 모듈이 배포에 지정된 대로 나열되지만 디바이스를 통해 보고되지 않으면 IoT Edge 디바이스에서 아직 모듈을 시작하는 중입니다. 몇 분 정도 기다린 후 페이지 맨 위에서 **새로 고침**을 선택합니다.

   ![배포된 모듈 목록에서 SimulatedTemperatureSensor 보기](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
