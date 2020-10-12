---
title: Azure Kinect 센서 SDK를 사용 하 여 파일 형식 기록
description: Azure Kinect 센서 SDK 기록 파일 형식을 사용 하는 방법을 이해 합니다.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: kinect, azure, 센서, sdk, 깊이, rgb, 레코드, 재생, matroska, .mkv
ms.openlocfilehash: 3a78ed158c78df1c9a6eadcc0bfb2e40b22750bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501783"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Azure Kinect 센서 SDK를 사용 하 여 파일 형식 기록

센서 데이터를 기록 하기 위해 Matroska (.mkv) 컨테이너 형식이 사용 됩니다 .이를 통해 다양 한 코덱을 사용 하 여 여러 트랙을 저장할 수 있습니다. 기록 파일은 색, 깊이, IR 이미지 및 IMU를 저장 하기 위한 트랙을 포함 합니다.

.Mkv 컨테이너 형식에 대 한 하위 수준 세부 정보는 [Matroska 웹 사이트](https://www.matroska.org/index.html)에서 찾을 수 있습니다.

| 트랙 이름 | 코덱 형식                          |
|------------|---------------------------------------|
| 색      | Mode-Dependent (MJPEG, NV12 또는 YUY2) |
| DEPTH      | b16g (16 비트 회색조, 빅 endian)   |
| IR         | b16g (16 비트 회색조, 빅 endian)   |
| IMU        | 사용자 지정 구조 아래의 [Imu 샘플 구조](record-file-format.md#imu-sample-structure) 를 참조 하세요. |

## <a name="using-third-party-tools"></a>타사 도구 사용

`ffmpeg`또는 `mkvinfo` [MKVToolNix](https://mkvtoolnix.download/) toolkit의 명령과 같은 도구를 사용 하 여 기록 파일에서 정보를 보고 추출할 수 있습니다.

예를 들어 다음 명령은 동일한 폴더에 대 한 16 비트 PNGs 시퀀스로 깊이 트랙을 추출 합니다.

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1`이 매개 변수는 대부분의 기록에서 깊이가 되는 트랙 인덱스 1을 추출 합니다. 기록에 색 트랙이 포함 되지 않은 경우이 `-map 0:0` 사용 됩니다.

`-vsync 0`매개 변수는 ffmpeg에서 프레임 속도가 30fps, 15fps 또는 5fps 인 것과 일치 하는 것이 아니라 그대로 프레임을 추출 하도록 합니다.

## <a name="imu-sample-structure"></a>IMU 샘플 구조체

재생 API를 사용 하지 않고 파일에서 IMU 데이터를 추출 하는 경우 데이터는 이진 형식으로 됩니다.
IMU 데이터의 구조는 아래와 같습니다. 모든 필드는 작은 endian입니다.

| 필드                        | 형식     |
|------------------------------|----------|
| 가 속도계 Timestamp (μs) | uint64   |
| 가 속도계 Data (x, y, z) | float [3] |
| 자이로스코프가 Timestamp (μs)     | uint64   |
| 자이로스코프가 Data (x, y, z)     | float [3] |

## <a name="identifying-tracks"></a>트랙 식별

색, 깊이, IR 등을 포함 하는 트랙을 식별 해야 할 수도 있습니다. 타사 도구로 작업 하 여 Matroska 파일을 읽을 때는 트랙을 식별 해야 합니다.
트랙 번호는 카메라 모드와 설정 된 트랙 집합에 따라 달라 집니다. 태그는 각 트랙의 의미를 식별 하는 데 사용 됩니다.

아래 태그 목록은 각각 특정 Matroska 요소에 연결 되며 해당 추적 또는 첨부 파일을 조회 하는 데 사용할 수 있습니다.

이러한 태그는 및와 같은 도구를 사용 하 여 볼 수 있습니다 `ffmpeg` `mkvinfo` .
태그의 전체 목록은 [레코드 및 재생](record-playback-api.md) 페이지에 나열 됩니다.

| 태그 이름             | 태그 대상             | 태그 값             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | 색 트랙            | Matroska Track UID    |
| K4A_DEPTH_TRACK      | 깊이 트랙            | Matroska Track UID    |
| K4A_IR_TRACK         | IR 트랙               | Matroska Track UID    |
| K4A_IMU_TRACK        | IMU 트랙              | Matroska Track UID    |
| K4A_CALIBRATION_FILE | 보정 첨부 파일 | 첨부 파일 이름   |

## <a name="next-steps"></a>다음 단계

[기록 및 재생](record-playback-api.md)
