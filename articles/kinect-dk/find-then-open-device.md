---
title: Azure Kinect 장치를 찾아 엽니다.
description: Azure Kinect Kinect 또는 SDK를 사용 하 여 Azure 장치를 찾고 여는 방법을 알아봅니다.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 깊이, rgb, 장치, 찾기, 열기
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276921"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Azure Kinect 장치를 찾아 엽니다.

이 문서에서는 Azure Kinect 진한를 찾고 여는 방법을 설명 합니다. 이 문서에서는 여러 장치가 컴퓨터에 연결 된 경우를 처리 하는 방법을 설명 합니다.

이 문서에서 함수를 사용 하는 방법을 보여 주는 [SDK 열거 예제](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) 를 참조할 수도 있습니다.

지원되는 함수는 다음과 같습니다.
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>연결 된 장치의 수를 검색 합니다.

먼저를 사용 하 여 현재 연결 된 Azure Kinect 장치 수를 가져옵니다 [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) .

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>장치 열기

장치에 대 한 정보를 가져오거나 해당 장치에서 데이터를 읽으려면 먼저를 사용 하 여 장치에 대 한 핸들을 열어야 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 합니다.

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

`index`의 매개 변수는 둘 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 이상의 연결 된 경우 열려는 장치를 나타냅니다. 단일 장치만 연결 될 것으로 예측 하는 경우의 인수 `K4A_DEVICE_DEFAULT` 또는 0을 전달 하 여 첫 번째 장치를 나타낼 수 있습니다.

언제 든 지 [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) 핸들을 사용 하 여 작업을 수행할 때 호출 해야 하는 장치를 열 수 있습니다. 핸들을 닫을 때까지 동일한 장치에 다른 핸들을 열 수 없습니다.

## <a name="identify-a-specific-device"></a>특정 장치 식별

장치를 연결 하거나 분리 하는 순서 대로 장치를 열거 하는 순서는 변경 되지 않습니다. 물리적 장치를 식별 하려면 장치의 일련 번호를 사용 해야 합니다.

장치에서 일련 번호를 읽으려면 [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) 핸들을 연 후 함수를 사용 합니다.

이 예에서는 일련 번호를 저장 하기 위해 적절 한 양의 메모리를 할당 하는 방법을 보여 줍니다.

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

## <a name="open-the-default-device"></a>기본 장치 열기

대부분의 응용 프로그램에는 동일한 컴퓨터에 단일 Azure Kinect만 연결 됩니다. 단일 예상 장치에만 연결 해야 하는 경우를 사용 하 여를 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 호출 `index` 하 여 `K4A_DEVICE_DEFAULT` 첫 번째 장치를 열 수 있습니다.

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

