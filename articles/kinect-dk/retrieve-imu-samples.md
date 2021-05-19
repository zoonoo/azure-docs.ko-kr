---
title: Azure Kinect IMU 샘플 검색
description: Azure Kinect SDK를 사용하여 Azure Kinect IMU 샘플을 검색하는 방법을 알아봅니다.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: kinect, azure, 구성, 깊이, 색상, RBG, 카메라, 센서, sdk, IMU, 동작 센서, 동작, 자이로스코프, 자이로, 가속도계, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276945"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Azure Kinect IMU 샘플 검색

Azure Kinect 디바이스는 가속도계 및 자이로스코프 유형을 포함하여 IMU(관성 모션 장비)에 대한 액세스를 제공합니다. IMU 샘플에 액세스하려면 먼저 디바이스를 열어서 구성한 다음 IMU 데이터를 캡처해야 합니다. 자세한 내용은 [디바이스 찾기 및 열기](find-then-open-device.md)를 참조하세요.

IMU 샘플은 이미지보다 훨씬 높은 빈도로 생성됩니다. 샘플은 샘플링되는 것보다 낮은 속도로 호스트에 보고됩니다. IMU 샘플을 기다릴 때 여러 개의 샘플이 동시에 사용 가능하게 되는 경우가 자주 있습니다.

IMU 보고 속도에 대한 세부 정보는 Azure Kinect DK [하드웨어 사양](hardware-specification.md)을 참조하세요.

## <a name="configure-and-start-cameras"></a>카메라 구성 및 시작

> [!NOTE]
> IMU 센서는 색상 및/또는 깊이 카메라가 실행되고 있을 때만 작동합니다. IMU 센서는 단독으로 작동할 수 없습니다.

카메라를 시작하려면 [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)를 사용합니다.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>IMU 샘플 액세스

 각 [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details)에는 거의 동시에 캡처된 가속도계 및 자이로스코프 읽기가 포함됩니다.

이미지 캡처를 얻은 동일 스레드나 별도의 스레드에서 IMU 샘플을 얻을 수 있습니다.

IMU 샘플을 사용할 수 있게 되는 즉시 검색하기 위해 자체 스레드에서 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e)를 호출하려고 할 수도 있습니다. 또한 API에는 각 이미지 캡처가 반환된 후에만 샘플을 확인할 수 있도록 충분한 내부 대기열이 있습니다.

IMU 샘플의 내부 큐가 어느 정도 있기 때문에 데이터를 전혀 삭제하지 않고 다음과 같은 패턴을 사용할 수 있습니다.

1. 어떤 프레임 속도에서도 캡처를 기다립니다.
2. 캡처를 처리합니다.
3. 큐에 있는 IMU 샘플을 모두 검색합니다.
4. 다음 캡처를 반복해서 기다립니다.

현재 큐에 있는 IMU 샘플을 모두 검색하려면 함수가 `K4A_WAIT_RESULT_TIMEOUT`을 반환할 때까지 루프에서 `timeout_in_ms`가 0인 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e)을 호출할 수 있습니다. `K4A_WAIT_RESULT_TIMEOUT`은 큐에 있는 샘플이 없고 지정된 시간을 초과하여 도착한 샘플이 없다는 것을 나타냅니다.

## <a name="usage-example"></a>사용 예제

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>다음 단계

이제 IMU 샘플로 작업하는 방법을 배웠으므로 다음 작업을 수행할 수 있습니다.
>[!div class="nextstepaction"]
>[마이크 입력 데이터 액세스](access-mics.md)
