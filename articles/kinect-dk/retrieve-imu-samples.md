---
title: Azure Kinect IMU 샘플 검색
description: Azure Kinect SDK를 사용 하 여 Azure Kinect IMU 샘플을 검색 하는 방법을 알아봅니다.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: kinect, azure, 구성, 깊이, 색, RBG, 카메라, 센서, sdk, IMU, 동작 센서, 동작, 자이로스코프가, gyro,가 속도계, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276945"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Azure Kinect IMU 샘플 검색

Azure Kinect 장치는가 속도계 및 자이로스코프가 유형을 포함 하 여 IMUs (관성 동작 단위)에 대 한 액세스를 제공 합니다. IMUs 샘플에 액세스 하려면 먼저 장치를 열고 구성한 다음 IMUS 데이터를 캡처해야 합니다. 자세한 내용은 [장치 찾기 및 열기](find-then-open-device.md)를 참조 하세요.

IMU 샘플은 이미지 보다 훨씬 더 높은 빈도로 생성 됩니다. 샘플은 샘플링 된 것 보다 낮은 속도로 호스트에 보고 됩니다. IMU 샘플을 대기 하는 경우 여러 샘플을 동시에 사용할 수 있게 됩니다.

IMU 보고 요금에 대 한 자세한 내용은 Azure Kinect 진한 [하드웨어 사양](hardware-specification.md) 을 참조 하세요.

## <a name="configure-and-start-cameras"></a>카메라 구성 및 시작

> [!NOTE]
> IMU 센서는 색 및/또는 깊이 카메라를 실행 하는 경우에만 작동할 수 있습니다. IMU 센서는 단독으로 작동할 수 없습니다.

카메라를 시작 하려면 [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)를 사용 합니다.

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

 각 [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) 에는 거의 동시에 캡처된가 속도계 및 자이로스코프가 읽기가 포함 됩니다.

이미지 캡처를 가져오는 동일한 스레드나 별도의 스레드에서 IMU 샘플을 가져올 수 있습니다.

IMU 샘플을 사용할 수 있는 즉시 검색 하려면 자체 스레드에서를 호출 하는 것이 좋습니다 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) . 또한 API에는 각 이미지 캡처가 반환 된 후에만 샘플을 확인할 수 있는 충분 한 내부 큐가 있습니다.

IMU 샘플의 내부 큐가 있기 때문에 데이터를 삭제 하지 않고 다음 패턴을 사용할 수 있습니다.

1. 모든 프레임 속도에서 캡처를 대기 합니다.
2. 캡처를 처리 합니다.
3. 대기 중인 모든 IMU 샘플을 검색 합니다.
4. 다음 캡처 대기를 반복 합니다.

현재 큐에 대기 중인 IMU 샘플을 모두 검색 하려면 [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) `timeout_in_ms` 함수가 반환 될 때까지 루프에서 0을 사용 하 여를 호출할 수 있습니다 `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT` 대기 중인 샘플이 없고 지정 된 시간 제한 내에 도착 한 항목이 없음을 나타냅니다.

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

이제 IMU 샘플을 사용 하는 방법을 배웠으므로 다음을 수행할 수도 있습니다.
>[!div class="nextstepaction"]
>[마이크 입력 데이터 액세스](access-mics.md)
