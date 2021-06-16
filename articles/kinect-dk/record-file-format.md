---
title: 기록 파일 형식에 대해 Azure Kinect 센서 SDK 사용
description: Azure Kinect 센서 SDK에서 기록된 파일 형식을 사용하는 방법을 이해합니다.
author: martinekuan
ms.author: martinek
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: kinect, azure, 센서, sdk, 깊이, rgb, 기록, 재생, matroska, mkv
ms.openlocfilehash: 0f45893d37d56e90fbb266c3b988b603f58a43d2
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753452"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>기록 파일 형식에 대해 Azure Kinect 센서 SDK 사용

센서 데이터를 기록하기 위해 Matroska(.mkv) 컨테이너 형식이 사용되며 이는 광범위한 코덱을 사용하여 여러 개의 트랙을 저장할 수 있습니다. 기록 파일은 색상, 깊이, IR 이미지, IMU를 저장하기 위한 트랙을 포함합니다.

.mkv 컨테이너 형식에 대한 낮은 수준의 세부 정보는 [Matroska 웹 사이트](https://www.matroska.org/index.html)에서 찾을 수 있습니다.

| 트랙 이름 | 코덱 형식                          |
|------------|---------------------------------------|
| COLOR      | 모드 종속(MJPEG, NV12 또는 YUY2) |
| DEPTH      | b16g(16비트 회색조, Big-endian)   |
| IR         | b16g(16비트 회색조, Big-endian)   |
| IMU        | 사용자 지정 구조는 아래의 [IMU 샘플 구조](record-file-format.md#imu-sample-structure)를 참조하세요. |

## <a name="using-third-party-tools"></a>타사 도구 사용

[MKVToolNix](https://mkvtoolnix.download/) 도구 키트의 `ffmpeg` 또는 `mkvinfo` 명령과 같은 도구를 사용하여 기록 파일에서 정보를 보고 추출할 수 있습니다.

예를 들어 다음 명령은 깊이 트랙을 16비트 PNG의 시퀀스로 동일한 폴더에 추출합니다.

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1` 매개 변수는 트랙 인덱스 1을 추출하는데 이는 대부분의 기록에서 깊이에 해당합니다. 기록에 색상 트랙이 포함되지 않은 경우 `-map 0:0`이 사용됩니다.

`-vsync 0` 매개 변수는 ffmpeg에서 프레임 속도를 30fps, 15fps 또는 5fps에 일치시키려고 하는 대신 있는 그대로의 프레임을 추출하게 합니다.

## <a name="imu-sample-structure"></a>IMU 샘플 구조

재생 API를 사용하지 않고 파일에서 IMU 데이터를 추출하는 경우 데이터는 이진 형식이 됩니다.
IMU 데이터의 구조는 아래와 같습니다. 모든 필드는 Little-endian 방식입니다.

| 필드                        | Type     |
|------------------------------|----------|
| 가속도계 타임스탬프(μs) | uint64   |
| 가속도계 데이터(x, y, z) | float[3] |
| 자이로스코프 타임스탬프(μs)     | uint64   |
| 자이로스코프 데이터(x, y, z)     | float[3] |

## <a name="identifying-tracks"></a>트랙 식별

색상, 깊이, IR 등을 포함하는 트랙을 식별해야 할 수도 있습니다. 타사 도구로 작업하여 Matroska 파일을 읽을 때는 트랙을 식별해야 합니다.
트랙 번호는 카메라 모드 및 사용하도록 설정된 트랙의 집합에 따라 달라집니다. 태그는 각 트랙의 의미를 식별하는 데 사용됩니다.

아래 태그 목록은 각각 특정 Matroska 요소에 연결되며 해당 추적 또는 첨부 파일을 찾아내는 데 사용될 수 있습니다.

이러한 태그는 `ffmpeg` 및 `mkvinfo`와 같은 도구를 사용하여 볼 수 있습니다.
태그의 전체 목록은 [기록 및 재생](record-playback-api.md) 페이지에 나열되어 있습니다.

| 태그 이름             | 태그 대상             | 태그 값             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | 색상 트랙            | Matroska 트랙 UID    |
| K4A_DEPTH_TRACK      | 깊이 트랙            | Matroska 트랙 UID    |
| K4A_IR_TRACK         | IR 트랙               | Matroska 트랙 UID    |
| K4A_IMU_TRACK        | IMU 트랙              | Matroska 트랙 UID    |
| K4A_CALIBRATION_FILE | 보정 첨부 파일 | 첨부 파일 이름   |

## <a name="next-steps"></a>다음 단계

[기록 및 재생](record-playback-api.md)
