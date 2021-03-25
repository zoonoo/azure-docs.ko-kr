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
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832075"
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

이제 원래 **LVA Edge Gateway** 디바이스 템플릿을 사용하는 디바이스가 없습니다. 이 디바이스 템플릿 삭제:

1. **디바이스 템플릿** 페이지로 이동하여 **LVA Edge 게이트웨이** 디바이스 템플릿을 선택합니다.

1. **삭제** 를 선택하여 디바이스 템플릿을 삭제합니다.

### <a name="get-the-device-credentials"></a>디바이스 자격 증명 가져오기

디바이스에서 IoT Central 애플리케이션에 연결할 수 있는 자격 증명이 필요합니다. 디바이스 자격 증명을 가져오려면 다음을 수행합니다.

1. **디바이스** 페이지에서 **gateway-001** 디바이스를 선택합니다.

1. **연결** 을 선택합니다.

1. **디바이스 연결** 페이지에서 **ID 범위**, **디바이스 ID** 및 디바이스 **기본 키** 를 *scratchpad.txt* 파일에 기록해 둡니다. 이러한 값은 나중에 사용합니다.

1. 연결 방법이 **공유 액세스 서명** 으로 설정되어 있는지 확인합니다.

1. **닫기** 를 선택합니다.

