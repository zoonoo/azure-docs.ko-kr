---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: de916fcbe0623185821e2f5da15a8f9cf71dfd4e
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426780"
---
### <a name="publish-the-device-template"></a>디바이스 템플릿 게시

애플리케이션에 디바이스를 추가하려면 먼저 디바이스 템플릿을 게시해야 합니다.

1. **LVA Edge 게이트웨이 v2** 디바이스 템플릿에서 **게시** 를 선택합니다.

1. **이 디바이스 템플릿을 애플리케이션에 게시** 페이지에서 **게시** 를 선택합니다.

이제 애플리케이션의 **디바이스** 페이지에서 디바이스 유형으로 **LVA Edge 게이트웨이 v2** 를 사용할 수 있습니다.

## <a name="migrate-the-gateway-device"></a>게이트웨이 디바이스 마이그레이션

기존 **gateway-001** 디바이스는 **LVA Edge 게이트웨이** 디바이스 템플릿을 사용합니다. 새 배포 매니페스트를 사용하려면 디바이스를 새 디바이스 템플릿으로 마이그레이션합니다.

**gateway-001** 디바이스를 마이그레이션하려면 다음을 수행합니다.

1. **디바이스** 페이지로 이동하여 **gateway-001** 디바이스를 선택하여 목록에서 강조 표시합니다.

1. **마이그레이션** 을 선택합니다. **마이그레이션** 아이콘이 표시되지 않는 경우 **...** 를 선택하여 추가 옵션을 볼 수 있습니다.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="게이트웨이 디바이스를 새 버전으로 마이그레이션":::

1. **마이그레이션** 대화 상자의 목록에서 **LVA Edge 게이트웨이 v2** 를 선택한 다음, **마이그레이션** 을 선택합니다.

몇 초 후 마이그레이션이 완료됩니다. 이제 디바이스에서 사용자 지정된 배포 매니페스트와 함께 **LVA Edge 게이트웨이 v2** 디바이스 템플릿을 사용하고 있습니다.

### <a name="get-the-device-credentials"></a>디바이스 자격 증명 가져오기

디바이스에서 IoT Central 애플리케이션에 연결할 수 있는 자격 증명이 필요합니다. 디바이스 자격 증명을 가져오려면 다음을 수행합니다.

1. **디바이스** 페이지에서 **gateway-001** 디바이스를 선택합니다.

1. **연결** 을 선택합니다.

1. **디바이스 연결** 페이지에서 **ID 범위**, **디바이스 ID** 및 디바이스 **기본 키** 를 *scratchpad.txt* 파일에 기록해 둡니다. 이러한 값은 나중에 사용합니다.

1. 연결 방법이 **공유 액세스 서명** 으로 설정되어 있는지 확인합니다.

1. **닫기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

**비디오 분석 - 개체 및 동작 감지** 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 만들고, 게이트웨이 디바이스에 대한 디바이스 템플릿을 만들고, 게이트웨이 디바이스를 애플리케이션에 추가했습니다.

시뮬레이션된 비디오 스트림을 사용하는 클라우드 VM을 실행하는 IoT Edge 모듈에서 비디오 분석 - 개체 및 동작 감지 애플리케이션을 사용해 보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

실제 **ONVIF** 카메라를 사용하는 실제 디바이스를 실행하는 IoT Edge 모듈에서 비디오 분석 - 개체 및 동작 감지 애플리케이션을 사용해 보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [비디오 분석용 IoT Edge 인스턴스 만들기(Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
