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
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876725"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Azure IoT Edge 게이트웨이 디바이스 만들기

비디오 분석 - 개체 및 동작 감지 애플리케이션에는 **LVA Edge 개체 감지기** 디바이스 템플릿과 **LVA Edge 동작 감지** 디바이스 템플릿이 포함되어 있습니다. 이 섹션에서는 배포 매니페스트를 사용하여 게이트웨이 디바이스 템플릿을 만들고, IoT Central 애플리케이션에 게이트웨이 디바이스를 추가합니다.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>LVA Edge 게이트웨이에 대한 디바이스 템플릿 만들기

배포 매니페스트를 가져오고 **LVA Edge 게이트웨이** 디바이스 템플릿을 만드는 방법은 다음과 같습니다.

1. IoT Central 애플리케이션에서 **디바이스 템플릿**으로 이동하고 **+ 새로 만들기**를 선택합니다.

1. **템플릿 유형 선택** 페이지에서 **Azure IoT Edge** 타일을 선택합니다. 그런 다음, **Next: 사용자 지정**을 선택합니다.

1. **Azure IoT Edge 배포 매니페스트 업로드** 페이지에서 템플릿 이름으로 *LVA Edge 게이트웨이*를 입력하고, **다운스트림 디바이스를 사용하는 게이트웨이 디바이스**를 확인합니다.

    아직 배포 매니페스트를 검색하지 마세요. 검색할 경우 배포 마법사에는 각 모듈의 인터페이스가 필요하지만, 우리는 **LvaEdgeGatewayModule**에 대한 인터페이스만 공개해야 합니다. 이후 단계에서 매니페스트를 업로드합니다.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="배포 매니페스트를 다운로드하지 말 것":::

    완료되면 **다음: 검토**를 클릭합니다.

1. **검토** 페이지에서 **만들기**를 선택합니다.

### <a name="import-the-device-capability-model"></a>디바이스 기능 모델 가져오기

디바이스 템플릿에는 디바이스 기능 모델이 포함되어야 합니다. **LVA Edge 게이트웨이** 페이지에서 **기능 모델 가져오기** 타일을 선택합니다. 앞에서 만든 *lva-configuration* 폴더로 이동하여 *LvaEdgeGatewayDcm.json* 파일을 선택합니다.

이제 **LVA Edge 게이트웨이** 디바이스 템플릿에는 **LVA Edge 게이트웨이 모듈**과 함께 **디바이스 정보**, **LVA Edge 게이트웨이 설정** 및 **LVA Edge 게이트웨이 인터페이스**가 포함되어 있습니다.
