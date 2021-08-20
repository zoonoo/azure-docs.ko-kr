---
title: '자습서: Azure IoT Central을 사용하여 태양광 패널 모니터링 앱 만들기'
description: '자습서: Azure IoT Central 애플리케이션 템플릿을 사용하여 태양광 패널 애플리케이션을 만드는 방법에 대해 알아봅니다.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 369195e8dfce2440c2e34151e7de6b2d23edbad1
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112963260"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>자습서: 태양광 패널 모니터링 앱 템플릿 만들기 및 살펴보기 

이 자습서에서는 태양광 패널 모니터링 애플리케이션을 만드는 과정을 안내하며, 여기에는 시뮬레이션된 데이터를 사용하는 샘플 디바이스 모델이 포함됩니다. 이 자습서에서 다음에 대해 알아보겠습니다.


> [!div class="checklist"]
> * 무료로 태양광 패널 앱 만들기
> * 애플리케이션 살펴보기
> * 리소스 정리


구독이 없는 경우 [평가판 계정을 만듭니다](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 작업을 완료하기 위한 필수 구성 요소는 없습니다. Azure 구독을 권장하지만, 필수는 아닙니다.


## <a name="create-a-solar-panel-monitoring-app"></a>태양광 패널 모니터링 앱 만들기 

이 애플리케이션은 다음과 같은 세 가지 간단한 단계로 만들 수 있습니다.

1. [Azure IoT Central](https://apps.azureiotcentral.com)로 이동합니다. 새 애플리케이션을 만들려면 **빌드** 를 선택합니다. 

1. **에너지** 탭을 선택합니다. **태양광 패널 모니터링** 에서 **앱 만들기** 를 선택합니다. 

    > [!div class="mx-imgBorder"]
    > ![Azure IoT Central 빌드 옵션의 스크린샷.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. **새 애플리케이션** 대화 상자에서 요청된 정보를 입력한 다음, **만들기** 를 선택합니다.
    * **애플리케이션 이름**: Azure IoT Central 애플리케이션의 이름을 선택합니다. 
    * **URL**: Azure IoT Central URL을 선택합니다. 플랫폼은 고유성을 확인합니다.
    * **요금제**: Azure 구독이 이미 있는 경우 기본 설정을 사용하는 것이 좋습니다. Azure 구독이 없는 경우 평가판으로 시작하세요.
    * **청구 정보**: 애플리케이션 자체는 무료입니다. 앱의 리소스를 프로비저닝하려면 디렉터리, Azure 구독 및 영역 세부 정보가 필요합니다.
        ![새 애플리케이션의 스크린샷.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![청구 정보의 스크린샷.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>애플리케이션 및 시뮬레이션된 데이터 확인

언제든지 새 태양광 패널 앱을 수정할 수 있습니다. 지금은 앱을 수정하기 전에 앱이 배포되었고 예상대로 작동하는지 확인합니다.

앱 만들기 및 데이터 시뮬레이션을 확인하려면 **대시보드** 로 이동합니다. 데이터가 포함된 타일이 보이면 앱 배포에 성공한 것입니다. 데이터 시뮬레이션이 데이터를 생성하는 데 몇 분 정도 걸릴 수 있습니다. 

## <a name="application-walk-through"></a>애플리케이션 연습
앱 템플릿을 성공적으로 배포한 후에는 좀 더 자세히 살펴볼 수 있습니다. 샘플 스마트 미터 디바이스, 디바이스 모델 및 대시보드와 함께 제공됩니다.

Adatum은 태양광 패널을 모니터링 및 관리하는 가상의 에너지 회사입니다. 태양광 패널 모니터링 대시보드에는 태양광 패널 속성, 데이터 및 샘플 명령이 표시됩니다. 이 대시보드를 사용하면 문제가 발생하여 추가 지원이 필요하기 전에 사용자와 사용자의 지원 팀이 다음 활동을 사전에 수행할 수 있습니다.
* 지도에서 최신 패널 정보와 설치된 [위치](../core/howto-use-location-data.md)를 검토합니다.
* 패널 상태 및 연결 상태를 확인합니다.
* 에너지 생성 및 온도 추세를 검토하여 비정상적인 패턴을 파악합니다.
* 계획 및 청구를 위해 총 에너지 생성을 추적합니다.
* 패널을 활성화하고 필요한 경우 펌웨어 버전을 업데이트합니다. 템플릿의 명령 단추는 가능한 기능을 보여주지만 실제 명령을 보내지는 않습니다.

> [!div class="mx-imgBorder"]
> ![태양광 패널 모니터링 템플릿 대시보드의 스크린샷.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>디바이스
앱과 함께 샘플 태양광 패널 디바이스가 제공됩니다. 디바이스 세부 정보를 보려면 **디바이스** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![태양광 패널 모니터링 템플릿 디바이스의 스크린샷.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

샘플 디바이스 **SP0123456789** 를 선택합니다. **속성 업데이트** 탭에서 디바이스의 쓰기 가능 속성을 업데이트할 수 있고, 업데이트된 값을 대시보드에 표시할 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![태양광 패널 모니터링 템플릿 업데이트 탭의 스크린샷.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>디바이스 템플릿
태양광 패널 디바이스 모델을 보려면 **디바이스 템플릿** 탭을 선택합니다. 모델에는 데이터, 속성, 명령 및 보기에 대해 미리 정의된 인터페이스가 있습니다.

> [!div class="mx-imgBorder"]
> ![태양광 패널 모니터링 템플릿 디바이스 템플릿의 스크린샷.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>리소스 정리
이 애플리케이션이 더 이상 필요 없는 경우 다음 단계에 따라 애플리케이션을 삭제합니다.

1. 왼쪽 창에서 **관리** 를 선택합니다.
1. **애플리케이션 설정** > **설정** 을 선택합니다. 

    > [!div class="mx-imgBorder"]
    > ![태양광 패널 모니터링 템플릿 관리의 스크린샷.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>다음 단계
 
> [!div class="nextstepaction"]
> [Azure IoT Central - 태양광 패널 앱 아키텍처](./concept-iot-central-solar-panel-app.md)

