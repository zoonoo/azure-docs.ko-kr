---
title: 문제 해결
description: Azure 원격 렌더링에 대 한 문제 해결 정보
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: c1b807c6e4fa269ac2ab8d7eacd3ca1d4f81a1ca
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792618"
---
# <a name="troubleshoot"></a>문제 해결

이 페이지에는 Azure 원격 렌더링과 충돌 하는 일반적인 문제 및 해결 방법이 나열 되어 있습니다.

## <a name="client-cant-connect-to-server"></a>클라이언트에서 서버에 연결할 수 없음

방화벽 (장치, 라우터 내 등)이 다음 포트를 차단 하지 않는지 확인 합니다.

* **50051 (TCP)** -초기 연결에 필요 (HTTP 핸드셰이크)
* **8266 (TCP + UDP)** -데이터를 전송 하는 데 필요 합니다.
* **5000 (tcp)**, **5433 (tcp)**, **8443 (Tcp)** - [arrinspector](tools/arr-inspector.md) 에 필요 합니다.

## <a name="error-disconnected-videoformatnotavailable"></a>오류 ' 연결 끊김: VideoFormatNotAvailable '

GPU에서 하드웨어 비디오 디코딩을 지원 하는지 확인 합니다. [개발 PC](../overview/system-requirements.md#development-pc)를 참조 하세요.

두 Gpu를 사용 하는 랩톱에서 작업 하는 경우 기본적으로 실행 중인 GPU가 하드웨어 비디오 디코딩 기능을 제공 하지 않을 수 있습니다. 그렇다면 응용 프로그램이 다른 GPU를 사용 하도록 강제 합니다. 이는 종종 GPU 드라이버 설정에서 가능 합니다.

## <a name="h265-codec-not-available"></a>H265 코덱을 사용할 수 없음

서버에서 **코덱이 사용할 수 없음** 오류가 발생 하는 것을 거부할 수 있는 두 가지 이유가 있습니다.

**H265 코덱이 설치 되어 있지 않습니다.**

먼저 시스템 요구 사항의 [소프트웨어](../overview/system-requirements.md#software) 섹션에 설명 된 대로 **HEVC 비디오 확장** 을 설치 해야 합니다.

여전히 문제가 발생 하는 경우 그래픽 카드가 H265을 지원 하 고 최신 그래픽 드라이버를 설치 했는지 확인 합니다. 공급 업체별 정보는 시스템 요구 사항에 대 한 [개발 PC](../overview/system-requirements.md#development-pc) 섹션을 참조 하세요.

**코덱이 설치 되었지만 사용할 수 없습니다.**

이 문제가 발생 하는 이유는 Dll의 보안 설정이 올바르지 않기 때문입니다. 이 문제는 H265로 인코딩된 비디오를 시청 하려고 할 때 매니페스트가 아닙니다. 코덱을 다시 설치 해도 문제가 해결 되지 않습니다. 대신 다음 단계를 수행 합니다.

1. **관리자 권한으로 PowerShell** 을 열고 다음을 실행 합니다.

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    해당 명령은 다음과 같이 코덱의 `InstallLocation` 를 출력 해야 합니다.
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Windows 탐색기에서 해당 폴더를 엽니다.
1. **X86** 및 **x64** 하위 폴더가 있어야 합니다. 폴더 중 하나를 마우스 오른쪽 단추로 클릭 하 고 **속성** 을 선택 합니다.
    1. **보안** 탭을 선택 하 고 **고급** 설정 단추를 클릭 합니다.
    1. **소유자** 에 대해 **변경** 을 클릭 합니다.
    1. 텍스트 필드에 **관리자** 를 입력 합니다.
    1. **이름 확인** 및 **확인** 을 클릭 합니다.
1. 다른 폴더에 대해 위의 단계를 반복 합니다.
1. 또한 두 폴더 내의 각 DLL 파일에서 위의 단계를 반복 합니다. 4 개의 Dll이 모두 있어야 합니다.

설정이 올바른지 확인 하려면 다음 4 개의 Dll 각각에 대해이 작업을 수행 합니다.

1. **속성 > 보안 > 편집** 을 선택 합니다.
1. 모든 **그룹/사용자** 목록을 살펴보고 각 그룹에 **읽기 & 실행** 권한 집합이 있는지 확인 합니다 ( **허용** 열의 확인 표시는 선택 이어야 함).

## <a name="low-video-quality"></a>낮은 비디오 품질

비디오 품질이 네트워크 품질 또는 누락 된 H265 비디오 코덱을 통해 손상 될 수 있습니다.

* [네트워크 문제를 확인](#unstable-holograms)하는 단계를 참조 하세요.
* 최신 그래픽 드라이버를 설치 하기 위한 [시스템 요구 사항을](../overview/system-requirements.md#development-pc) 참조 하십시오.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>MRC로 기록 된 비디오는 라이브 환경의 품질을 반영 하지 않습니다.

비디오가 [혼합 현실 캡처 (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)를 통해 Hololens에 기록 될 수 있습니다. 그러나 결과 비디오는 다음 두 가지 이유로 라이브 환경 보다 품질이 떨어집니다.
* 비디오 프레임 속도는 60 Hz와는 달리 30 Hz로 더 있습니다.
* 비디오 이미지는 [지연 단계 다시 프로젝션](../overview/features/late-stage-reprojection.md) 처리 단계를 거치지 않으므로 비디오가 choppier 보입니다.

둘 다 기록 기술의 내재 된 제한 사항입니다.

## <a name="black-screen-after-successful-model-loading"></a>모델 로드 성공 후 검은색 화면

렌더링 런타임에 연결 하 여 모델을 성공적으로 로드 했지만 나중에 검정색 화면만 표시 되는 경우에는 몇 가지 다른 원인이 있을 수 있습니다.

보다 자세한 분석을 수행 하기 전에 다음 작업을 테스트 하는 것이 좋습니다.

* H265 코덱이 설치 되어 있나요? H264 코덱에 대 한 대체 (fallback)가 있어야 하지만이 대체가 제대로 작동 하지 않는 경우가 있습니다. 최신 그래픽 드라이버를 설치 하기 위한 [시스템 요구 사항을](../overview/system-requirements.md#development-pc) 참조 하십시오.
* Unity 프로젝트를 사용 하는 경우 Unity를 닫고 프로젝트 디렉터리에서 임시 *라이브러리* 및 *obj* 폴더를 삭제 하 고 프로젝트를 다시 로드/빌드합니다. 경우에 따라 캐시 된 데이터로 인해 샘플이 적절 하지 않은 이유로 제대로 작동 하지 않을 수 있습니다.

이러한 두 단계가 도움이 되지 않으면 클라이언트가 비디오 프레임을 받았는지 여부를 확인 해야 합니다. [서버 쪽 성능 쿼리](../overview/features/performance-queries.md) 챕터에서 설명한 대로 프로그래밍 방식으로 쿼리할 수 있습니다. 에 `FrameStatistics struct` 는 수신 된 비디오 프레임 수를 나타내는 멤버가 있습니다. 이 숫자가 0 보다 크고 시간이 지남에 따라 증가 하는 경우 클라이언트는 서버에서 실제 비디오 프레임을 받습니다. 따라서 클라이언트 쪽에서 문제가 발생 해야 합니다.

### <a name="common-client-side-issues"></a>일반적인 클라이언트 쪽 문제

**모델이 선택한 VM의 한도를 초과 합니다. 특히 최대 다각형 수는 다음과 같습니다.**

특정 [VM 크기 제한](../reference/limits.md#overall-number-of-polygons)을 참조 하세요.

**모델이 뷰 내에 없습니다.**

대부분의 경우 모델은 올바르게 표시 되지만 카메라의 외부에 위치 합니다. 일반적으로 모델을 멀리 떨어져 있는 피벗을 사용 하 여 내보낸 후 카메라의 먼 클리핑 평면에 의해 잘립니다. 모델의 경계 상자를 프로그래밍 방식으로 쿼리 하 고 Unity를 사용 하 여 상자를 선 상자로 시각화 하거나 해당 값을 디버그 로그에 출력 하는 것이 좋습니다.

또한 변환 프로세스는 변환 된 모델과 함께 [출력 json 파일](../how-tos/conversion/get-information.md) 을 생성 합니다. 모델 위치 지정 문제를 디버깅 하기 위해 [Outputstatistics 섹션](../how-tos/conversion/get-information.md#the-outputstatistics-section)의 `boundingBox` 항목을 살펴보면 됩니다.

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

경계 상자는 3D 공간에서 `min` 및 `max` 위치 (미터)로 설명 됩니다. 따라서 1000.0 좌표는 원점에서 1 kilometer 떨어져 있음을 의미 합니다.

이 경계 상자에는 보이지 않는 기 하 도형으로 이어질 수 있는 두 가지 문제가 있을 수 있습니다.
* **상자는 중앙에서 멀리 떨어져 있을 수**있으므로 개체가 멀리 떨어져 있기 때문에 모두 잘립니다. 이 `boundingBox` 경우 값은 다음과 같이 표시 됩니다. `min = [-2000, -5,-5], max = [-1990, 5,5]`x 축에 대 한 많은 오프셋을 여기에 예로 사용 합니다. 이러한 유형의 문제를 해결 하려면 `recenterToOrigin` [모델 변환 구성](../how-tos/conversion/configure-model-conversion.md)에서 옵션을 사용 하도록 설정 합니다.
* **상자는 가운데 맞춤 되지만 크기가 너무 클**수 있습니다. 즉, 카메라가 모델 중앙에서 시작 하는 경우 해당 기 하 도형은 모든 방향으로 잘립니다. 이 `boundingBox` 경우 일반적인 값은와 `min = [-1000,-1000,-1000], max = [1000,1000,1000]`같습니다. 이러한 유형의 문제는 일반적으로 단위 배율이 일치 하지 않습니다. 보정 하려면 [변환 하는 동안 크기 조정 값](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) 을 지정 하거나 올바른 단위를 사용 하 여 원본 모델을 표시 합니다. 런타임에 모델을 로드할 때 루트 노드에도 크기 조정을 적용할 수 있습니다.

**Unity 렌더링 파이프라인에는 렌더링 후크가 포함 되지 않습니다.**

Azure 원격 렌더링은 Unity 렌더링 파이프라인에 후크하고 비디오를 사용 하 여 프레임 컴퍼지션을 수행 하 고 다시 프로젝션을 수행 합니다. 이러한 후크가 있는지 확인 하려면 메뉴 *창 > 분석 > 프레임 디버거*를 엽니다. 파이프라인에서를 사용 하도록 설정 하 고에 대 한 `HolographicRemotingCallbackPass` 두 개의 항목이 있는지 확인 합니다.

![Unity 프레임 디버거](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>원격 렌더링 API를 사용 하는 Unity 코드는 컴파일되지 않습니다.

### <a name="use-debug-when-compiling-for-unity-editor"></a>Unity 편집기를 컴파일할 때 디버그 사용

Unity 솔루션의 *빌드 형식을* **디버그**로 전환 합니다. Unity 편집기에서 ARR을 테스트할 때 ' 디버그 `UNITY_EDITOR` ' 빌드에서만 정의를 사용할 수 있습니다. 이는 [배포 된 응용 프로그램](../quickstarts/deploy-to-hololens.md)에 사용 되는 빌드 형식과 관련이 없으며 ' 릴리스 ' 빌드를 선호 해야 합니다.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>HoloLens 용 Unity 샘플을 컴파일하는 동안 컴파일 오류가 발생 했습니다. 2

HoloLens 2에 대 한 Unity 샘플 (빠른 시작, ShowCaseApp, ...)을 컴파일하려고 할 때 발생 하는 오류가 표시 됩니다. Visual Studio는 불만 일부 파일을 복사할 수 없습니다. 이 문제가 발생 하는 경우:
* 프로젝트에서 모든 임시 Unity 파일을 제거 하 고 다시 시도 하세요.
* 복사 단계에서 긴 파일 이름으로 인해 문제가 발생 하는 것 처럼 보일 수 있으므로 프로젝트가 합리적인 짧은 경로를 사용 하는 디스크의 디렉터리에 있는지 확인 합니다.
* 그래도 도움이 되지 않으면 MS Sense가 복사 단계에 방해가 될 수 있습니다. 예외를 설정 하려면 명령줄에서 다음 레지스트리 명령을 실행 합니다 (관리자 권한 필요).
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>불안정 한 Holograms

렌더링 된 개체가 head 이동과 함께 이동 하는 것 처럼 보이는 경우에는 lsr ( *늦은 단계 다시 프로젝션* )의 문제가 발생할 수 있습니다. 이러한 상황을 접근 하는 방법에 대 한 지침은 [후기 단계 Reprojection](../overview/features/late-stage-reprojection.md) 에 대 한 섹션을 참조 하세요.

불안정 한 holograms (wobbling, 휘기, jittering 또는 점프 holograms)의 또 다른 이유는 네트워크 연결 불량, 특히 네트워크 대역폭이 부족 하거나 대기 시간이 너무 높을 수 있습니다. 네트워크 연결의 품질에 대 한 좋은 지표는 [성능 통계](../overview/features/performance-queries.md) 값 `ARRServiceStats.VideoFramesReused`입니다. 재사용 된 프레임은 새 비디오 프레임을 사용할 수 없기 때문에 클라이언트 쪽에서 이전 비디오 프레임을 다시 사용 해야 하는 상황을 표시 합니다 (예: 패킷 손실 또는 네트워크 대기 시간의 변동 때문). 가 `ARRServiceStats.VideoFramesReused` 0 보다 자주 큰 경우 네트워크 문제가 있음을 나타냅니다.

확인할 다른 값은 `ARRServiceStats.LatencyPoseToReceiveAvg`입니다. 이는 지속적으로 100 밀리초 미만 이어야 합니다. 높은 값이 표시 되는 경우이는 너무 멀리 떨어진 데이터 센터에 연결 되어 있음을 나타냅니다.

잠재적 완화의 목록은 [네트워크 연결에 대 한 지침](../reference/network-requirements.md#guidelines-for-network-connectivity)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [시스템 요구 사항](../overview/system-requirements.md)
* [네트워크 요구 사항](../reference/network-requirements.md)
