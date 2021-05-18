---
title: Azure Kinect 디바이스 찾기 및 열기
description: Azure Kinect Senor SDK를 사용하여 Azure Kinect 디바이스를 찾고 여는 방법을 알아봅니다.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 깊이, rgb, 디바이스, 찾기, 열기
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276921"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Azure Kinect 디바이스 찾기 및 열기

이 문서에서는 Azure Kinect DK를 찾은 다음 여는 방법을 설명합니다. 이 문서에서는 컴퓨터에 여러 개의 디바이스가 연결되어 있는 경우 이를 처리하는 방법을 설명합니다.

이 문서에서 함수를 사용하는 방법을 보여 주는 [SDK 열거 예](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate)를 참조할 수도 있습니다.

지원되는 함수는 다음과 같습니다.
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>연결된 디바이스 수 검색

먼저 [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)를 사용하여 현재 연결되어 있는 Azure Kinect 디바이스 개수를 얻습니다.

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>디바이스 열기

디바이스에 대한 정보를 얻거나 해당 디바이스에서 데이터를 읽으려면 먼저 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)을 사용하여 디바이스에 대한 핸들을 열어야 합니다.

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

[`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)의 `index` 매개 변수는 둘 이상의 디바이스가 연결되어 있는 경우 열 디바이스를 나타냅니다. 단일 디바이스가 연결되어 있는 것으로 예상되면 `K4A_DEVICE_DEFAULT` 인수나 0을 전달하여 첫 번째 디바이스를 나타낼 수 있습니다.

디바이스를 열 때는 항상 핸들 사용이 끝났을 때 [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)를 호출해야 합니다. 핸들을 닫기 전까지는 동일한 디바이스에 어떤 다른 핸들도 열 수 없습니다.

## <a name="identify-a-specific-device"></a>특정 디바이스 식별

디바이스가 연결되거나 분리되기 전까지는 인덱스에 기준해서 열거된 디바이스 순서는 변경되지 않습니다. 물리적 디바이스를 식별하려면 디바이스의 일련 번호를 사용해야 합니다.

디바이스에서 일련 번호를 읽으려면 핸들을 연 후 [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) 함수를 사용합니다.

이 예에서는 일련 번호를 저장하기 위해 적절한 양의 메모리를 할당하는 방법을 보여 줍니다.

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>기본 디바이스 열기

대부분의 애플리케이션에서는 단일 Azure Kinect DK만 동일한 컴퓨터에 연결되어 있습니다. 예상되는 단일 디바이스에만 연결해야 하는 경우 `K4A_DEVICE_DEFAULT`의 `index`로 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)을 호출하여 첫 번째 디바이스를 열 수 있습니다.

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[이미지 검색](retrieve-images.md)

>[!div class="nextstepaction"]
>[IMU 샘플 검색](retrieve-imu-samples.md)

