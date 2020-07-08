---
title: Azure Kinect 재생 API
description: Azure Kinect 센서 SDK를 사용 하 여 재생 API를 사용 하 여 기록 파일을 여는 방법에 대해 알아봅니다.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 깊이, rgb, 레코드, 재생, matroska, .mkv
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276915"
---
# <a name="the-azure-kinect-playback-api"></a>Azure Kinect 재생 API

센서 SDK는 Matroska (.mkv) 파일에 장치 데이터를 기록 하기 위한 API를 제공 합니다. Matroska 컨테이너 형식은 비디오 트랙, IMU 샘플 및 장치 보정을 저장 합니다. 제공 된 [k4arecorder](record-sensor-streams-file.md) 명령줄 유틸리티를 사용 하 여 기록을 생성할 수 있습니다. Record API를 사용 하 여 직접 기록을 사용자 지정 하 고 직접 기록할 수도 있습니다.

기록 API에 대 한 자세한 내용은을 참조 하십시오 [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

Matroska 파일 형식 사양에 대 한 자세한 내용은 [기록 파일 형식](record-file-format.md) 페이지를 참조 하세요.

## <a name="use-the-playback-api"></a>재생 API 사용

재생 API를 사용 하 여 기록 파일을 열 수 있습니다. 재생 API는 센서 SDK의 나머지 부분과 동일한 형식으로 센서 데이터에 대 한 액세스를 제공 합니다.

### <a name="open-a-record-file"></a>레코드 파일 열기

다음 예제에서는를 사용 하 여 녹음을 열고 [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) , 기록 길이를 인쇄 한 후를 사용 하 여 파일을 닫습니다 [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

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

파일이 열리면 기록에서 캡처 읽기를 시작할 수 있습니다. 다음 예에서는 파일의 각 캡처를 읽습니다.

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

파일의 끝에 도달 하면 돌아가서 다시 읽을 수 있습니다. 이 프로세스는를 사용 하 여 뒤로 읽으면 수행할 수 [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) 있지만 기록의 길이에 따라 매우 느릴 수 있습니다.
대신 함수를 사용 [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) 하 여 파일의 특정 지점으로 이동할 수 있습니다.

이 예제에서는 파일의 다양 한 지점을 검색 하는 타임 스탬프 (마이크로초)를 지정 합니다.

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

녹음/녹화에는 장치 일련 번호 및 펌웨어 버전과 같은 다양 한 메타 데이터가 포함 될 수도 있습니다. 이 메타 데이터는 함수를 사용 하 여 액세스할 수 있는 기록 태그에 저장 됩니다 [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) .

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

### <a name="record-tag-list"></a>레코드 태그 목록

다음은 기록 파일에 포함 될 수 있는 모든 기본 태그의 목록입니다. 이러한 값은 대부분 구조체의 일부로 사용할 수 [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 있으며 함수를 사용 하 여 읽을 수 있습니다 [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) .

태그가 없으면 기본값을 갖는 것으로 간주 됩니다.

| 태그 이름                     | 기본값      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html)필드가 | 참고     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | 해제              | `color_format` / `color_resolution`  | 가능한 값: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P" 등                                      |
| `K4A_DEPTH_MODE`             | 해제              | `depth_mode` / `depth_track_enabled` | 가능한 값: "OFF," NFOV_UNBINNED "," PASSIVE_IR "등                                                |
| `K4A_IR_MODE`                | 해제              | `depth_mode` / `ir_track_enabled`    | 가능한 값: "OFF", "ACTIVE", "PASSIVE"                                                                    |
| `K4A_IMU_MODE`               | 해제              | `imu_track_enabled`                  | 가능한 값: "ON", "OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.js" | 해당 없음                                  | 확인해[`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | 값이 나노초로 저장 된 경우 API는 마이크로초를 제공 합니다.                                                        |
| `K4A_WIRED_SYNC_MODE`        | 실행형       | `wired_sync_mode`                    | 가능한 값: "독립형", "MASTER", "하위"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | 값이 나노초로 저장 된 경우 API는 마이크로초를 제공 합니다.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | 해당 없음                                  | 장치 색 펌웨어 버전 (예: "1.x")                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | 해당 없음                                  | 장치 깊이 펌웨어 버전 (예: "1.x")                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | 해당 없음                                  | 녹음/녹화 장치 일련 번호                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | 아래의 [타임 스탬프 동기화](record-playback-api.md#timestamp-synchronization) 를 참조 하세요.                       |
| `K4A_COLOR_TRACK`            | 없음               | 해당 없음                                  | [기록 파일 형식-식별 트랙](record-file-format.md#identifying-tracks)을 참조 하세요.                     |
| `K4A_DEPTH_TRACK`            | 없음               | 해당 없음                                  | [기록 파일 형식-식별 트랙](record-file-format.md#identifying-tracks)을 참조 하세요.                     |
| `K4A_IR_TRACK`               | 없음               | 해당 없음                                  | [기록 파일 형식-식별 트랙](record-file-format.md#identifying-tracks)을 참조 하세요.                     |
| `K4A_IMU_TRACK`              | 없음               | 해당 없음                                  | [기록 파일 형식-식별 트랙](record-file-format.md#identifying-tracks)을 참조 하세요.                     |

## <a name="timestamp-synchronization"></a>타임 스탬프 동기화

Matroska 형식을 사용 하려면 기록을 0의 타임 스탬프로 시작 해야 합니다. [외부 카메라를 동기화](record-external-synchronized-units.md)할 때 각 장치의 첫 번째 타임 스탬프는 0이 아닐 수 있습니다.

기록 및 재생 사이에 장치에서 원본 타임 스탬프를 유지 하기 위해 파일은 타임 스탬프에 적용할 오프셋을 저장 합니다.

`K4A_START_OFFSET_NS`태그는 기록 후 파일을 다시 동기화 할 수 있도록 타임 스탬프 오프셋을 지정 하는 데 사용 됩니다. 이 타임 스탬프 오프셋을 파일의 각 타임 스탬프에 추가 하 여 원래 장치 타임 스탬프를 다시 생성할 수 있습니다.

시작 오프셋은 구조체 에서도 사용할 수 있습니다 [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) .
