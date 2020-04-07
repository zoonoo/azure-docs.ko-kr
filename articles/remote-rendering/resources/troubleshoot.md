---
title: 문제 해결
description: Azure 원격 렌더링에 대한 문제 해결 정보
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7ee219ae5ace0f0da398cc542f410d3c895c8bd4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679986"
---
# <a name="troubleshoot"></a>문제 해결

이 페이지에는 Azure 원격 렌더링을 방해하는 일반적인 문제와 이를 해결하는 방법이 나열되어 있습니다.

## <a name="client-cant-connect-to-server"></a>클라이언트가 서버에 연결할 수 없습니다.

방화벽(장치, 라우터 내부 등)이 다음 포트를 차단하지 않도록 하십시오.

* **50051(TCP)** - 초기 연결에 필요한 경우(HTTP 핸드셰이크)
* **8266(TCP+UDP)** - 데이터 전송에 필요
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - [ArrInspector에](tools/arr-inspector.md) 필요한

## <a name="error-disconnected-videoformatnotavailable"></a>오류 '연결이 끊어졌습니다: 비디오 형식을 사용할 수 없음'

GPU가 하드웨어 비디오 디코딩을 지원하는지 확인합니다. [개발 PC를](../overview/system-requirements.md#development-pc)참조하십시오.

두 개의 GPU가 있는 랩톱에서 작업하는 경우 기본적으로 실행 중인 GPU가 하드웨어 비디오 디코딩 기능을 제공하지 않을 수 있습니다. 그렇다면 앱이 다른 GPU를 사용하도록 강제적용해 보십시오. GPU 드라이버 설정에서 가능한 경우가 많습니다.

## <a name="h265-codec-not-available"></a>H265 코덱을 사용할 수 없습니다.

서버가 **사용할 수 없는 코덱** 오류로 연결을 거부할 수 있는 데는 두 가지 이유가 있습니다.

**H265 코덱이 설치되지 않았습니다.**

먼저 시스템 요구 사항의 [소프트웨어](../overview/system-requirements.md#software) 섹션에 언급 된 대로 **HEVC 비디오 확장 프로그램을** 설치 해야 합니다.

그래도 문제가 발생하면 그래픽 카드가 H265를 지원하며 최신 그래픽 드라이버가 설치되어 있는지 확인하십시오. 공급업체별 정보에 대한 시스템 요구 사항의 [개발 PC](../overview/system-requirements.md#development-pc) 섹션을 참조하십시오.

**코덱이 설치되었지만 사용할 수 없습니다.**

이 문제의 원인은 DLL에 대한 잘못된 보안 설정때문입니다. H265로 인코딩된 비디오를 시청하려고 할 때 이 문제는 나타나지 않습니다. 코덱을 다시 설치해도 문제가 해결되지 않습니다. 대신 다음 단계를 수행합니다.

1. 관리자 **권한으로 PowerShell을** 열고 실행

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    이 명령은 다음과 `InstallLocation` 같은 코덱을 출력해야 합니다.
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Windows 탐색기에서 해당 폴더 열기
1. **x86** 및 **x64** 하위 폴더가 있어야합니다. 폴더 중 하나를 마우스 오른쪽 버튼으로 클릭하고 **속성을** 선택합니다.
    1. **보안** 탭을 선택하고 **고급** 설정 버튼을 클릭합니다.
    1. **소유자의** **변경을** 클릭합니다.
    1. 텍스트 필드에 **관리자** 입력
    1. **이름 확인** 및 **확인을** 클릭합니다.
1. 다른 폴더에 대해 위의 단계를 반복합니다.
1. 또한 두 폴더 내부의 각 DLL 파일에 위의 단계를 반복합니다. 4개의 DLL이 모두 있어야 합니다.

이제 설정이 올바른지 확인하려면 네 개의 DLL 각각에 대해 이 작업을 수행합니다.

1. **보안 > 편집> 속성** 선택 >
1. 모든 그룹 / **사용자의** 목록을 통해 가서 각 하나 읽기 **& 실행** 오른쪽 세트가 있는지 확인 **(허용** 열의 체크 표시를 선택해야합니다)

## <a name="low-video-quality"></a>낮은 비디오 품질

비디오 품질은 네트워크 품질 또는 누락된 H265 비디오 코덱으로 인해 손상될 수 있습니다.

* 네트워크 문제를 [식별하는](#unstable-holograms)단계를 참조하십시오.
* 최신 그래픽 드라이버 설치에 대한 [시스템 요구 사항을](../overview/system-requirements.md#development-pc) 참조하십시오.

## <a name="black-screen-after-successful-model-loading"></a>모델 로딩 성공 후 검은색 화면

렌더링 런타임에 연결되고 모델을 성공적으로 로드했지만 나중에 검은색 화면만 표시되는 경우 몇 가지 뚜렷한 원인이 있을 수 있습니다.

보다 심층적인 분석을 수행하기 전에 다음 사항을 테스트하는 것이 좋습니다.

* H265 코덱이 설치되어 있습니까? H264 코덱에 대한 대체가 있어야하지만 이 대체가 제대로 작동하지 않는 경우를 보았습니다. 최신 그래픽 드라이버 설치에 대한 [시스템 요구 사항을](../overview/system-requirements.md#development-pc) 참조하십시오.
* Unity 프로젝트를 사용하는 경우 Unity를 닫고 프로젝트 디렉터리에서 임시 *라이브러리* 및 *obj* 폴더를 삭제하고 프로젝트를 다시 로드/빌드합니다. 경우에 따라 캐시된 데이터로 인해 뚜렷한 이유 없이 샘플이 제대로 작동하지 않는 경우가 있습니다.

이 두 단계가 도움이 되지 않으면 클라이언트에서 비디오 프레임을 수신하는지 여부를 확인해야 합니다. 서버 측 성능 쿼리 장에서 설명한 대로 프로그래밍 방식으로 쿼리할 수 [있습니다.](../overview/features/performance-queries.md) 수신된 `FrameStatistics struct` 비디오 프레임 수를 나타내는 멤버가 있습니다. 이 숫자가 0보다 크고 시간이 지남에 따라 증가하는 경우 클라이언트는 서버에서 실제 비디오 프레임을 수신합니다. 따라서 클라이언트 측에서 문제가 되어야 합니다.

### <a name="common-client-side-issues"></a>일반적인 클라이언트 측 문제

**모델이 뷰 절두체 내부에 있지 않습니다.**

대부분의 경우 모델이 올바르게 표시되지만 카메라 프러스트 외부에 있습니다. 일반적인 이유는 모델이 멀리 중심에서 벗어난 피벗으로 내보내져 카메라의 원거리 클리핑 평면에 의해 잘리기 때문입니다. 모델의 경계 상자를 프로그래밍 방식으로 쿼리하고 Unity를 사용하여 상자를 줄 상자로 시각화하거나 해당 값을 디버그 로그에 인쇄하는 데 도움이 됩니다.

**Unity 렌더 파이프라인에는 렌더 후크가 포함되지 않습니다.**

Azure 원격 렌더링은 Unity 렌더링 파이프라인에 연결하여 비디오로 프레임 컴포지션을 하고 다시 투영을 수행합니다. 이러한 후크가 있는지 확인하려면 *프레임 디버거*를 > > 분석 > 메뉴를 엽니다. 이를 활성화하고 `HolographicRemotingCallbackPass` 파이프라인에 두 개의 항목이 있는지 확인합니다.

![유니티 프레임 디버거](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>원격 렌더링 API를 사용하는 Unity 코드는 컴파일되지 않습니다.

Unity 솔루션의 *빌드 유형을* **디버그로**전환합니다. Unity 편집기에서 ARR을 `UNITY_EDITOR` 테스트할 때 정의는 '디버그' 빌드에서만 사용할 수 있습니다. 이는 [배포된 응용 프로그램에](../quickstarts/deploy-to-hololens.md)사용되는 빌드 유형과 관련이 없습니다.

## <a name="unstable-holograms"></a>불안정한 홀로그램

렌더링된 오브젝트가 머리 움직임과 함께 이동하는 것처럼 보이는 경우 후기 단계 재투영(LSR)에 문제가 발생할 수 있습니다. *Late Stage Reprojection* 이러한 상황에 접근하는 방법에 대한 지침은 [후기 단계 재투영의](../overview/features/late-stage-reprojection.md) 섹션을 참조하십시오.

불안정한 홀로그램(흔들림, 뒤틀림, 지터링 또는 점프 홀로그램)의 또 다른 이유는 네트워크 연결이 불량하거나 특히 네트워크 대역폭이 부족하거나 대기 시간이 너무 길어질 수 있습니다. 네트워크 연결 품질에 대한 좋은 지표는 성능 `ARRServiceStats.VideoFramesReused` [통계](../overview/features/performance-queries.md) 값입니다. 재사용된 프레임은 새 비디오 프레임을 사용할 수 없었기 때문에 클라이언트 측에서 이전 비디오 프레임을 재사용해야 하는 상황(예: 패킷 손실 또는 네트워크 대기 시간의 변화)을 나타냅니다. 0보다 큰 경우가 많으면 `ARRServiceStats.VideoFramesReused` 네트워크 문제가 있음을 나타냅니다.

살펴봐야 할 또 `ARRServiceStats.LatencyPoseToReceiveAvg`다른 값은 . 그것은 지속적으로 100 ms 미만이어야합니다. 더 높은 값이 표시되면 너무 멀리 떨어진 데이터 센터에 연결되어 있음을 나타냅니다.

잠재적인 완화 목록은 [네트워크 연결에 대한 지침을](../reference/network-requirements.md#guidelines-for-network-connectivity)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [시스템 요구 사항](../overview/system-requirements.md)
* [네트워크 요구 사항](../reference/network-requirements.md)
