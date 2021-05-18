---
title: Azure Kinect 뷰어
description: Azure Kinect 뷰어를 사용하여 모든 디바이스의 데이터 스트림을 시각화하는 방법을 이해합니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, 센서, 뷰어, 시각화, 깊이, rgb, 색상, imu, 오디오, 마이크, 포인트 클라우드
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92166108"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect 뷰어

설치된 도구 디렉터리 아래에서 `k4aviewer.exe`로 찾을 수 있는(예: `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, 여기에서 `X.Y.Z`는 설치된 SDK 버전) Azure Kinect 뷰어를 사용하면 모든 디바이스 데이터 스트림을 시각화하여 다음과 같은 작업이 가능합니다.

* 센서가 올바르게 작동하는지 확인합니다.
* 디바이스의 위치를 지정하는 데 도움을 줍니다.
* 카메라 설정을 시험해 봅니다.
* 디바이스 구성을 읽습니다.
* [Azure Kinect 레코더](azure-kinect-recorder.md)로 수행된 기록을 재생합니다.

Azure Kinect 뷰어에 대한 자세한 내용을 보려면 [Azure Kinect 비디오를 사용하는 방법](https://www.microsoft.com/videoplayer/embed/RE3hNwG)을 시청합니다.

Azure Kinect 뷰어는 [오픈 소스](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer)이며 API를 사용하는 방법에 대한 예로 사용될 수 있습니다.

## <a name="use-viewer"></a>사용자 뷰어

뷰어는 센서의 라이브 데이터 또는 기록된 데이터([ Azure Kinect Recorder ](azure-kinect-recorder.md))의 두 가지 모드로 작동할 수 있습니다.

### <a name="start-application"></a>애플리케이션 시작

`k4aviewer.exe`를 실행하여 애플리케이션을 시작합니다.

![시작된 뷰어 애플리케이션을 보여 주는 스크린샷입니다.](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>라이브 데이터로 뷰어 사용

1. **디바이스 열기** 섹션에서 열려는 디바이스의 **일련 번호** 를 선택합니다. 그런 다음 디바이스가 누락되어 있는 경우 **새로 고침** 을 선택합니다.
2. **디바이스 열기** 단추를 선택합니다.
3. **시작** 을 선택하여 기본 설정으로 데이터 스트리밍을 시작합니다.

### <a name="use-the-viewer-with-recorded-data"></a>기록된 데이터로 뷰어 사용

**기록 열기** 섹션에서 기록된 파일로 이동하여 선택합니다.

## <a name="check-device-firmware-version"></a>디바이스 펌웨어 버전 확인

다음 이미지에 표시된 것처럼 구성 창에서 디바이스 펌웨어 버전에 액세스합니다.

![뷰어를 사용하여 디바이스 펌웨어 버전 확인](./media/how-to-guides/check-firmware-update.png)

예를 들어 이 경우는 깊이 카메라의 ISP가 FW 1.5.63을 실행하고 있습니다.

## <a name="depth-camera"></a>깊이 카메라

깊이 카메라 뷰어는 다음과 같은 두 개의 창을 표시합니다.

* 하나는 IR 밝기를 표시하는 회색조 이미지인 *활성 밝기* 라고 합니다.
* 두 번째는 깊이 데이터를 색으로 구분하여 표현한 것으로 *깊이* 라고 합니다.

커서를 깊이 창의 픽셀로 가져 가면 아래와 같이 깊이 센서의 값을 볼 수 있습니다.

![깊이 보기](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB 카메라

아래 이미지는 색상 카메라 보기를 보여 줍니다.

![RGB 보기](./media/how-to-guides/viewer-rgb-camera.png)

스트리밍 중에 구성 창에서 RGB 카메라 설정을 제어할 수 있습니다.

![RGB 카메라 컨트롤](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>IMU(관성 측정 장비)

IMU 창에는 가속도계 및 자이로스코프, 두 구성 요소가 있습니다.

위쪽 절반은 가속도계이며 미터/초<sup>2</sup> 단위로 선형 가속을 표시합니다.  이는 중력으로부터의 가속을 포함하므로 테이블에 평평하게 놓여 있는 경우 Z 축은 약 -9.8 m/s<sup>2</sup>로 표시됩니다.

아래쪽 절반은 자이로스코프 부분이며 라디안/초 단위로 회전 움직임을 표시합니다.

![동작 센서 보기](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>마이크 입력

마이크 보기에는 각 마이크에서 듣는 소리의 표현이 표시됩니다. 소리가 없으면 그래프가 비어 있는 것으로 표시되고, 그렇지 않으면 연한 파란색 파형이 위쪽에 오버레이된 진한 파란색 파형이 표시됩니다.

진한 파형은 해당 시간 조각에 대해 마이크가 관찰한 최솟값 및 최댓값을 나타냅니다. 연한 파형은 해당 시간 조각에 대해 마이크가 관찰한 값의 평균 제곱근을 나타냅니다.

![마이크 입력 보기](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>포인트 클라우드 시각화

3D로 시각화된 깊이를 통해 지시된 키를 사용하여 이미지에서 이동할 수 있습니다.

![깊이 포인트 클라우드](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>동기화 컨트롤

뷰어를 사용하면 다중 디바이스 동기화를 구성할 때 디바이스를 독립 실행형(기본값), 마스터 또는 종속 모드로 구성할 수 있습니다.
구성을 변경하거나 동기화 케이블을 삽입/제거할 때 **새로 고침** 을 선택하여 업데이트합니다.

![외부 동기화 컨트롤](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[외부 동기화 설정 가이드](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
