---
title: Azure IoT Hub에 대한 디바이스 업데이트에서 디바이스 그룹 만들기 | Microsoft Docs
description: Azure IoT Hub에 대한 디바이스 업데이트에서 디바이스 그룹 만들기
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679519"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>IoT Hub에 대한 디바이스 업데이트에서 디바이스 그룹 만들기
IoT Hub에 대한 디바이스 업데이트를 통해 IoT 디바이스 그룹에 업데이트를 배포할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [IoT Hub용 디바이스 업데이트를 사용으로 설정하여 IoT Hub에 액세스합니다](create-device-update-account.md). IoT Hub에 대해 S1(표준) 계층 이상을 사용하는 것이 좋습니다. 
* IoT Hub에서 디바이스 업데이트를 위해 프로비저닝된 IoT 디바이스(또는 시뮬레이터).
* [프로비전된 디바이스에 대한 업데이트를 하나 이상 가져왔습니다.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>디바이스에 태그 추가  

IoT Hub에 대한 디바이스 업데이트를 통해 IoT 디바이스 그룹에 업데이트를 배포할 수 있습니다. 그룹 만들기의 첫 번째 단계는 IoT Hub의 대상 디바이스 집합에 태그를 추가하는 것입니다. 태그는 디바이스 업데이트에 연결된 후에만 디바이스에 추가될 수 있습니다.

아래 문서에서는 태그를 추가하고 업데이트하는 방법을 설명합니다.

### <a name="programmatically-update-device-twin"></a>프로그래밍 방식으로 디바이스 쌍 업데이트

디바이스 업데이트를 사용하여 디바이스를 등록한 후에 RegistryManager를 사용하여 디바이스 쌍을 적절한 태그로 업데이트할 수 있습니다. 
[샘플 .NET 앱을 사용하여 태그를 추가하는 방법을 알아봅니다.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[태그 속성에 대해 알아봅니다](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>디바이스 업데이트 태그 형식

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>작업 사용

[이러한](../iot-hub/iot-hub-devguide-jobs.md) 예시에 따라 디바이스 업데이트 태그를 추가하거나 업데이트하기 위해 여러 디바이스에서 작업을 예약할 수 있습니다. [자세히 알아보기](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > 이 작업은 현재 IOT Hub 메시지 할당량을 기반으로 하며, 한 번에 최대 5만 개의 디바이스 쌍 태그만 변경하는 것이 좋습니다. 일일 IoT Hub 메시지 할당량을 초과하는 경우, IoT Hub 단위를 추가로 구입해야 할 수 있습니다. 자세한 내용은 [할당량 및 제한](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling)에서 확인할 수 있습니다.

### <a name="direct-twin-updates"></a>직접 쌍 업데이트

디바이스 쌍에서 태그를 직접 추가하거나 업데이트할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하여 IoT 허브로 이동합니다.

2. 왼쪽 탐색 창의 'IoT 디바이스' 또는 'IoT Edge'에서 IoT 디바이스를 찾아 디바이스 쌍으로 이동합니다.

3. 디바이스 쌍에서 기존 디바이스 업데이트 태그 값을 null로 설정하여 삭제합니다.

4. 아래와 같이 새 디바이스 업데이트 태그 값을 추가합니다. [태그가 있는 디바이스 쌍 JSON 문서 예시입니다.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>제한 사항

* 예약된 값인 '범주화되지 않음'을 제외하고 모든 값을 태그에 추가할 수 있습니다.
* 태그 값은 255자를 초과할 수 없습니다.
* 태그 값에는 영숫자 문자와 특수 문자 ".", "-", "_", "~"을 사용할 수 있습니다.
* 태그 및 그룹 이름은 대/소문자를 구분합니다.
* 디바이스에는 이름 ADUGroup을 가진 태그를 하나만 포함할 수 있습니다. 해당 이름의 태그를 추가하면 태그 이름 ADUGroup의 기존 값이 재정의됩니다.
* 한 디바이스는 하나의 그룹에만 속할 수 있습니다.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>기존 IoT Hub 태그를 선택하여 디바이스 그룹 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 이전에 디바이스 업데이트 인스턴스에 연결한 IoT Hub를 선택합니다.

3. 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.

4. 페이지 맨 위에서 그룹 탭을 선택합니다. 아직 그룹으로 묶이지 않은 디바이스 업데이트에 연결된 디바이스 수를 확인할 수 있습니다.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="그룹으로 묶이지 않은 디바이스의 스크린샷" lightbox="media/create-update-group/ungrouped-devices.png":::

5. 추가 단추를 선택하여 새 그룹을 만듭니다.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="디바이스 그룹 추가의 스크린샷" lightbox="media/create-update-group/add-group.png":::

6. 목록에서 IoT Hub 태그를 선택하고 업데이트 그룹 만들기를 선택합니다.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="태그 선택의 스크린샷" lightbox="media/create-update-group/select-tag.png":::

7. 그룹이 만들어지면 업데이트 규정 준수 차트 및 그룹 목록이 업데이트된 것을 볼 수 있습니다.  업데이트 규정 준수 차트는 최신 업데이트, 사용 가능한 새 업데이트, 진행 중인 업데이트 및 아직 그룹화되지 않은 디바이스에 대한 다양한 준수 상태의 디바이스 수를 표시합니다. [업데이트 규정 준수에 대해 알아봅니다.](device-update-compliance.md)
   :::image type="content" source="media/create-update-group/updated-view.png" alt-text="업데이트 규정 준수 보기의 스크린샷." lightbox="media/create-update-group/updated-view.png":::

8. 새로 만든 그룹 및 새 그룹의 디바이스에 사용 가능한 업데이트가 표시되어야 합니다. 업데이트 이름을 클릭하여 이 보기에서 새 그룹에 업데이트를 배포할 수 있습니다. 자세한 내용은 다음 단계: 업데이트 배포를 참조하세요.

## <a name="view-device-details-for-the-group-you-created"></a>만든 그룹의 디바이스 세부 정보 보기

1. 새로 만든 그룹으로 이동하고 그룹 이름을 클릭합니다.

2. 그룹의 일부인 디바이스 목록이 해당 디바이스 업데이트 속성과 함께 표시됩니다. 이 보기에서 해당 그룹의 구성 요소인 모든 디바이스의 업데이트 규정 준수 정보를 볼 수도 있습니다. 업데이트 규정 준수 차트는 최신 업데이트, 사용 가능한 새 업데이트 및 진행 중인 업데이트에 대한 다양한 준수 상태의 디바이스 수를 표시합니다.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="디바이스 그룹 세부 정보 보기의 스크린샷" lightbox="media/create-update-group/group-details.png":::

3. 그룹 내의 각 개별 디바이스를 클릭하여 IoT Hub의 디바이스 세부 정보 페이지로 리디렉션할 수도 있습니다.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="디바이스 그룹 세부 정보 보기의 스크린샷" lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>다음 단계 

[업데이트 배포](deploy-update.md)

디바이스 그룹에 대해 [자세히 알아봅니다](device-update-groups.md)

[업데이트 규정 준수에 대해 알아봅니다.](device-update-compliance.md)
