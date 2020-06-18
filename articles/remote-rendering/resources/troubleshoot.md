---
title: 문제 해결
description: Azure Remote Rendering 문제 해결 정보
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b6cb9c70de27e40c62d6a7adeece5cb39554c090
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844570"
---
# <a name="troubleshoot"></a>문제 해결

이 페이지에는 Azure Remote Rendering과 관련된 일반적인 문제와 해결 방법이 나열되어 있습니다.

## <a name="cant-link-storage-account-to-arr-account"></a>스토리지 계정을 ARR 계정에 연결할 수 없음

[스토리지 계정을 연결](../how-tos/create-an-account.md#link-storage-accounts)하는 동안 Remote Rendering 계정이 나열되지 않는 경우가 가끔 있습니다. 이 문제를 해결하려면 Azure Portal에서 ARR 계정으로 이동하여 왼쪽에 있는 **설정** 그룹에서 **ID**를 선택합니다. **상태**가 **켬**으로 설정되었는지 확인합니다.
![Unity 프레임 디버거](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>클라이언트가 서버에 연결할 수 없음

디바이스의 방화벽, 라우터 내부의 방화벽 등 방화벽이 다음 포트를 차단하지 않는지 확인합니다.

* **50051(TCP)** - 초기 연결에 필요(HTTP 핸드셰이크)
* **8266(TCP+UDP)** - 데이터를 전송하는 데 필요
* **5000(TCP)** , **5433(TCP)** , **8443(TCP)** - [ArrInspector](tools/arr-inspector.md)에 필요

## <a name="error-disconnected-videoformatnotavailable"></a>'연결 끊김: VideoFormatNotAvailable' 오류

GPU가 하드웨어 비디오 디코딩을 지원하는지 확인합니다. [개발 PC](../overview/system-requirements.md#development-pc)를 참조하세요.

GPU가 2개인 랩톱에서 작업하는 경우 실행 중인 GPU가 기본적으로 하드웨어 비디오 디코딩 기능을 제공하지 않는 것일 수 있습니다. 이 경우에 해당한다면 앱이 다른 GPU를 사용하도록 강제해 보세요. 이 작업은 GPU 드라이버 설정에서 수행할 수 있는 경우가 많습니다.

## <a name="h265-codec-not-available"></a>H265 코덱을 사용할 수 없음

서버가 **코덱을 사용할 수 없음** 오류와 함께 연결을 거부하는 두 가지 이유가 있습니다.

**H265 코덱이 설치되지 않음:**

먼저 시스템 요구 사항의 [소프트웨어](../overview/system-requirements.md#software) 섹션에 설명된 대로 **HEVC 비디오 확장**을 설치합니다.

그래도 문제가 발생하면 사용하는 그래픽 카드가 H265을 지원하고 최신 그래픽 드라이버가 설치되었는지 확인합니다. 공급업체별 정보는 시스템 요구 사항의 [개발 PC](../overview/system-requirements.md#development-pc) 섹션을 참조하세요.

**코덱이 설치되었지만 사용할 수 없음:**

이 문제가 발생하는 이유는 DLL의 보안 설정이 올바르지 않기 때문입니다. H265로 인코딩된 비디오를 시청할 때는 이 문제가 나타나지 않습니다. 또한 코덱을 다시 설치해도 문제가 해결되지 않습니다. 그 대신 다음 단계를 수행하세요.

1. **관리자 권한으로 PowerShell을 열고** 다음 명령을 실행합니다.

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    이 명령은 다음과 같이 코덱의 `InstallLocation`을 출력합니다.
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Windows 탐색기에서 이 폴더를 엽니다.
1. **x86** 및 **x64** 하위 폴더가 있을 것입니다. 폴더 중 하나를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
    1. **보안** 탭을 선택하고 **고급** 설정 단추를 클릭합니다.
    1. **소유자**에 대한 **변경**을 클릭합니다.
    1. 텍스트 필드에 **관리자**를 입력합니다.
    1. **이름 확인**을 클릭하고 **확인**을 클릭합니다.
1. 다른 폴더에 대해 위의 단계를 반복합니다.
1. 두 폴더 안에 있는 각 DLL 파일에서도 위의 단계를 반복합니다. 총 4개의 DLL이 있어야 합니다.

이제 설정이 올바른지 확인하기 위해, 4개의 각 DLL에 대해 다음 작업을 수행합니다.

1. **속성 > 보안 > 편집**을 클릭합니다.
1. 모든 **그룹/사용자** 목록을 살펴보고 각 항목의 **읽기 및 실행** 권한이 설정되었는지(**허용** 열의 확인 표시를 선택해야 함) 확인합니다.

## <a name="low-video-quality"></a>낮은 비디오 품질

네트워크 품질 때문에 또는 H265 비디오 코덱이 없어서 비디오 품질이 저하될 수 있습니다.

* [네트워크 문제 확인](#unstable-holograms) 단계를 참조하세요.
* 최신 그래픽 드라이버를 설치하기 위한 [시스템 요구 사항](../overview/system-requirements.md#development-pc)을 참조하세요.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>MRC로 녹화된 비디오는 라이브 환경의 품질을 반영하지 않습니다.

Hololens에서 [MRC(Mixed Reality Capture)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)를 통해 비디오를 녹화할 수 있습니다. 그러나 녹화된 비디오는 다음 두 가지 이유로 라이브 환경보다 품질이 떨어집니다.
* 비디오 프레임 속도는 60Hz가 아닌 30Hz로 제한됩니다.
* 비디오 이미지가 [후기 단계 다시 프로젝션](../overview/features/late-stage-reprojection.md) 처리 단계를 거치지 않으므로 뚝뚝 끊어집니다.

둘 다 녹화 기술의 제한입니다.

## <a name="black-screen-after-successful-model-loading"></a>모델을 성공적으로 로드 후 나타나는 검은색 화면

렌더링 런타임에 연결하여 모델을 성공적으로 로드했지만 이후에 검은색 화면만 표시되는 경우 몇 가지 다른 원인이 있을 수 있습니다.

보다 자세한 분석을 수행하기 전에 다음 사항을 테스트하는 것이 좋습니다.

* H265 코덱이 설치되었나요? H264 코덱을 대체하는 다른 코덱이 있겠지만, 다른 코덱이 제대로 작동하지 않는 경우가 있습니다. 최신 그래픽 드라이버를 설치하기 위한 [시스템 요구 사항](../overview/system-requirements.md#development-pc)을 참조하세요.
* Unity 프로젝트를 사용하는 경우 Unity를 닫고, 프로젝트 디렉터리의 임시 *library* 및 *obj* 폴더를 삭제하고, 프로젝트를 다시 로드/빌드합니다. 특별한 이유 없이 캐시된 데이터로 인해 샘플이 올바르게 작동하지 않는 경우가 있습니다.

이러한 두 단계로 문제가 해결되지 않으면 클라이언트가 비디오 프레임을 수신하는지 여부를 확인해야 합니다. 이는 [서버 쪽 성능 쿼리](../overview/features/performance-queries.md) 챕터에서 설명한 대로 프로그래밍 방식으로 쿼리할 수 있습니다. `FrameStatistics struct`에는 수신된 비디오 프레임 수를 나타내는 멤버가 있습니다. 이 숫자가 0보다 크고 시간이 지날수록 증가하면 클라이언트가 서버에서 실제 비디오 프레임을 수신하는 것입니다. 따라서 클라이언트 쪽 문제입니다.

### <a name="common-client-side-issues"></a>일반적인 클라이언트 쪽 문제

**모델이 선택한 VM의 제한, 특히 최대 다각형 수를 초과하는 경우:**

특정 [VM 크기 제한](../reference/limits.md#overall-number-of-polygons)을 참조하세요.

**모델이 보기 절두체 내에 없는 경우:**

모델이 올바르게 표시되지만 카메라 절두체의 외부에 위치한 경우가 자주 있습니다. 중심에서 멀리 떨어진 피벗을 사용하여 모델을 내보내면 모델이 카메라의 원거리 클리핑 거리에 의해 잘리는 것이 이 현상의 주요 원인입니다. 모델의 경계 상자를 프로그래밍 방식으로 쿼리하고 Unity를 사용하여 상자를 선 상자로 시각화하거나 해당 값을 디버그 로그에 출력하면 도움이 됩니다.

또한 변환 프로세스는 변환된 모델과 함께 [출력 json 파일](../how-tos/conversion/get-information.md)을 생성합니다. 모델 위치 지정 문제를 디버그하려면 [outputStatistics 섹션](../how-tos/conversion/get-information.md#the-outputstatistics-section)에서 `boundingBox` 항목을 확인하는 것이 좋습니다.

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

경계 상자는 3D 공간에서 `min` 및 `max` 위치(미터 단위)로 설명됩니다. 따라서 좌표 1000.0은 원점에서 1킬로미터 떨어져 있다는 뜻입니다.

이 경계 상자에 다음 두 가지 문제가 있으면 기하 도형이 보이지 않습니다.
* **상자가 중심에서 멀리 떨어져 있는 경우:** 원거리 클리핑 때문에 개체가 모두 잘립니다. 이 경우 `boundingBox` 값은 `min = [-2000, -5,-5], max = [-1990, 5,5]`와 비슷한 형태입니다. 여기서는 예를 들기 위해 x축에 큰 오프셋을 사용했습니다. 이러한 유형의 문제를 해결하려면 [모델 변환 구성](../how-tos/conversion/configure-model-conversion.md)에서 `recenterToOrigin` 옵션을 사용하도록 설정합니다.
* **상자가 가운데에 있지만 크기가 너무 큰 경우:** 카메라가 모델의 중앙에서 시작하더라도 모든 방향에서 기하도형이 잘립니다. 이 경우의 일반적인 `boundingBox` 값은 `min = [-1000,-1000,-1000], max = [1000,1000,1000]`과 비슷한 형태입니다. 이러한 유형의 문제가 발생하는 이유는 대부분 단위 배율 불일치입니다. 단위 배율을 보정하려면 [변환하는 동안 스케일링 값을 지정](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)하거나 올바른 단위로 원본 모델을 표시합니다. 런타임에 모델을 로드할 때 루트 노드에 스케일링을 적용할 수도 있습니다.

**Unity 렌더링 파이프라인에 렌더링 후크가 없는 경우:**

Azure Remote Rendering은 Unity 렌더링 파이프라인에 후크하여 비디오의 프레임 컴퍼지션과 다시 프로젝션을 수행합니다. 이러한 후크가 있는지 확인하려면 메뉴에서 *창 > 분석 > 프레임 디버거*를 엽니다. 디버거를 사용하도록 설정하고 파이프라인에 `HolographicRemotingCallbackPass`에 대한 두 개의 항목이 있는지 확인합니다.

![Unity 프레임 디버거](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Remote Rendering API를 사용하는 Unity 코드가 컴파일되지 않음

### <a name="use-debug-when-compiling-for-unity-editor"></a>Unity 편집기에 사용하기 위해 컴파일할 때 디버그 사용

Unity 솔루션의 *빌드 형식*을 **디버그**로 전환합니다. Unity 편집기에서 ARR을 테스트할 때 `UNITY_EDITOR` 정의는 '디버그' 빌드에서만 사용할 수 있습니다. [배포된 애플리케이션](../quickstarts/deploy-to-hololens.md)에 사용되는 빌드 형식과는 관련이 없습니다. 배포된 애플리케이션에는 '릴리스' 빌드를 사용해야 합니다.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>HoloLens 2용 Unity 샘플을 컴파일하는 동안 컴파일 오류 발생

HoloLens 2용 Unity 샘플(빠른 시작, ShowCaseApp 등)을 컴파일하려고 할 때 가짜 오류가 발생하는 경우가 있습니다. Visual Studio가 파일을 복사할 수 없다는 메시지를 표시하는데, 해당 파일이 분명 있습니다. 이 문제가 발생하는 경우:
* 프로젝트의 임시 Unity 파일을 모두 제거하고 다시 시도합니다. 즉, Unity 프로젝트를 닫고, 프로젝트 디렉터리의 임시 *library* 및 *obj* 폴더를 삭제하고, 프로젝트를 다시 로드/빌드합니다.
* 프로젝트가 있는 디렉터리의 경로가 너무 길지는 않은지 확인합니다. 파일 이름이 너무 길면 복사 단계에서 문제가 발생할 수 있습니다.
* 이렇게 해도 문제가 해결되지 않으면 MS Sense가 복사 단계를 방해하는 것일 수 있습니다. 예외를 설정하려면 명령줄에서 다음 레지스트리 명령을 실행합니다(관리자 권한 필요).
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>불안정한 홀로그램

머리를 움직이면 렌더링된 개체가 함께 이동하는 것처럼 보이는 경우 LSR(*후기 단계 다시 프로젝션*) 문제가 발생할 수 있습니다. 이러한 상황을 해결하는 방법에 대한 지침은 [후기 단계 다시 프로젝션](../overview/features/late-stage-reprojection.md)의 섹션을 참조하세요.

네트워크 연결 불량 때문에, 특히 네트워크 대역폭이 부족하거나 대기 시간이 너무 길어도 홀로그램이 불안정(홀로그램 흔들림, 뒤틀림, 지터링 또는 점프)할 수 있습니다. 네트워크 연결 품질을 보여주는 좋은 지표는 [성능 통계](../overview/features/performance-queries.md) 값 `ARRServiceStats.VideoFramesReused`입니다. 재사용된 프레임은 새 비디오 프레임을 사용할 수 없어서 클라이언트 쪽에서 이전 비디오 프레임을 재사용해야만 했다는 상황을 보여줍니다. 패킷 손실, 네트워크 대기 시간의 변동 등을 예로 들 수 있습니다. `ARRServiceStats.VideoFramesReused`가 0보다 큰 상황이 자주 발생하면 네트워크에 문제가 있다는 뜻입니다.

확인할 또 다른 값은 `ARRServiceStats.LatencyPoseToReceiveAvg`입니다. 이 값은 지속적으로 100밀리초 미만이어야 합니다. 이 값을 초과하면 현재 연결된 데이터 센터가 너무 멀리 떨어져 있다는 뜻입니다.

문제를 완화하는 데 도움이 될 수 있는 방법 목록은 [네트워크 연결 지침](../reference/network-requirements.md#guidelines-for-network-connectivity)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [시스템 요구 사항](../overview/system-requirements.md)
* [네트워크 요구 사항](../reference/network-requirements.md)
