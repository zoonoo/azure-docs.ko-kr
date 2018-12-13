---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4a4ec51430242cc3e3d6a0b801f2b4be7858ab50
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516119"
---
<!-- put the ## header in the file that includes this file -->

이 섹션에서는 IoT 허브의 ID 레지스트리에 디바이스 ID를 만듭니다. ID 레지스트리에 항목이 없는 경우 디바이스를 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](../articles/iot-hub/iot-hub-devguide-identity-registry.md)의 “ID 레지스트리” 섹션을 참조하세요. 

1. IoT 허브 탐색 메뉴에서 **IoT 디바이스**를 열고 **추가**를 클릭하여 IoT 허브에 새 디바이스를 등록합니다.

    ![포털에서 장치 ID 만들기](./media/iot-hub-include-create-device/create-identity-portal.png)

1. **myDeviceId**와 같은 새 장치의 이름을 입력하고 **저장**을 클릭합니다. 이 작업은 IoT Hub에 대한 새 장치 ID를 만듭니다.

   ![새 장치 추가](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. 장치가 만들어진 후 **IoT 장치** 창의 목록에서 장치를 엽니다. 나중에 사용하기 위해 **연결 문자열---기본 키**를 복사합니다.

    ![디바이스 연결 문자열](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 디바이스 ID 및 키와 개별 디바이스에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 애플리케이션이 다른 디바이스별 메타데이터를 저장해야 할 경우 애플리케이션별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드](../articles/iot-hub/iot-hub-devguide-identity-registry.md)를 참조하세요.
