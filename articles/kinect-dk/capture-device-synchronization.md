---
title: Azure Kinect 디바이스 동기화 캡처
description: Azure Kinect 센서 SDK를 사용하여 Azure Kinect 캡처 디바이스를 동기화하는 방법을 알아봅니다.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 깊이, rgb, 내부, 외부, 동기화, 데이지 체인, 단계 오프셋
ms.openlocfilehash: ce0c72d3d708d5696a9775b3885f278f0c23cac1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034316"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Azure Kinect 디바이스 동기화 캡처

Azure Kinect 하드웨어는 색 및 깊이 이미지의 캡처 시간을 조정할 수 있습니다. 동일한 디바이스에 있는 카메라 간의 정렬은 **내부 동기화** 입니다. 연결된 여러 디바이스 간에 캡처 시간 정렬은 **외부 동기화** 입니다. 마이크 배열은 색 및 깊이 카메라와 독립적으로 작동합니다.

## <a name="device-internal-synchronization"></a>디바이스 내부 동기화

개별 카메라 간의 이미지 캡처는 하드웨어에서 동기화됩니다. 색 및 깊이 센서의 이미지를 포함하는 모든 [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html)에서 이미지의 타임스탬프는 하드웨어의 운영 모드에 따라 정렬됩니다. 기본적으로 캡처의 이미지는 노출 중앙에 정렬됩니다. 깊이 및 색 캡처의 상대적 타이밍은 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)의 `depth_delay_off_color_usec` 필드를 사용하여 조정할 수 있습니다.

## <a name="device-external-synchronization"></a>디바이스 외부 동기화

하드웨어 설정은 [외부 동기화 설정](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)을 참조하세요.

연결된 각 디바이스의 소프트웨어는 **마스터** 또는 **종속** 모드에서 작동하도록 구성해야 합니다. 이 설정은 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)에서 구성됩니다.

외부 동기화를 사용하는 경우에는 타임스탬프가 제대로 맞춰지도록 항상 마스터보다 먼저 종속 카메라를 시작해야 합니다.

### <a name="subordinate-mode"></a>종속 모드

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>마스터 모드

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>동기화 잭 상태 검색

동기화 입력 및 동기화 출력 잭의 현재 상태를 프로그래밍 방식으로 검색하려면 [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) 함수를 사용합니다.

## <a name="next-steps"></a>다음 단계

이제 디바이스 동기화를 사용하도록 설정하고 캡처하는 방법을 알았습니다. 사용 방법을 검토할 수도 있습니다. 

>[!div class="nextstepaction"]
>[Azure Kinect 센서 SDK 기록 및 재생 API](record-playback-api.md)
