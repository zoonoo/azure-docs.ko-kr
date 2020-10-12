---
title: Azure Kinect 알려진 문제 및 문제 해결
description: Azure Kinect 진한에서 센서 SDK를 사용 하는 경우 알려진 문제 및 문제 해결 팁에 대해 알아봅니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: 문제 해결, 업데이트, 버그, kinect, 피드백, 복구, 로깅, 팁
ms.openlocfilehash: 2db7b17ffc8b6595107dbc52fae719aa9f2d15db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277092"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Azure Kinect 알려진 문제 및 문제 해결

이 페이지에는 Azure Kinect 진한에서 센서 SDK를 사용 하는 경우 알려진 문제 및 문제 해결 팁이 포함 되어 있습니다. 제품 하드웨어 관련 문제는 [제품 지원 페이지](https://aka.ms/kinectsupport) 를 참조 하세요.

## <a name="known-issues"></a>알려진 문제

- ASMedia USB 호스트 컨트롤러와의 호환성 문제 (예: ASM1142 칩셋)
  - Microsoft USB 드라이버를 사용 하는 일부 경우에서 차단을 해제할 수 있습니다.
  - 많은 Pc에는 대체 호스트 컨트롤러가 있으며 USB3 포트를 변경 하는 것이 도움이 될 수 있습니다.

센서 SDK 관련 문제에 대 한 자세한 내용을 보려면 [GitHub 문제](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues) 를 확인 하세요.

## <a name="collecting-logs"></a>로그 수집

K4A.dll에 대 한 로깅은 환경 변수를 통해 사용 하도록 설정 됩니다. 기본적으로 로깅은 stdout에 전송 되 고 오류 및 중요 메시지만 생성 됩니다. 이러한 설정을 변경 하 여 로깅이 파일로 이동할 수 있습니다. 필요에 따라 자세한 정도를 조정할 수도 있습니다. 다음은 k4a 라는 파일에 대 한 로깅을 사용 하도록 설정 하 고 경고 및 상위 수준 메시지를 캡처하는 Windows의 예입니다.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. 명령 프롬프트에서 시나리오 실행 (예: 뷰어 시작)
4. K4a로 이동 하 여 파일을 공유 합니다.

자세한 내용은 헤더 파일의 클립 아래를 참조 하십시오.

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

본문 추적 SDK K4ABT.dll에 대 한 로깅은 다음과 유사 합니다. 단, 사용자는 다른 환경 변수 이름 집합을 수정 해야 합니다.

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>장치가 장치 관리자에서 열거 하지 않음

- 장치 뒤의 상태 LED를 확인 합니다 .이 LED가 주황색으로 깜박이는 경우 USB 연결 문제가 발생 하 고 충분 한 전력이 제공 되지 않습니다. 전원 공급 장치 케이블이 제공 된 전원 어댑터에 연결 되어 있어야 합니다. 전원 케이블의 USB 종류는 연결 되어 있지만 장치에는 PC USB 포트가 제공할 수 있는 것 보다 더 많은 전력이 필요 합니다. 따라서 PC 포트나 USB 허브에 연결 하지 마세요.
- 전원 케이블을 연결 하 고 데이터에 대 한 USB3 포트를 사용 하는지 확인 합니다.
- 데이터 연결에 대 한 USB3 포트를 변경해 보세요. (예를 들어 PC 뒷면에서 마더보드와 가까운 USB 포트를 사용 하는 것이 좋습니다.)
- 케이블이 손상 되었거나 품질이 낮은 케이블을 선택 하면 불안정 한 열거가 발생할 수 있습니다 (장치 관리자에서 장치가 "깜박임" 상태로 유지 됨).
- 노트북에 연결 하 고 배터리를 실행 하는 경우 포트의 전원을 제한 하는 것일 수 있습니다.
- 호스트 PC를 다시 부팅 합니다.
- 문제가 지속 되 면 호환성 문제가 있을 수 있습니다.
- 펌웨어 업데이트 중에 오류가 발생 하 고 장치가 자체적으로 복구 되지 않은 경우 [공장 기본 설정을](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)수행 합니다.

## <a name="azure-kinect-viewer-fails-to-open"></a>Azure Kinect Viewer를 열지 못함

- Windows Device Manager에서 장치가 열거 되는지 먼저 확인 합니다.

    ![Windows 장치 관리자의 Azure Kinect 카메라](./media/resources/viewer-fails.png)

- 장치를 사용 하는 다른 응용 프로그램이 있는지 확인 합니다 (예: Windows 카메라 응용 프로그램). 한 번에 한 응용 프로그램만 장치에 액세스할 수 있습니다.
- K4aviewer 로그에서 오류 메시지를 확인 합니다.
- Windows 카메라 응용 프로그램을 열고 작동 하는지 확인 합니다.
- 전원 주기 장치, 장치를 사용 하기 전에 스트리밍 LED의 전원을 끌 때까지 기다립니다.
- 호스트 PC를 다시 부팅 합니다.
- PC에서 최신 그래픽 드라이버를 사용 하 고 있는지 확인 합니다.
- 고유한 SDK 빌드를 사용 하는 경우 문제를 해결 하려면 공식적으로 릴리스된 버전을 사용해 보세요.
- 문제가 지속 되 면 [로그](troubleshooting.md#collecting-logs) 및 파일 피드백을 수집 합니다.

## <a name="cannot-find-microphone"></a>마이크를 찾을 수 없음

- 먼저 Device Manager에서 마이크 배열이 열거 되었는지 확인 합니다.
- 장치가 열거 되 고 Windows에서 제대로 작동 하지 않는 경우 펌웨어 업데이트 창에서 깊이 카메라에 다른 컨테이너 ID를 할당 한 후 문제가 발생할 수 있습니다.
- Device Manager로 이동 하 고 "Azure Kinect 마이크 배열"을 마우스 오른쪽 단추로 클릭 한 다음 "장치 제거"를 선택 하 여 다시 설정 해 볼 수 있습니다. 완료 되 면 센서를 분리 하 고 다시 연결 합니다.

    ![Azure Kinect Mic 배열](./media/resources/mic-not-found.png)

- 그런 다음 Azure Kinect Viewer를 다시 시작한 후 다시 시도 합니다.

## <a name="device-firmware-update-issues"></a>장치 펌웨어 업데이트 문제

- 업데이트 후 올바른 버전 번호를 보고 하지 않은 경우 장치를 껐다 껐다 해야 할 수 있습니다.
- 펌웨어 업데이트가 중단 되 면 잘못 된 상태가 되어 열거 되지 못할 수 있습니다. 장치를 분리 하 고 다시 연결 하 고 복구 가능 여부를 확인 하는 데 60 초 정도 기다립니다.
그렇지 않은 경우 [공장 재설정](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) 을 수행 합니다.

## <a name="image-quality-issues"></a>이미지 품질 문제

- [Azure Kinect viewer](azure-kinect-viewer.md) 를 시작 하 고, 간섭을 위한 장치의 위치를 확인 하거나, 센서가 차단 되었거나 렌즈가 변경 되었는지 확인 합니다.
- 특정 모드에서 문제가 발생 하는 경우 다른 운영 모드를 시도 하 여 범위를 좁힙니다.
- 팀과 이미지 품질 문제를 공유 하려면 다음 작업을 수행할 수 있습니다.

1) [Azure Kinect viewer](azure-kinect-viewer.md) 에서 보기를 일시 중지 하 고 스크린샷 사용 또는
2) [Azure Kinect 레코더](azure-kinect-recorder.md)를 사용 하 여 기록 합니다 (예:).`k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>일관 되지 않거나 예기치 않은 장치 타임 스탬프

를 호출 ```k4a_device_set_color_control``` 하면 장치에 대 한 타이밍 변경을 일시적으로 유도 하 여 안정화 하는 데 몇 가지 캡처를 사용할 수 있습니다. 각 새 이미지를 사용 하 여 내부 타이밍 계산을 다시 설정 하지 않으려면 이미지 캡처 루프에서 API를 호출 하지 마세요. 대신 카메라를 시작 하기 전에 또는 이미지 캡처 루프 내에서 값을 변경 해야 하는 경우에만 API를 호출 합니다. 특히를 호출 하지 마십시오 ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` .

