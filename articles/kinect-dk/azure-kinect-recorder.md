---
title: Azure Kinect DK 레코더
description: Azure Kinect 레코더를 사용하여 센서 SDK의 데이터 스트림을 파일로 기록하는 방법을 이해합니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 기록, 재생, 판독기, matroska, mkv, 스트림, 깊이, rgb, 카메라, 색상, imu, 오디오
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276963"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect DK 레코더

이 문서에서는 `k4arecorder` 명령줄 유틸리티를 사용하여 센서 SDK의 데이터 스트림을 파일로 기록하는 방법을 다룹니다.

>[!NOTE]
>Azure Kinect 레코더는 오디오를 기록하지 않습니다.

## <a name="recorder-options"></a>레코더 옵션

`k4arecorder`에는 출력 파일 및 기록 모드를 지정하는 다양한 명령줄 인수가 있습니다.

기록은 [Matroska .mkv 형식](record-file-format.md)으로 저장됩니다. 기록은 색상 및 깊이에 대한 여러 비디오 트랙을 사용하고 카메라 보정 및 메타데이터와 같은 추가 정보도 사용합니다.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>파일 기록

예제 1. IMU를 사용하여 30fps로 깊이 NFOV 비범주화(640x576) 모드, RGB 1080p를 기록합니다.
**CTRL-C** 키를 눌러 기록하기를 중지합니다.

```
k4arecorder.exe output.mkv
```

예제 2. 10초 동안 IMU를 사용하지 않고 15fps로 WFOV 무범주화(1MP), RGB 3072p를 기록합니다.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

예제 3. 5초 동안 30fps로 WFOV 2x2 범주화를 기록하고 output.mkv로 저장합니다.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>[Azure Kinect 뷰어](azure-kinect-viewer.md)를 사용하여 기록하기 전에 RGB 카메라 컨트롤을 구성할 수 있습니다(예: 수동 화이트 밸런스 설정).

## <a name="verify-recording"></a>기록 확인

[Azure Kinect 뷰어](azure-kinect-viewer.md)로 .mkv 출력 파일을 열 수 있습니다.

트랙을 추출하거나 파일 정보를 보기 위해 `mkvinfo`와 같은 도구가 [MKVToolNix](https://mkvtoolnix.download/) 도구 키트의 일부로 제공됩니다.

## <a name="next-steps"></a>다음 단계

[외부 동기화 장치가 있는 레코더 사용](record-external-synchronized-units.md)