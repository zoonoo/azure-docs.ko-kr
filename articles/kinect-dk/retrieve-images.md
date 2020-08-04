---
title: Azure Kinect 이미지 데이터 검색
description: Kinect 센서 SDK를 사용 하 여 Azure Kinect image 데이터를 검색 하는 방법을 알아봅니다.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 검색, 센서, 카메라, sdk, 깊이, rgb, 이미지, 색, 캡처, 해상도, 버퍼
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87538917"
---
# <a name="retrieve-azure-kinect-image-data"></a>Azure Kinect 이미지 데이터 검색

이 페이지에서는 Azure Kinect에서 이미지를 검색 하는 방법에 대 한 세부 정보를 제공 합니다. 이 문서에서는 장치의 색과 깊이를 조정 하 여 이미지를 캡처 및 액세스 하는 방법을 보여 줍니다. 이미지에 액세스 하려면 먼저 장치를 열고 구성 해야 하며, 그런 다음 이미지를 캡처할 수 있습니다.
이미지를 구성 하 고 캡처하기 전에 [장치를 찾아 열어야](find-then-open-device.md)합니다.

이 문서에서 함수를 사용 하는 방법을 보여 주는 [SDK 스트리밍 예제](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) 를 참조할 수도 있습니다.

지원되는 함수는 다음과 같습니다.

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>장치 구성 및 시작

Kinect 장치에서 사용 가능한 두 개의 카메라는 여러 모드, 해상도 및 출력 형식을 지원 합니다. 전체 목록은 Azure Kinect Development Kit [하드웨어 사양](hardware-specification.md)을 참조 하세요.

스트리밍 구성은 구조체의 값을 사용 하 여 설정 됩니다 [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

카메라를 시작 하면 [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) 를 호출 하거나 장치를 닫을 때까지 계속 해 서 데이터를 캡처합니다.

## <a name="stabilization"></a>안정화

다중 장치 동기화 기능을 사용 하 여 장치를 시작 하는 경우 고정 된 노출 설정을 사용 하 여이 작업을 수행 하는 것이 좋습니다.
수동 노출 집합을 사용 하면 이미지와 프레임 속도가 안정화 되기 전에 장치에서 캡처를 8 개까지 걸릴 수 있습니다. 자동 노출을 사용 하면 이미지와 프레임 속도가 안정화 되기 전에 최대 20 개의 캡처를 사용할 수 있습니다.

## <a name="get-a-capture-from-the-device"></a>장치에서 캡처 가져오기

이미지는 상관 관계가 지정 된 방식으로 장치에서 캡처됩니다. 캡처된 각 이미지에는 깊이 이미지, IR 이미지, 색 이미지 또는 이미지 조합이 포함 됩니다.

기본적으로 API는 스트리밍 모드에 대해 요청 된 모든 이미지를 받은 후에만 캡처를 반환 합니다. 의 매개 변수를 지워 해당 이미지를 사용할 수 있는 즉시 깊이 또는 색 이미지만 포함 하 여 부분 캡처를 반환 하도록 API를 구성할 수 있습니다 [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

API에서 캡처를 성공적으로 반환한 후에는 캡처 개체 사용을 완료 한 경우를 호출 해야 합니다 [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) .

## <a name="get-an-image-from-the-capture"></a>캡처에서 이미지 가져오기

캡처된 이미지를 검색 하려면 각 이미지 형식에 대해 적절 한 함수를 호출 합니다. 다음 중 하나:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

[`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) 이미지 사용을 완료 한 후에는 이러한 함수가 반환 하는 핸들에서를 호출 해야 합니다.

## <a name="access-image-buffers"></a>이미지 버퍼 액세스

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html)에는 이미지의 속성을 가져오기 위한 많은 접근자 함수가 있습니다.

이미지의 메모리 버퍼에 액세스 하려면 [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)를 사용 합니다.

다음 예제에서는 캡처된 깊이 이미지에 액세스 하는 방법을 보여 줍니다. 이와 동일한 원칙이 다른 이미지 형식에도 적용 됩니다. 그러나 이미지 형식 변수를 IR 또는 색과 같은 올바른 이미지 형식으로 바꾸어야 합니다.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>다음 단계

이제 Azure Kinect 장치를 사용 하 여 색과 깊이 사이에서 카메라 이미지를 캡처 및 조정 하는 방법을 알고 있습니다. 다음을 수행할 수도 있습니다.

>[!div class="nextstepaction"]
>[IMU 샘플 검색](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[마이크 액세스](access-mics.md)
