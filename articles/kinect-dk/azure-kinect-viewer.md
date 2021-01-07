---
title: Azure Kinect 뷰어
description: Azure Kinect viewer를 사용 하 여 모든 장치 데이터 스트림을 시각화 하는 방법을 이해 합니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, 센서, 뷰어, 시각화, 깊이, rgb, 색, imu, 오디오, 마이크, 지점 클라우드
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166108"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect 뷰어

설치 된 도구 디렉터리 아래에 있는 Azure Kinect Viewer `k4aviewer.exe` (예 `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` `X.Y.Z` : 설치 된 SDK 버전)에는 모든 장치 데이터 스트림을 시각화 하는 데 사용할 수 있습니다.

* 센서가 제대로 작동 하는지 확인 합니다.
* 장치 위치를 지정 하는 데 도움을 줍니다.
* 카메라 설정을 시험해 보세요.
* 장치 구성을 읽습니다.
* [Azure Kinect 레코더](azure-kinect-recorder.md)를 사용 하 여 작성 된 재생 기록.

Azure Kinect viewer에 대 한 자세한 내용은 [Azure Kinect 비디오를 사용 하는 방법](https://www.microsoft.com/videoplayer/embed/RE3hNwG)을 시청 하세요.

Azure Kinect Viewer는 [오픈 소스](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) 이며 api를 사용 하는 방법에 대 한 예제로 사용할 수 있습니다.

## <a name="use-viewer"></a>뷰어 사용

뷰어는 센서의 라이브 데이터 또는 기록 된 데이터 ([Azure Kinect 레코더](azure-kinect-recorder.md))를 사용 하 여 두 가지 모드로 작동할 수 있습니다.

### <a name="start-application"></a>응용 프로그램 시작

을 실행 하 여 응용 프로그램을 시작 `k4aviewer.exe` 합니다.

![시작 된 뷰어 응용 프로그램을 보여 주는 스크린샷](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>라이브 데이터에 뷰어 사용

1. **장치 열기** 섹션에서 열려는 장치의 **일련 번호** 를 선택 합니다. 그런 다음 장치가 없는 경우 **새로 고침**을 선택 합니다.
2. **장치 열기** 단추를 선택 합니다.
3. **시작** 을 선택 하 여 기본 설정으로 데이터 스트리밍을 시작 합니다.

### <a name="use-the-viewer-with-recorded-data"></a>기록 된 데이터와 함께 뷰어 사용

**기록 열기** 섹션에서 기록 된 파일로 이동 하 여 선택 합니다.

## <a name="check-device-firmware-version"></a>장치 펌웨어 버전 확인

다음 그림에 표시 된 것 처럼 구성 창에서 장치 펌웨어 버전에 액세스 합니다.

![뷰어를 사용 하 여 장치 펌웨어 버전 확인](./media/how-to-guides/check-firmware-update.png)

예를 들어이 경우에는 카메라 ISP가 FW 1.5.63을 실행 하는 것입니다.

## <a name="depth-camera"></a>깊이 카메라

깊이 카메라 뷰어는 다음과 같은 두 개의 창을 표시 합니다.

* 하나는 IR 밝기를 표시 하는 회색조 이미지인 *활성 밝기* 라고 합니다.
* 두 번째는 깊이 데이터를 색으로 표현한 색이 있는 *깊이*를 지칭 합니다.

아래와 같이 깊이 창의 픽셀에서 커서를 가리켜 깊이 센서의 값을 확인 합니다.

![깊이 보기](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB 카메라

아래 이미지는 컬러 카메라 보기를 보여 줍니다.

![RGB 보기](./media/how-to-guides/viewer-rgb-camera.png)

스트리밍 중에 구성 창에서 RGB 카메라 설정을 제어할 수 있습니다.

![RGB 카메라 컨트롤](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>IMU (관성 측정 단위)

IMU 창에는가 속도계 및 자이로스코프가의 두 구성 요소가 있습니다.

위쪽 절반은가 속도계 이며 미터/초<sup>2</sup>의 선형 가속을 보여 줍니다.  이는 중력의 가속을 포함 하므로 테이블에 플랫으로 배치 된 경우 Z 축이 9.8 m/s<sup>2</sup>를 표시 합니다.

아래쪽 절반은 자이로스코프가 부분이 며 라디안/초 단위로 회전 움직임을 보여 줍니다.

![동작 센서 보기](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>마이크 입력

마이크 보기에는 각 마이크의 소리 소리가 표시 됩니다. 사운드가 없으면 그래프가 비어 있는 것으로 표시 되 고, 그렇지 않으면 밝은 파란색 파형이 위쪽에 오버레이 된 진한 파란색 파형이 표시 됩니다.

어두운 물결은 해당 시간 조각에 대해 마이크가 관찰 한 최소 및 최대 값을 나타냅니다. 연한 물결은 해당 시간 조각에서 마이크에 의해 관찰 된 값의 근본 평균 제곱을 나타냅니다.

![마이크 입력 보기](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Point cloud 시각화

3D로 시각화 된 깊이를 통해 지시 된 키를 사용 하 여 이미지를 이동할 수 있습니다.

![깊이 지점 클라우드](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>동기화 컨트롤

뷰어를 사용 하 여 다중 장치 동기화를 구성할 때 장치를 독립 실행형 (기본값), 마스터 또는 하위 모드로 구성할 수 있습니다.
구성 또는 동기화 케이블 삽입/제거를 변경 하는 경우 **새로 고침** 을 선택 하 여 업데이트 합니다.

![외부 동기화 컨트롤](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[외부 동기화 설정 가이드](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
