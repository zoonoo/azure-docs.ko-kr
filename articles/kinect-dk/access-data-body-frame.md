---
title: 신체 프레임에서 Azure Kinect DK 데이터 액세스
description: 신체 프레임의 데이터 및 Azure Kinect DK의 해당 데이터에 액세스하는 함수에 대해 알아봅니다.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: 신체, 프레임, azure, kinect, 신체, 추적, 팁
ms.openlocfilehash: be44f59cb84e99129bf526575293eee69ca64598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277044"
---
# <a name="access-data-in-body-frame"></a>신체 프레임의 데이터에 액세스

이 문서에서는 신체 프레임에 포함된 데이터와 해당 데이터에 액세스하는 함수에 대해 설명합니다.

지원되는 함수는 다음과 같습니다.

- [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)
- [k4abt_frame_get_body_index_map()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)
- [k4abt_frame_get_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)
- [k4abt_frame_get_device_timestamp_usec()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>신체 프레임의 주요 구성 요소

각 신체 프레임에는 신체 구조 컬렉션, 2D 신체 인덱스 맵 및 이 결과가 생성된 입력 캡처가 포함되어 있습니다.

![신체 프레임 구성 요소](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>신체 구조의 컬렉션 액세스

단일 캡처에 여러 신체가 감지될 수 있습니다. [k4abt_frame_get_num_bodies()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3) 함수를 호출하여 신체의 수를 쿼리할 수 있습니다.

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

신체 ID 및 관절 위치/방향 정보를 찾기 위해 각 신체 인덱스를 통해 반복하려면 [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) 및 [k4abt_frame_get_body_skeleton()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb) 함수를 사용합니다.

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>신체 인덱스 맵 액세스

신체 인덱스 맵에 액세스하려면 [k4abt_frame_get_body_index_map()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6) 함수를 사용합니다. 신체 인덱스 맵에 대한 자세한 설명은 [신체 인덱스 맵](body-index-map.md)을 참조하세요. 신체 인덱스 맵이 더 이상 필요하지 않을 때는 이를 해제해야 합니다.

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>입력 캡처 액세스

신체 추적기는 비동기 API입니다. 결과가 표시될 때 원본 캡처가 이미 해제되었을 수 있습니다. 이 신체 추적 결과를 생성하는 데 사용된 입력 캡처를 쿼리하려면 [k4abt_frame_get_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a) 함수를 사용합니다. 이 함수가 호출 될 때마다 k4a_capture_t에 대 한 참조 횟수가 증가합니다. 캡처가 더 이상 필요하지 않을 때 [k4a_capture_release()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) 함수를 사용합니다.

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Kinect 신체 추적 SDK](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
