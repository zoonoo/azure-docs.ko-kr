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
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876730"
---
### <a name="add-relationships"></a>관계 추가

**LVA Edge 게이트웨이** 디바이스 템플릿의 **모듈/LVA Edge 게이트웨이 모듈**에서 **관계**를 선택합니다. **+ 관계 추가**를 선택하고 다음 두 관계를 추가합니다.

|표시 이름               |속성          |대상 |
|-------------------------- |------------- |------ |
|LVA Edge 동작 감지기   |기본값 사용   |LVA Edge 동작 감지기 디바이스 |
|LVA Edge 개체 감지기   |기본값 사용   |LVA Edge 개체 감지기 디바이스 |

그런 다음 **저장**을 선택합니다.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="관계 추가":::

### <a name="add-views"></a>뷰 추가

**LVA Edge 게이트웨이** 디바이스 템플릿에는 보기 정의가 포함되지 않습니다.

디바이스 템플릿에 보기를 추가하려면 다음을 수행합니다.

1. **LVA Edge 게이트웨이** 디바이스 템플릿에서 **보기**로 이동하고, **디바이스 시각화** 타일을 선택합니다.

1. 보기 이름으로 *LVA Edge 게이트웨이 디바이스*를 입력합니다.

1. 다음 타일을 보기에 추가합니다.

    * **디바이스 정보** 속성을 포함하는 타일: **디바이스 모델**, **제조업체**, **운영 체제** **프로세서 아키텍처**, **소프트웨어 버전**, **총 메모리**, **총 스토리지**
    * **사용 가능한 메모리** 및 **시스템 하트비트** 원격 분석 값을 포함하는 꺾은선형 차트 타일
    * 다음 이벤트를 포함하는 이벤트 기록 타일: **Create Camera**, **Delete Camera**, **Module Restart**, **Module Started**, **Module Stopped**
    * **IoT Central 클라이언트 상태** 원격 분석을 표시하는 2x1 마지막으로 알려진 값 타일
    * **모듈 상태** 원격 분석을 표시하는 2x1 마지막으로 알려진 값 타일
    * **시스템 하트비트** 원격 분석을 표시하는 1x1 마지막으로 알려진 값 타일
    * **연결된 카메라** 원격 분석을 표시하는 1x1 마지막으로 알려진 값 타일

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="관계 추가":::

1. **저장**을 선택합니다.

### <a name="publish-the-device-template"></a>디바이스 템플릿 게시

애플리케이션에 디바이스를 추가하려면 먼저 디바이스 템플릿을 게시해야 합니다.

1. **LVA Edge 게이트웨이** 디바이스 템플릿에서 **게시**를 선택합니다.

1. **이 디바이스 템플릿을 애플리케이션에 게시** 페이지에서 **게시**를 선택합니다.

이제 애플리케이션의 **디바이스** 페이지에서 디바이스 유형으로 **LVA Edge 게이트웨이**를 사용할 수 있습니다.

## <a name="add-a-gateway-device"></a>게이트웨이 디바이스 추가

**LVA Edge 게이트웨이** 디바이스를 애플리케이션에 추가하는 방법은 다음과 같습니다.

1. **디바이스** 페이지로 이동하여 **LVA Edge 게이트웨이** 디바이스 템플릿을 선택합니다.

1. **+새로 만들기**를 선택합니다.

1. **새 디바이스 만들기** 대화 상자에서 디바이스 이름을 *LVA Gateway 001*로 변경하고 디바이스 ID를 *lva-gateway-001*로 변경합니다.

    > [!NOTE]
    > 디바이스 ID는 애플리케이션에서 고유해야 합니다.

1. **만들기**를 선택합니다.

디바이스가 상태가 **등록됨**입니다.

### <a name="get-the-device-credentials"></a>디바이스 자격 증명 가져오기

디바이스에서 IoT Central 애플리케이션에 연결할 수 있는 자격 증명이 필요합니다. 디바이스 자격 증명을 가져오려면 다음을 수행합니다.

1. **디바이스** 페이지에서, 앞에서 만든 **lva-gateway-001** 디바이스를 선택합니다.

1. **연결**을 선택합니다.

1. **디바이스 연결** 페이지에서 **ID 범위**, **디바이스 ID** 및 디바이스 **기본 키**를 *scratchpad.txt* 파일에 기록해 둡니다. 이러한 값은 나중에 사용합니다.

1. 연결 방법이 **공유 액세스 서명**으로 설정되어 있는지 확인합니다.

1. **닫기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

**비디오 분석 - 개체 및 동작 감지** 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 만들고, 게이트웨이 디바이스에 대한 디바이스 템플릿을 만들고, 게이트웨이 디바이스를 애플리케이션에 추가했습니다.

시뮬레이션된 비디오 스트림을 사용하는 클라우드 VM을 실행하는 IoT Edge 모듈에서 비디오 분석 - 개체 및 동작 감지 애플리케이션을 사용해 보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

실제 **ONVIF** 카메라를 사용하는 실제 디바이스를 실행하는 IoT Edge 모듈에서 비디오 분석 - 개체 및 동작 감지 애플리케이션을 사용해 보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [비디오 분석용 IoT Edge 인스턴스 만들기(Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
