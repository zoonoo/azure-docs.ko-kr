---
title: Azure Kinect 알려진 문제 및 문제 해결
description: 센서 SDK를 Azure Kinect DK와 사용할 때의 일부 알려진 문제 및 문제 해결 팁에 대해 알아봅니다.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: conceptual
keywords: 문제 해결, 업데이트, 버그, kinect, 피드백, 복구, 로깅, 팁
ms.openlocfilehash: da5242a09934a756093a9e02b6d474e6c75fecda
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108743"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Azure Kinect 알려진 문제 및 문제 해결

이 페이지에는 센서 SDK를 Azure Kinect DK와 사용할 때의 알려진 문제 및 문제 해결 팁이 포함되어 있습니다. 제품 하드웨어 관련 문제는 [제품 지원 페이지](./index.yml)도 참조하세요.

## <a name="known-issues"></a>알려진 문제

- ASMedia USB 호스트 컨트롤러와의 호환성 문제(예: ASM1142 칩셋)
  - Microsoft USB 드라이버를 사용하는 일부 경우가 차단 해제할 수 있음
  - 많은 PC에 대체 호스트 컨트롤러도 있으며 USB3 포트를 변경하는 것이 도움이 될 수 있음

센서 SDK 관련 문제에 대한 자세한 내용은 [GitHub 문제](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)를 확인합니다.

## <a name="collecting-logs"></a>로그 수집

K4A.dll에 대한 로깅은 환경 변수를 통해 사용하도록 설정됩니다. 기본적으로 로깅은 stdout으로 전송되고 오류 및 중요한 메시지만 생성됩니다. 로깅이 파일로 이동할 수 있도록 이러한 설정을 변경할 수 있습니다. 또한 필요에 따라 세부 정보 표시를 조정할 수 있습니다. 아래는 k4a.log라는 파일로 로깅하도록 설정하는 Windows용의 예로서 경고 및 상위 수준의 메시지를 캡처합니다.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. 명령 프롬프트에서 시나리오 실행(예: 뷰어 시작)
4. k4a.log로 이동하여 파일을 공유합니다.

자세한 내용은 헤더 파일의 아래 클립을 참조하세요.

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

신체 추적 SDK K4ABT.dll에 대한 로깅은 사용자가 환경 변수 이름의 다른 집합을 수정해야 한다는 점을 제외하면 유사합니다.

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

## <a name="device-doesnt-enumerate-in-device-manager"></a>디바이스 관리자에 열거되지 않은 디바이스

- 디바이스 뒤의 상태 LED를 확인하여 황색으로 깜박이고 있으면 USB 연결 문제로, 충분한 전력을 제공받지 못하고 있는 것입니다. 전원 공급 케이블이 제공된 전원 어댑터에 연결되어 있어야 합니다. 전원 케이블에 USB A 타입이 연결되어 있지만 디바이스에는 PC의 USB 포트가 제공할 수 있는 것보다 더 많은 전력이 필요합니다. 그러므로 PC 포트나 USB 허브에 연결하지 않습니다.
- 전원 케이블이 연결되어 있고 데이터용 USB3 포트를 사용하고 있는지 확인합니다.
- 데이터 연결용 USB3 포트를 변경해 봅니다(예를 들어 PC 뒷면에서 마더보드에 가까운 USB 포트를 사용하는 것을 권장).
- 케이블을 확인해 봅니다. 손상되거나 품질이 낮은 케이블은 불안정한 열거(디바이스 관리자에서 디바이스가 "깜박임" 상태 유지)를 유발할 수 있습니다.
- 노트북에 연결하여 배터리로 실행하고 있는 경우 포트에 대한 전원 대역폭을 제한하고 있는 것일 수 있습니다.
- 호스트 PC를 다시 부팅합니다.
- 문제가 지속되면 호환성 문제가 있을 수 있습니다.
- 펌웨어 업데이트 중에 오류가 발생하고 디바이스가 자체적으로 복구되지 않은 경우 [공장 초기화](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)를 수행합니다.

## <a name="azure-kinect-viewer-fails-to-open"></a>Azure Kinect 뷰어 열기 실패

