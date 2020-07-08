---
title: Azure Kinect 장치 동기화 캡처
description: Azure Kinect 센서 SDK를 사용 하 여 Azure Kinect 캡처 장치를 동기화 하는 방법을 알아봅니다.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 깊이, rgb, 내부, 외부, 동기화, 데이지 체인, 단계 오프셋
ms.openlocfilehash: 914a29882277c11c388d3f81f0b1fe827fc2f156
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276958"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Azure Kinect 장치 동기화 캡처

Azure Kinect 하드웨어는 색 및 깊이 이미지의 캡처 시간을 맞출 수 있습니다. 동일한 장치의 카메라 간 맞춤은 **내부 동기화**입니다. 연결 된 여러 장치에 걸친 캡처 시간 정렬은 **외부 동기화**입니다.

## <a name="device-internal-synchronization"></a>장치 내부 동기화

하드웨어에서 개별 카메라 간의 이미지 캡처가 동기화 됩니다. 색 및 깊이 센서의 이미지를 모두 포함 하는 모든 [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) 에서 이미지의 타임 스탬프는 하드웨어의 운영 모드를 기준으로 정렬 됩니다. 기본적으로 캡처의 이미지는 맞춤의 중심입니다. 깊이 및 색 캡처의 상대적 타이밍은 `depth_delay_off_color_usec` [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)의 필드를 사용 하 여 조정할 수 있습니다.

## <a name="device-external-synchronization"></a>장치 외부 동기화

하드웨어 설정에 대 한 [외부 동기화 설정](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) 을 참조 하세요.

연결 된 각 장치에 대 한 소프트웨어는 **마스터** 또는 **하위** 모드에서 작동 하도록 구성 해야 합니다. 이 설정은 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)구성 됩니다.

외부 동기화를 사용 하는 경우에는 타임 스탬프가 제대로 맞춰지도록 마스터를 위해 항상 하위 카메라를 시작 해야 합니다.

### <a name="subordinate-mode"></a>하위 모드

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>마스터 모드

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>동기화 잭 상태를 검색 하는 중

동기화 입력 및 동기화 출력 잭에 대 한 현재 상태를 프로그래밍 방식으로 검색 하려면 [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) 함수를 사용 합니다.

## <a name="next-steps"></a>다음 단계

이제 장치 동기화를 사용 하도록 설정 하 고 캡처하는 방법을 알고 있습니다. 또한를 사용 하는 방법을 검토할 수 있습니다. 

>[!div class="nextstepaction"]
>[Azure Kinect 센서 SDK 레코드 및 재생 API](record-playback-api.md)
