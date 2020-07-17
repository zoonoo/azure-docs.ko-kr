---
title: 외부 동기화 장치에 Azure Kinect 레코더 사용
description: Azure Kinect 레코더를 사용 하 여 외부 동기화에 대해 구성 된 장치에서 데이터를 기록 하는 방법에 대해 알아봅니다.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, 센서, 뷰어, 외부 동기화, 단계 지연, 깊이, RGB, 카메라, 오디오 케이블, 레코더
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276916"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>외부 동기화 장치에 Azure Kinect 레코더 사용

이 문서에서는 [Azure Kinect 레코더가](azure-kinect-recorder.md) 데이터 외부 동기화 구성 장치를 기록 하는 방법에 대 한 지침을 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [외부 동기화를 위해 여러 Azure KINECT 진한 단위를 설정](https://support.microsoft.com/help/4494429)합니다.

## <a name="external-synchronization-constraints"></a>외부 동기화 제약 조건

- 마스터 장치는 연결 된 케이블에서 동기화 할 수 없습니다.
- 동기화를 사용 하려면 마스터 장치가 RGB 카메라를 스트리밍합니다.
- 모든 단위는 동일한 카메라 구성 (프레임 속도 및 해상도)을 사용 해야 합니다.
- 모든 단위는 동일한 장치 펌웨어 ([펌웨어 업데이트](update-device-firmware.md) 지침)를 실행 해야 합니다.
- 모든 하위 장치는 마스터 장치 전에 시작 해야 합니다.
- 모든 장치에서 동일한 노출 값을 설정 해야 합니다.
- 각 하위의 *지연 마스터* 설정은 마스터 장치를 기준으로 합니다.

## <a name="record-when-each-unit-has-a-host-pc"></a>각 장치에 호스트 PC가 있는 경우 기록

아래 예제에서 각 장치에는 자체의 전용 호스트 PC가 있습니다.
USB 대역폭과 CPU/GPU 사용량 문제를 방지 하기 위해 장치를 전용 Pc에 연결 하는 것이 좋습니다.

### <a name="subordinate-1"></a>하위 1

1. 첫 번째 단위에 대 한 레코더 설정

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. 장치가 대기를 시작 합니다.

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>하위 2

1. 두 번째 단위에 대 한 레코더 설정

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. 장치가 대기를 시작 합니다.

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>마스터

1. Master에서 기록을 시작 합니다.

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. 기록이 완료 될 때까지 대기

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>여러 단위가 단일 호스트 PC에 연결 된 경우 기록

단일 호스트 PC에 여러 Azure Kinect Eks를 연결할 수 있습니다. 그러나 USB 대역폭과 호스트 계산에 매우 많은 부담이 될 수 있습니다. 수요를 줄이려면 다음을 수행 합니다.

- 각 장치를 고유한 USB 호스트 컨트롤러에 연결 합니다.
- 각 장치에 대해 깊이 엔진을 처리할 수 있는 강력한 GPU가 있습니다.
- 필요한 센서만 기록 하 고 낮은 프레임 속도를 사용 합니다.

항상 종속 장치를 먼저 시작 하 고 마스터를 마지막으로 시작 합니다.

## <a name="subordinate-1"></a>하위 1

1. 하위에서 레코더 시작

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. 장치가 대기 상태로 전환 됩니다.

## <a name="master"></a>마스터

1. 마스터 장치 시작

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. 기록을 마칠 때까지 기다립니다.

## <a name="playing-recording"></a>녹음/녹화 재생

[Azure Kinect viewer](azure-kinect-viewer.md) 를 사용 하 여 기록을 재생할 수 있습니다.



## <a name="tips"></a>팁

- 동기화 된 카메라를 기록 하는 데 수동 노출을 사용 합니다. RGB 카메라 자동 노출이 시간 동기화에 영향을 줄 수 있습니다.
- 하위 장치를 다시 시작 하면 동기화가 손실 됩니다.
- 일부 [카메라 모드](hardware-specification.md#depth-camera-supported-operating-modes) 는 최대 15fps를 지원 합니다. 장치 간에 모드/프레임 속도를 혼합 하지 않는 것이 좋습니다.
- 단일 PC에 여러 단위를 연결 하면 USB 대역폭이 쉽게 포화 될 수 있으므로 장치당 별도의 호스트 PC를 사용 하는 것이 좋습니다. CPU/GPU 계산에도 유의 하세요.
- 안정성을 개선 하는 데 필요 하지 않은 경우 마이크와 IMU를 사용 하지 않도록 설정 합니다.

문제의 경우 [문제 해결](troubleshooting.md) 을 참조 하세요.

## <a name="see-also"></a>참조

- [외부 동기화 설정](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- 레코더 설정 및 추가 정보에 대 한 [Azure Kinect 레코더](azure-kinect-recorder.md) 입니다.
- 녹음/녹화를 재생 하거나 RGB 카메라 속성을 설정 하는 [Azure Kinect Viewer](azure-kinect-viewer.md) 는 레코더를 통해 사용할 수 없습니다.
- 장치 펌웨어를 업데이트 하기 위한 [Azure Kinect 펌웨어 도구](azure-kinect-firmware-tool.md) 입니다.