- 먼저 Windows 디바이스 관리자에 디바이스가 열거되는지 확인합니다.

    ![Windows 디바이스 관리자의 Azure Kinect 카메라](./media/resources/viewer-fails.png)

- 디바이스를 사용하는 다른 애플리케이션이 있는지 확인합니다(예: Windows 카메라 애플리케이션). 한 번에 한 애플리케이션만 디바이스에 액세스할 수 있습니다.
- k4aviewer.err 로그에서 오류 메시지를 확인합니다.
- Windows 카메라 애플리케이션을 열고 작동하는지 확인합니다.
- 디바이스의 전원을 껐다가 다시 켭니다. 디바이스를 사용하기 전에 스트리밍 LED 전원이 꺼질 때까지 기다립니다.
- 호스트 PC를 다시 부팅합니다.
- PC에서 최신 그래픽 드라이버를 사용하고 있는지 확인합니다.
- 자체적인 SDK 빌드를 사용하고 있는 경우 문제를 해결하려면 공식적으로 릴리스된 버전을 사용해 봅니다.
- 문제가 지속되면 [로그를 수집](troubleshooting.md#collecting-logs)하고 피드백을 제출합니다.

## <a name="cannot-find-microphone"></a>마이크를 찾을 수 없음

- 먼저 디바이스 관리자에 마이크 배열이 열거되어 있는 확인합니다.
- 디바이스가 열거되어 있고 Windows에서 다른 의미로 제대로 작동하는 경우 문제는 펌웨어 업데이트 후 Windows가 Depth Camera에 다른 컨테이너 ID를 할당한 것일 수 있습니다.
- 디바이스 관리자로 이동하여 "Azure Kinect 마이크 배열"을 마우스 오른쪽 단추로 클릭하고 "디바이스 제거"를 선택하여 다시 설정해 볼 수 있습니다. 완료되고 나면 센서를 분리하고 다시 연결합니다.

    ![Azure Kinect 마이크 배열](./media/resources/mic-not-found.png)

- 그런 다음 Azure Kinect 뷰어를 다시 시작하여 다시 시도합니다.

## <a name="device-firmware-update-issues"></a>디바이스 펌웨어 업데이트 문제

- 업데이트 후 올바른 버전 번호가 보고되지 않는 경우 디바이스의 전원을 껐다가 다시 켜야 할 수 있습니다.
- 펌웨어 업데이트가 중단되면 잘못된 상태가 되어 열거되지 못할 수 있습니다. 디바이스를 분리하고 다시 연결하여 60초를 기다려서 복구되는지 확인합니다.
복구되지 않으면 [공장 초기화](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)를 수행합니다.

## <a name="image-quality-issues"></a>이미지 품질 문제

- [Azure Kinect 뷰어](azure-kinect-viewer.md)를 시작하여 간섭에 대해서 디바이스의 위치 지정을 확인하거나 센서가 가려져 있는지 또는 렌즈가 더러운지 확인합니다.
- 특정 모드에서 문제가 발생하는 경우 다른 작동 모드를 시도하여 범위를 좁힙니다.
- 이미지 품질 문제를 팀과 공유하기 위해 다음과 같은 작업을 할 수 있습니다.

1) [Azure Kinect 뷰어](azure-kinect-viewer.md)에서 보기를 일시 중지하고 스크린샷 찍기 또는
2) [Azure Kinect 레코더](azure-kinect-recorder.md)를 사용하여 기록(예: `k4arecorder.exe -l 5 -r 5 output.mkv`)

## <a name="inconsistent-or-unexpected-device-timestamps"></a>일관되지 않거나 예기치 않은 디바이스 타임스탬프

