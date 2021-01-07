---
title: Azure Kinect 진한 레코더
description: Azure Kinect 레코더를 사용 하 여 센서 SDK의 데이터 스트림을 파일에 기록 하는 방법을 이해 합니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, record, 재생, 판독기, matroska, .mkv, 스트림, 깊이, rgb, 카메라, 색, imu, 오디오
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276963"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect 진한 레코더

이 문서에서는 명령줄 유틸리티를 사용 하 여 `k4arecorder` 센서 SDK의 데이터 스트림을 파일에 기록 하는 방법을 설명 합니다.

>[!NOTE]
>Azure Kinect 레코더는 오디오를 기록 하지 않습니다.

## <a name="recorder-options"></a>레코더 옵션

에는 `k4arecorder` 출력 파일 및 기록 모드를 지정 하는 다양 한 명령줄 인수가 있습니다.

기록은 [Matroska. .mkv 형식](record-file-format.md)으로 저장 됩니다. 기록은 색 및 깊이에 대 한 여러 비디오 트랙과 카메라 보정 및 메타 데이터와 같은 추가 정보를 사용 합니다.

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

## <a name="record-files"></a>레코드 파일

예제 1. 레코드 깊이 NFOV un범주화 된 (640x576) 모드, RGB 1080p (IMU)
Ctrl + **C** 키를 눌러 기록을 중지 합니다.

```
k4arecorder.exe output.mkv
```

예제 2. WFOV 비 범주화 (1MP), RGB 3072p (IMU 없이 15 fps)를 10 초 동안 기록 합니다.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

예제 3. 30 fps에서 WFOV 2x2를 5 초 동안 기록 하 고 .mkv에 저장 합니다.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>[Azure Kinect Viewer](azure-kinect-viewer.md) 를 사용 하 여 기록 하기 전에 RGB 카메라 컨트롤을 구성할 수 있습니다 (예: 수동 흰색 잔액 설정).

## <a name="verify-recording"></a>기록 확인

[Azure Kinect Viewer](azure-kinect-viewer.md)를 사용 하 여 .mkv 파일을 열 수 있습니다.

트랙을 추출 하거나 파일 정보를 보려면와 같은 도구를 `mkvinfo` [MKVToolNix](https://mkvtoolnix.download/) 도구 키트의 일부로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[외부 동기화 된 단위와 함께 레코더 사용](record-external-synchronized-units.md)