## <a name="usb3-host-controller-compatibility"></a>USB3 호스트 컨트롤러 호환성

장치 관리자에서 장치를 열거 하지 않는 경우 지원 되지 않는 USB3 controller에 연결 되었기 때문일 수 있습니다. 

Azure Kinect 진한 Windows의 경우 **Intel**, **텍사스 악기 (TI)** 및 **Renesas** 는 지원 되는 *유일한 호스트 컨트롤러*입니다. Windows 플랫폼의 Azure Kinect SDK는 통합 컨테이너 ID를 사용 하며, SDK가 실제로 동일한 장치에 있는 깊이, 색 및 오디오 장치를 찾을 수 있도록 USB 2.0 및 3.0 장치를 확장 해야 합니다. Linux에서 더 많은 호스트 컨트롤러가 지원 될 수 있습니다. 해당 플랫폼은 장치 일련 번호에 대 한 컨테이너 ID와 더 적은 수를 사용 합니다. 

USB 호스트 컨트롤러의 항목은 PC에 호스트 컨트롤러가 두 개 이상 설치 된 경우 훨씬 더 복잡해 집니다. 호스트 컨트롤러가 혼합 되어 있는 경우 사용자에 게 일부 포트가 정상적으로 작동 하 고 다른 포트가 전혀 작동 하지 않는 문제가 발생할 수 있습니다. 포트를 케이스에 연결 하는 방법에 따라 Azure Kinect에 문제가 있는 모든 front 포트가 표시 될 수 있습니다.