```k4a_device_set_color_control```을 호출하면 안정화를 위해 몇 개를 캡처가 필요할 수 있는 디바이스에 일시적으로 타이밍 변경을 유도할 수 있습니다. 각각의 새 이미지로 내부 타이밍 계산을 다시 설정하지 않으려면 이미지 캡처 루프에서 API를 호출하는 것을 피합니다. 대신 카메라를 시작하기 전 또는 이미지 캡처 루프 내에서 값을 변경해야 할 때에만 API를 호출합니다. 특히 ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` 호출을 피합니다.

## <a name="usb3-host-controller-compatibility"></a>USB3 호스트 컨트롤러 호환성

디바이스 관리자 아래에 디바이스가 열거되고 있지 않은 경우 지원되지 않는 USB3 컨트롤러에 연결되어 있기 때문일 수 있습니다. 

Windows에서 Azure Kinect DK를 사용하는 경우 **Intel**, **TI(Texas Instruments)** , **Renesas** 가 *지원되는 유일한 호스트 컨트롤러* 입니다. Windows 플랫폼의 Azure Kinect SDK는 통합 컨테이너 ID에 의존하며 동일한 디바이스에 실제로 있는 깊이, 색상, 오디오 디바이스를 SDK가 찾을 수 있도록 USB 2.0 및 3.0 디바이스를 확장해야 합니다. Linux에서는 플랫폼이 컨테이너 ID에 덜 의존하고 디바이스 일련 번호에 더 의존하는 만큼 더 많은 호스트 컨트롤러가 지원될 수 있습니다. 

USB 호스트 컨트롤러 항목은 PC에 호스트 컨트롤러가 두 개 이상 설치되어 있을 때 훨씬 더 복잡해집니다. 호스트 컨트롤러가 혼합되어 있는 경우 일부 포트는 제대로 작동하고 다른 포트는 전혀 작동하지 않는 문제가 발생할 수 있습니다. 케이스에 포트를 유선으로 연결한 방법에 따라서는 모든 앞쪽 포트가 Azure Kinect와 문제가 있는 것을 볼 수도 있습니다.

**Windows:** 디바이스 관리자를 연 호스트 컨트롤러를 확인하려면

1. 보기 -> 유형별 디바이스 
2. Azure Kinect가 연결된 상태에서 카메라 선택->Azure Kinect 4K 카메라
3. 보기 -> 연결별 디바이스

![USB 포트 문제 해결](./media/resources/usb-troubleshooting.png)

PC에 연결된 USB 포트를 더 잘 이해하려면 Azure Kinect DK를 PC의 다른 USB 포트에 연결할 때 각 USB 포트에 대해 이러한 단계를 반복합니다.

## <a name="depth-camera-auto-powers-down"></a>깊이 카메라 자동 전원 끄기

깊이 카메라에서 이미지의 깊이 데이터를 계산하는 데 사용하는 레이저는 수명이 제한되어 있습니다. 레이저의 수명을 최대화하기 위해 깊이 데이터가 사용되지 않을 때를 깊이 카메라가 감지합니다. 디바이스는 몇 분 동안 스트리밍하고 있으나 호스트 PC에서 데이터를 읽지 않으면 깊이 카메라의 전원이 꺼집니다. 이는 또한 깊이 카메라가 스트리밍하고 있는 상태에서 종속 디바이스가 시작되고 깊이 프레임이 마스터 디바이스가 캡처 동기화를 시작하기를 기다리는 데 적극적으로 도움이 되고 있는 다중 디바이스 동기화에 영향을 줍니다. 다중 디바이스 캡처 시나리오에서 이 문제를 피하려면 첫 번째 종속 디바이스가 시작되고 1분 이내에 마스터 디바이스가 시작되도록 합니다. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Unreal로 신체 추적 SDK 사용

Unreal로 신체 추적 SDK를 사용하려면 `<SDK Installation Path>\tools`를 환경 변수 `PATH`에 추가하고 `dnn_model_2_0.onnx` 및 `cudnn64_7.dll`을 `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64`에 복사했는지 확인합니다.

## <a name="using-azure-kinect-on-headless-linux-system"></a>헤드리스 Linux 시스템에서 Azure Kinect 사용

Linux에서 Azure Kinect 깊이 엔진은 OpenGL을 사용합니다. OpenGL은 모니터가 시스템에 연결되어 있어야 하는 창 인스턴스가 필요합니다. 이 문제에 대한 해결 방법은 다음과 같습니다.

1. 사용하려는 사용자 계정에 대해 자동 로그인을 사용하도록 설정합니다. 자동 로그인을 사용하도록 설정하는 방법에 대한 지침은 [이](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/) 문서를 참조합니다.
2. 시스템 전원을 끄고, 모니터를 분리하고, 시스템 전원을 켭니다. 자동 로그인은 x-서버 세션을 강제로 만듭니다.
3. ssh를 통해 연결하고 DISPLAY env 변수 `export DISPLAY=:0`을 설정합니다.
4. Azure Kinect 애플리케이션을 시작합니다.

[xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) 유틸리티를 사용하여 자동 로그인 후 화면을 즉시 잠글 수 있습니다. 시작 애플리케이션 또는 시스템 서비스에 다음 명령을 추가합니다.

`bash -c “xtrlock -b”`

## <a name="missing-c-documentation"></a>C# 설명서 누락

센서 SDK C# 설명서는 [여기](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html)에 있습니다.

신체 추적 SDK C# 설명서는 [여기](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html)에 있습니다.

## <a name="specifying-onnx-runtime-execution-environment"></a>ONNX 런타임 실행 환경 지정

신체 추적 SDK는 CPU, CUDA, DirectML(Windows에만 해당), TensorRT 실행 환경을 지원하여 포즈 예측 모델을 유추합니다. `K4ABT_TRACKER_PROCESSING_MODE_GPU`는 Linux에서의 CUDA 실행 및 Windows에서의 DirectML 실행에 대한 기본값입니다. 특정 실행 환경을 선택하기 위해 세 가지 모드(`K4ABT_TRACKER_PROCESSING_MODE_GPU_CUDA`, `K4ABT_TRACKER_PROCESSING_MODE_GPU_DIRECTML`, `K4ABT_TRACKER_PROCESSING_MODE_GPU_TENSORRT`)가 더 추가되었습니다.

> [!NOTE]  
> ONNX 런타임은 가속화되지 않은 opcode에 대한 경고를 표시합니다. 이는 무시해도 괜찮습니다.

ONNX Runtime은 TensorRT 모델 캐싱을 제어하는 환경 변수를 포함합니다. 권장 값은 다음과 같습니다.
- ORT_TENSORRT_ENGINE_CACHE_ENABLE=1 
- ORT_TENSORRT_CACHE_PATH="pathname"

신체 추적을 시작하기에 앞서 폴더를 만들어야 합니다.

> [!IMPORTANT]  
> TensorRT는 유추에 앞서 모델을 미리 처리하여 다른 실행 환경에 비교했을 때 시작 시간이 확장되는 결과가 나옵니다. 엔진 캐싱은 이를 첫 실행으로 제한하지만 실험적이며 특정 모델(ONNX 런타임 버전, TensorRT 버전, GPU 모델)에만 해당됩니다.

TensorRT 실행 환경은 FP32(기본값) 및 FP16을 둘 다 지원합니다. FP16은 최소한의 정확도 감소로 최대 2배까지의 성능 향상을 제공합니다. FP16을 지정하려면:
- ORT_TENSORRT_FP16_ENABLE=1

## <a name="required-dlls-for-onnx-runtime-execution-environments"></a>ONNX 런타임 실행 환경에 필요한 DLL

|Mode      | CUDA 11.1            | CUDNN 8.0.5          | TensorRT 7.2.1       |
|----------|----------------------|----------------------|----------------------|
| CPU      | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| CUDA     | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           | cudnn_ops_infer64_8  |                      |
|          | cublas64_11          | cudnn_cnn_infer64_8  |                      |
|          | cublasLt64_11        |                      |                      |
| DirectML | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| TensorRT | cudart64_110         | cudnn64_8            | nvinfer              |
|          | cufft64_10           | cudnn_ops_infer64_8  | nvinfer_plugin       |
|          | cublas64_11          | cudnn_cnn_infer64_8  | myelin64_1           |
|          | cublasLt64_11        |                      |                      |
|          | nvrtc64_111_0        |                      |                      |
|          | nvrtc-builtins64_111 |                      |                      |

## <a name="next-steps"></a>다음 단계

[추가 지원 정보](support.md)