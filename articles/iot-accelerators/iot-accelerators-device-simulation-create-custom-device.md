---
title: 사용자 지정 시뮬레이션된 장치 만들기 - Azure | Microsoft Docs
description: 이 자습서에서는 장치 시뮬레이션을 사용하여 시뮬레이션에서 사용하기 위한 사용자 지정 시뮬레이션된 장치를 만듭니다.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 302b863e7ad7d6df286adf53342356f279ab92d2
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50756791"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>자습서: 사용자 지정 시뮬레이션된 장치 만들기

이 자습서에서는 장치 시뮬레이션을 사용하여 시뮬레이션에서 사용하기 위한 사용자 지정 시뮬레이션된 장치를 만듭니다. 장치 시뮬레이션을 시작하려면 포함된 샘플 시뮬레이션된 장치 중 하나를 사용할 수 있습니다. 또한 이 문서에 설명된 대로 사용자 지정 시뮬레이션된 장치를 만들 수 있습니다. 더 많은 사용자 지정 옵션은 [고급 장치 모델 만들기](iot-accelerators-device-simulation-advanced-device.md)를 참조하세요.

이 자습서에서는 다음을 수행했습니다.

>[!div class="checklist"]
> * 시뮬레이션된 장치 모델의 목록 보기
> * 사용자 지정 시뮬레이션된 장치 만들기
> * 장치 모델 복제
> * 장치 모델 삭제

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 실행하려면 Azure 구독에서 장치 시뮬레이션의 배포된 인스턴스가 필요합니다.

장치 시뮬레이션을 아직 배포하지 않은 경우 [Azure에서 IoT 장치 시뮬레이션 배포 및 실행](quickstart-device-simulation-deploy.md) 빠른 시작을 완료해야 합니다.

## <a name="open-device-simulation"></a>장치 시뮬레이션 열기

브라우저에서 장치 시뮬레이션을 실행하려면 먼저 [Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com)로 이동합니다.

Azure 구독 자격 증명을 사용하여 로그인하라는 메시지가 표시될 수 있습니다.

그런 다음, [Azure에서 IoT 장치 시뮬레이션 배포 및 실행](quickstart-device-simulation-deploy.md) 빠른 시작에서 배포한 장치 시뮬레이션에 대한 타일에서 **시작**을 클릭합니다.

## <a name="view-your-device-models"></a>장치 모델 보기

메뉴 모음에서 **장치 모델**을 선택합니다. **장치 모델** 페이지는 이 장치 시뮬레이션의 인스턴스에 있는 사용 가능한 모든 장치 모델을 나열합니다.

![장치 모델](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>장치 모델 만들기

페이지의 오른쪽 위 모서리에서 **+ 장치 모델 추가**를 클릭합니다.

![장치 모델 추가](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

이 자습서에서는 온도 및 습도 데이터를 보내는 시뮬레이션된 냉장고를 만듭니다.

다음 정보로 양식을 작성합니다.

| 설정             | 값                                                |
| ------------------- | ---------------------------------------------------- |
| 장치 모델 이름   | 냉장고                                         |
| 모델 설명   | 온도 및 습도 센서가 있는 냉장고 |
| 버전             | 1.0                                                  |

> [!NOTE]
> 장치 모델 이름은 고유해야 합니다.

**+ 데이터 요소 추가**를 클릭하여 다음 값으로 온도 및 습도에 대한 데이터 요소를 추가합니다.

| 데이터 요소          | 동작        | 최소값 | 최대값 | 단위 |
| ------------------- | --------------- | --------- | --------- | ---- |
| 온도         | 임의          | -50       | 100       | F    |
| 습도            | 임의          | 0         | 100       | %    |

**저장**을 클릭하여 장치 모델을 저장합니다.

![장치 모델 만들기](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

이제 냉장고가 장치 모델 목록에 포함됩니다. 냉장고를 확인하려면 **다음**을 클릭하여 다른 페이지로 이동해야 할 수도 있습니다.

## <a name="clone-a-device-model"></a>장치 모델 복제

장치 모델을 복제하여 기존 장치 모델의 복사본을 만들 수 있습니다. 그런 다음, 특정 요구 사항에 맞게 복사본을 편집할 수 있습니다. 복제는 유사한 장치 모델을 생성해야 하는 경우 시간을 절약합니다.

장치 모델을 복제하려면 모델 옆의 상자를 체크한 다음, 작업 모음에서 **복제**를 클릭합니다.

![장치 모델 삭제](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>장치 모델 삭제

모든 사용자 지정 장치 모델을 삭제할 수 있습니다. 장치 모델을 삭제하려면 모델 옆의 상자를 체크한 다음, 작업 모음에서 **삭제**를 클릭합니다.

![장치 모델 삭제](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 지정 장치 모델을 생성, 복제 및 삭제하는 방법을 알아보았습니다. 장치 모델에 대해 자세히 알아보려면 다음 방법 문서를 참조하세요.

> [!div class="nextstepaction"]
> [고급 장치 모델 만들기](iot-accelerators-device-simulation-advanced-device.md)