**Windows:** 열려 있는 호스트 컨트롤러를 확인 하려면 Device Manager

1. 유형별 보기-> 장치 
2. Azure Kinect 연결 됨 선택 카메라->Azure Kinect 4K 카메라
3. 연결을 통해 > 장치 보기

![USB 포트 문제 해결](./media/resources/usb-troubleshooting.png)

PC에서 연결 된 USB 포트를 더 잘 이해 하려면 Azure Kinect 진한을 PC의 다른 USB 포트에 연결할 때 각 USB 포트에 대해 이러한 단계를 반복 합니다.

## <a name="depth-camera-auto-powers-down"></a>깊이 카메라 자동 전원을 끕니다.

깊이 카메라에서 이미지 깊이 데이터를 계산 하는 데 사용 하는 레이저의 수명은 제한적입니다. 레이저 좋아하세요의 수명을 최대화 하기 위해 깊이 데이터가 사용 되지 않는 경우 깊이 카메라가 검색 됩니다. 장치가 몇 분 동안 스트리밍 되지만 호스트 PC에서 데이터를 읽지 않는 경우 깊이 카메라 전원이 켜 짐 또한 하위 장치가 깊이 카메라를 스트리밍하는 상태에서 시작 하 고 깊이 프레임은 마스터 장치가 캡처 동기화를 시작할 때까지 대기 하는 데 도움이 되는 다중 장치 동기화에 영향을 줍니다. 다중 장치 캡처 시나리오에서이 문제를 방지 하려면 마스터 장치가 시작 되는 첫 번째 하위 작업의 1 분 이내에 시작 되도록 합니다. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Unreal에서 본문 추적 SDK 사용

Unreal에서 본문 추적 SDK를 사용 하려면 `<SDK Installation Path>\tools` 환경 변수에를 추가 하 `PATH` 고 및를에 복사 했는지 확인 `dnn_model_2_0.onnx` `cudnn64_7.dll` `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` 합니다.

## <a name="next-steps"></a>다음 단계

[추가 지원 정보](support.md)
