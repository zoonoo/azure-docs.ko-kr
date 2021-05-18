---
title: Azure Kinect 재생 API
description: 재생 API를 사용하여 기록 파일을 열기 위해 Azure Kinect 센서 SDK를 사용하는 방법을 알아봅니다.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 깊이, rgb, 기록, 재생, matroska, mkv
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276915"
---
# <a name="the-azure-kinect-playback-api"></a>Azure Kinect 재생 API

센서 SDK는 디바이스 데이터를 Matroska(.mkv) 파일로 기록하기 위한 API를 제공합니다. Matroska 컨테이너 형식은 비디오 트랙, IMU 샘플, 디바이스 보정을 저장합니다. 제공된 [k4arecorder](record-sensor-streams-file.md) 명령줄 유틸리티를 사용하여 기록을 생성할 수 있습니다. 또한 레코드 API를 사용하여 직접 기록을 사용자 지정하고 기록할 수 있습니다.

기록 API에 대한 자세한 내용은 [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831)를 참조하세요.

Matroska 파일 형식 사양에 대한 자세한 내용은 [기록 파일 형식](record-file-format.md) 페이지를 참조하세요.

## <a name="use-the-playback-api"></a>재생 API 사용

재생 API를 사용하여 기록 파일을 열 수 있습니다. 재생 API는 센서 SDK의 나머지 부분과 동일한 형식으로 센서 데이터에 대한 액세스를 제공합니다.

### <a name="open-a-record-file"></a>기록 파일 열기

다음 예에서는 [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368)을 사용하여 기록을 열고 기록 길이를 인쇄한 다음 [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff)를 사용하여 파일을 닫습니다.

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>캡처 읽기

파일이 열리고 나면 기록에서 캡처를 읽기 시작할 수 있습니다. 이 다음 예에서는 파일에 있는 각각의 캡처를 읽습니다.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>기록 내에서 검색

파일의 끝에 도달하고 나면 돌아가서 다시 읽으려고 할 수도 있습니다. 이 프로세스는 [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c)로 역방향으로 읽어서 수행할 수 있지만 기록의 길이에 따라 매우 느릴 수 있습니다.
대신 [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) 함수를 사용하여 파일의 특정 지점으로 이동할 수 있습니다.

이 예에서는 마이크로초 단위로 타임스탬프를 지정하여 파일의 다양한 지점을 검색합니다.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>태그 정보 읽기

또한 기록에는 디바이스 일련 번호 및 펌웨어 버전과 같은 다양한 메타데이터가 포함될 수 있습니다. 이 메타데이터는 기록 태그에 저장되며 [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) 함수를 사용하여 액세스할 수 있습니다.

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>기록 태그 목록

아래는 기록 파일에 포함될 수 있는 모든 기본 태그의 목록입니다. 이러한 값의 대부분은 [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 구조의 일부로 사용 가능하고 [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) 함수를 사용하여 읽을 수 있습니다.

태그가 존재하지 않는 경우 기본값을 갖는 것으로 간주됩니다.

| 태그 이름                     | 기본값      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 필드 | 참고     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | "OFF"              | `color_format` / `color_resolution`  | 가능한 값: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P" 등                                      |
| `K4A_DEPTH_MODE`             | "OFF"              | `depth_mode` / `depth_track_enabled` | 가능한 값: "OFF, "NFOV_UNBINNED", "PASSIVE_IR" 등                                                |
| `K4A_IR_MODE`                | "OFF"              | `depth_mode` / `ir_track_enabled`    | 가능한 값: "OFF", "ACTIVE", "PASSIVE"                                                                    |
| `K4A_IMU_MODE`               | "OFF"              | `imu_track_enabled`                  | 가능한 값: "ON", "OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.json" | 해당 없음                                  | [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) 참조 |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | 값이 나노초 단위로 저장된 경우 API는 마이크로초 단위로 제공합니다.                                                        |
| `K4A_WIRED_SYNC_MODE`        | "STANDALONE"       | `wired_sync_mode`                    | 가능한 값: "STANDALONE", "MASTER", "SUBORDINATE"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | 값이 나노초 단위로 저장된 경우 API는 마이크로초 단위로 제공합니다.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | 해당 없음                                  | 디바이스 색상 펌웨어 버전(예: "1.x.xx")                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | 해당 없음                                  | 디바이스 깊이 펌웨어 버전(예: "1.x.xx")                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | 해당 없음                                  | 기록 디바이스 일련 번호                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | 아래의 [타임스탬프 동기화](record-playback-api.md#timestamp-synchronization)를 참조하세요.                       |
| `K4A_COLOR_TRACK`            | 없음               | 해당 없음                                  | [기록 파일 형식 - 트랙 식별](record-file-format.md#identifying-tracks)을 참조하세요.                     |
| `K4A_DEPTH_TRACK`            | 없음               | 해당 없음                                  | [기록 파일 형식 - 트랙 식별](record-file-format.md#identifying-tracks)을 참조하세요.                     |
| `K4A_IR_TRACK`               | 없음               | 해당 없음                                  | [기록 파일 형식 - 트랙 식별](record-file-format.md#identifying-tracks)을 참조하세요.                     |
| `K4A_IMU_TRACK`              | 없음               | 해당 없음                                  | [기록 파일 형식 - 트랙 식별](record-file-format.md#identifying-tracks)을 참조하세요.                     |

## <a name="timestamp-synchronization"></a>타임스탬프 동기화

Matroska 형식을 사용하려면 기록의 타임스탬프가 0으로 시작해야 합니다. [외부에서 카메라를 동기화](record-external-synchronized-units.md)할 때는 각 디바이스의 첫 번째 타임스탬프가 0이 아닐 수 있습니다.

기록 및 재생 간에 디바이스로부터 원본 타임스탬프를 유지하기 위해 파일은 타임스탬프에 적용할 오프셋을 저장합니다.

`K4A_START_OFFSET_NS` 태그는 기록한 후 파일을 다시 동기화할 수 있도록 타임스탬프 오프셋을 지정하는 데 사용됩니다. 이 타임스탬프 오프셋을 파일의 각 타임스탬프에 추가하여 원래의 디바이스 타임 스탬프를 다시 생성할 수 있습니다.

시작 오프셋은 [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 구조에서도 사용 가능합니다.
