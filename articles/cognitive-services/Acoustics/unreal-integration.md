---
title: Project Acoustics Unreal 및 Wtointegration
titlesuffix: Azure Cognitive Services
description: 이 방법에서는 프로젝트 Acoustics Unreal 및 wto플러그 인을 프로젝트에 통합 하는 방법을 설명 합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 47f39e8dcd96ea3bdba564df348e9b89a6b036ba
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933180"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project Acoustics Unreal 및 Wtointegration
이 방법은 Project Acoustics 플러그 인 패키지의 자세한 통합 단계를 기존 Unreal 및 Wtogame 프로젝트에 제공 합니다. 

소프트웨어 요구 사항:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Unreal의 wtoplugin](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Wtounreal 플러그 인을 사용 하는 대신 WTOSDK의 직접 통합을 사용 하는 경우 프로젝트 Acoustics Unreal 플러그 인을 참조 하 고 WTOAPI 호출을 조정 합니다.

Wto이 아닌 오디오 엔진과 함께 Project Acoustics를 사용 하려는 경우 [Project Acoustics 토론 포럼](https://github.com/microsoft/ProjectAcoustics/issues)에서 향상 된 요청을 수행 하세요. Acoustics Unreal 플러그 인 프로젝트를 사용 하 여 Acoustics 데이터를 쿼리 한 다음 엔진에 대 한 API 호출을 수행할 수 있습니다.

## <a name="download-project-acoustics"></a>프로젝트 다운로드 Acoustics
아직 없는 경우 [Acoustics Unreal & wacplugin 패키지 프로젝트](https://www.microsoft.com/download/details.aspx?id=58090)를 다운로드 합니다. 

패키지에 Unreal Engine 플러그 인 및 wto믹서 플러그 인이 포함 되어 있습니다. Unreal 플러그 인은 편집기 및 런타임 통합을 제공 합니다. 게임을 진행 하는 동안 Acoustics Unreal plugin 프로젝트는 각 게임 개체의 폐색 같은 매개 변수를 계산 합니다. 이러한 매개 변수는 WTOAPI 호출로 변환 됩니다.

## <a name="review-integration-steps"></a>통합 단계 검토

패키지를 설치 하 고 게임에 배포 하는 기본 단계는 다음과 같습니다.
1. 프로젝트 Acoustics Wto믹서 플러그 인 설치
2. (다시) 게임에 배포 합니다. 이 단계에서는 믹서 플러그 인을 게임 프로젝트로 전파 합니다.
3. 게임에 프로젝트 Acoustics Unreal 플러그 인 추가
4. Wwise의 Unreal 플러그 인 기능 확장
5. 게임 빌드 및 Python 사용 설정 확인
6. Project Acoustics를 사용 하도록 Wtoproject 설정
7. 실제 오디오에서 오디오 설정

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Project Acoustics mixer 플러그 인 설치
* Wwise 시작 관리자를 연 다음, **플러그 인** 탭의 **새 플러그 인 설치**에서 **디렉터리에서 추가**를 선택합니다. 

    ![Wto시작 관리자에서 플러그 인을 설치 하는 스크린샷](media/wwise-install-new-plugin.png)

* 다운로드한 패키지에 포함된 `AcousticsWwisePlugin\ProjectAcoustics` 디렉터리를 선택합니다. 이 파일은 Wto믹서 플러그 인 번들을 포함 합니다.

* 플러그 인을 설치 합니다. 이제 Project Acoustics가 설치 된 플러그 인 목록에 표시 됩니다.  
![Project Acoustics 설치 후의 설치 된 플러그 인 목록 스크린샷](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (다시) 게임에 배포
이미 통합 한 경우에도 게임에 다시 배포 합니다. 그러면 프로젝트 Acoustics Wtoplugin이 선택 됩니다.

* **엔진 플러그 인:** Unreal C++ 프로젝트에 게임 플러그 인을 설치한 경우이 단계를 건너뜁니다. 예를 들어, 실제 프로젝트가 청사진 으로만 사용 되기 때문에 엔진 플러그 인 대신 설치 된 경우에는 믹서 플러그 인을 사용한 Wwise 배포가 더 복잡 합니다. 빈 빈 C++ 프로젝트를 만들고, unreal editor가 열리면 나머지 절차를 수행 하 여이 더미 프로젝트를 배포 합니다. 그런 다음 배포 된 Wtoplugin을 복사 합니다.
 
* Wwise 시작 관리자에서 **Unreal Engine** 탭을 선택한 다음, **최근 Unreal Engine 프로젝트** 옆의 햄버거 메뉴를 클릭하고 **프로젝트 찾아보기**를 선택합니다. 게임의 unreal 프로젝트 `.uproject` 파일을 엽니다.

    ![Wto시작 관리자의 Unreal 탭 스크린샷](media/wwise-unreal-tab.png)

* 그런 다음 **프로젝트에서 통합** 또는 **프로젝트에서 수정**을 클릭 합니다. 이 단계 (re)는 프로젝트 Acoustics 믹서 플러그 인을 포함 하 여 프로젝트에 Wto바이너리를 통합 합니다.

* **엔진 플러그 인:** 엔진 플러그 인으로 wto를 사용 하 고 위와 같이 더미 프로젝트를 만든 경우 배포 `[DummyUProject]\Plugins\Wwise` 된 폴더를 복사 하 여 붙여넣습니다. `[UESource]\Engine\Plugins\Wwise` `[DummyUProject]`는 비어 있지 않은 실제 C++ 프로젝트 경로 이며, `[UESource]` 이는 unreal Engine 소스가 설치 된 위치입니다. 복사가 완료 되 면 더미 프로젝트를 삭제할 수 있습니다.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. 게임에 프로젝트 Acoustics Unreal 플러그 인 추가
 
* 플러그 인 패키지의 `[UProjectDir]\Plugins\ProjectAcoustics` `UProjectDir` `.uproject` 폴더를 복사 하 고 새 폴더를 만듭니다. 여기서은 파일을 포함 하는 게임의 프로젝트 폴더입니다. `Unreal\ProjectAcoustics`
  * **엔진 플러그 인**: 엔진 플러그 인으로 Wtoas를 사용 하는 경우 Project Acoustics를 Unreal engine 플러그인으로 사용 해야 합니다. 위의 대상 디렉터리 대신을 사용 `[UESource]\Engine\Plugins\ProjectAcoustics`합니다.

* 폴더와 `Wwise` `ProjectAcoustics` 함께 폴더가 표시 되는지 확인 합니다. 위의 2 단계에서 배포한 믹서 플러그 인의 이진과 함께 Wtoplugin을 포함 합니다.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Wwise의 Unreal 플러그 인 기능 확장
* Acoustics Unreal 플러그 인 프로젝트를 사용 하려면 [다음 지침](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)에 따라 WTOUNREAL plugin API에서 추가 동작이 노출 되어야 합니다. 패치 절차를 자동화 하는 배치 파일을 포함 했습니다. 
* `Plugins\ProjectAcoustics\Resources` 내부에서 `PatchWwise.bat`를 실행합니다. 아래 예제 이미지는 AcousticsGame 샘플 프로젝트를 사용 합니다.

    ![패치를 위해 제공 된 스크립트를 강조 표시 하는 Windows 탐색기 창 스크린샷](media/patch-wwise-script.png)

* DirectX SDK를 설치하지 않은 경우 사용하는 Wwise의 버전에 따라 `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`에 `DXSDK_DIR`을 포함하는 줄을 주석으로 처리해야 할 수도 있습니다.

    ![DXSDK 주석 처리를 보여주는 코드 편집기의 스크린샷](media/directx-sdk-comment.png)

* Visual Studio 2019을 사용 하 여 컴파일하는 경우 연결 오류를 해결 하려면 `VSVersion` `vc150`의 `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` 기본값을 다음과 같이 수동으로 편집 합니다.

    ![이름이 microsoft.vc150로 변경 된 VSVersion을 보여 주는 코드 편집기의 스크린샷](media/vsversion-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. 게임 빌드 및 Python 사용 설정 확인

* 게임을 컴파일하고 올바르게 빌드되는지 확인 합니다. 그렇지 않으면 계속 진행 하기 전에 이전 단계를 신중 하 게 확인 합니다. 
* Unreal 편집기에서 프로젝트를 엽니다. 
* **엔진 플러그 인:** ProjectAcoustics를 엔진 플러그 인으로 사용 하는 경우 "기본 제공" 플러그 인 아래에 나열 되어 있는지 확인 합니다.
* 프로젝트 Acoustics가 통합 되었음을 나타내는 새 모드가 표시 됩니다.

    ![Acoustics 모드 전체를 표시 하지 않는 실제의 스크린샷](media/acoustics-mode-full.png)

* Python 플러그 인을 사용 하지 않도록 설정 했는지 확인 합니다. 편집기 통합이 제대로 작동 하려면이 기능이 필요 합니다.

    ![Unreal 편집기에서 Python 확장을 사용 하도록 설정 하는 스크린샷](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. 프로젝트 설정

예제 다운로드에는 예제 Wwise 프로젝트가 포함 되어 있습니다. 이러한 지침과 함께 확인 하는 것이 좋습니다. 아래 스크린샷은이 프로젝트에서 가져옵니다.

### <a name="bus-setup"></a>버스 설정
* 프로젝트 Acoustics Unreal 플러그 인은이 ***정확한*** 이름 `Project Acoustics Bus`으로 버스에서 연결 된 믹서 플러그 인을 찾습니다. 이 이름을 사용 하 여 새 오디오 버스를 만듭니다. 믹서 플러그 인은 다양 한 구성에서 작동할 수 있지만 지금은 반향 처리를 수행 하는 데 사용 된다고 가정 합니다. 이 버스는 Acoustics을 사용 하는 모든 원본에 대해 혼합 된 반향 신호를 전달 합니다. 업스트림을 모든 버스 믹싱 구조로 혼합할 수 있습니다. 예제는 샘플 다운로드에 포함 된 Wto샘플 프로젝트에서 가져온 것입니다.

    ![프로젝트 Acoustics Bus를 표시 하는 Wto버스의 스크린샷](media/acoustics-bus.png)

* 버스의 채널 구성은 다음 `1.0, 2.0, 4.0, 5.1 or 7.1`중 하나로 설정 해야 합니다. 다른 configs이 버스에서 출력이 발생 하지 않습니다.

    ![Project Acoustics Bus에 대 한 채널 구성 옵션의 스크린샷](media/acoustics-bus-channel-config.png)

* 이제 프로젝트 Acoustics bus 세부 정보로 이동 하 여 믹서 플러그 인 탭을 볼 수 있는지 확인 합니다.

    ![Project Acoustics Bus에 대 한 믹서 플러그 인 탭을 사용 하도록 설정 하는 방법을 보여 주는 Wwise의 스크린샷](media/mixer-tab-enable.png)

* 그런 다음 믹서 플러그 인 탭으로 이동 하 여 프로젝트 acoustics 믹서 플러그 인을 버스에 추가 합니다.

    ![Project Acoustics Mixer 플러그 인을 추가 하는 방법을 보여 주는 Wtobus 스크린샷](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>행위자-믹서 계층 설정
* 성능상의 이유로, Project Acoustics는 오디오 DSP를 모든 원본에 동시에 적용 합니다. 이렇게 하려면 플러그 인이 믹서 플러그인으로 작동 해야 합니다. 출력 버스가 일반적으로 드라이 출력 신호를 전달 하지만 wto는 출력 버스에 믹서 플러그 인을 사용 해야 합니다. Project Acoustics를 사용 하려면 버스 신호를 받은 상태에서 `Project Acoustics Bus`원음 신호를 aux로 라우팅해야 합니다. 다음 프로세스는이 신호 흐름으로의 점진적 마이그레이션을 지원 합니다.

* 전례, 무기 및 기타를 최상위 수준에 포함 하는 행위자 믹서 계층 구조를 포함 하는 기존 프로젝트가 있다고 가정해 봅니다. 각에는 해당 하는 출력 버스가 있습니다. Acoustics를 사용 하도록 전례를 마이그레이션하려는 경우를 예로 들어 보겠습니다. 먼저 전례 출력 버스의 자식인 마른 서브 믹스를 운반 하는 해당 aux bus를 만듭니다. 예를 들어, 정확한 이름이 중요 하지는 않지만 아래 이미지에서 "건조" 접두사를 사용 하 여 이러한 접두사를 구성 했습니다. 전례 bus에 대 한 모든 미터 또는 효과는 여전히 이전 처럼 작동 합니다.

    ![권장 Wto마른 조합 설정의 스크린샷](media/wwise-dry-mix-setup.png)

* 그런 다음 전례 행위자-믹서에 대 한 버스 출력 구조를 다음과 같이 수정 합니다. Project Acoustics Bus를 출력 버스로 설정 하 고 Dry_Footsteps를 사용자 정의 보조 버스로 설정 합니다.

    ![권장 Wto행위자 믹서 버스 설정의 스크린샷](media/actor-mixer-bus-settings.png)

* 이제 모든 전례가 acoustics 처리를 가져오고 프로젝트 Acoustics Bus에서 반향를 출력 합니다. 마른 신호는 일반적인 방법으로 Dry_Footsteps 및 spatialized를 통해 라우팅됩니다.

* Project Acoustics는 전 세계에 3D 위치가 있는 소리에만 적용 됩니다. 다음 단계별 [설명서](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)를 참조 하십시오. 배치 속성을 표시 된 대로 설정 해야 합니다. "3D Spatialization" 설정은 필요에 따라 "위치" 또는 "위치 + 방향" 중 하나일 수 있습니다.

    ![권장 Wto행위자 위치 설정의 스크린샷](media/wwise-positioning.png)

* 출력 버스를 업스트림을 **Project Acoustics bus** 와 혼합 하는 다른 버스로 설정 하는 것은 작동 하지 않습니다. 이 요구 사항은 믹서 플러그 인에 적용 됩니다.

* 전례 행위자 계층의 자식이 acoustics을 사용 하지 않도록 하려면 항상 "override parent"를 사용 하 여 옵트아웃 (opt out) 할 수 있습니다.

* 게임의 모든 행위자-믹서에서 반향에 대해 게임 또는 사용자 정의 보내기를 사용 하는 경우, 반향를 두 번 적용 하지 않으려면이 행위자-믹서에서 해당 기능을 해제 합니다.

### <a name="spatialization"></a>Spatialization
기본적으로 Acoustics wtomixer 플러그 인은 컨볼루션 반향를 적용 하 고, spatialization 이동 하는 것을 그대로 유지 합니다. 이 기본 반향 전용 구성에서 Project Acoustics를 사용 하는 경우, 사용자는 거의 모든 spatializer를 Project Acoustics ' 반향으로 결합 하 고 일치 시킬 수 있도록 마른 조합에서 모든 채널 구성 및 spatialization 메서드를 사용할 수 있습니다. 옵션에는 [Ambisonics 기반 binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) 및 [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)이 포함 됩니다.
 
프로젝트 Acoustics에는 개체 기반 고해상도 HRTF 렌더링 및 이동을 모두 지 원하는 선택적 spatializer이 포함 되어 있습니다. 믹서 플러그 인 설정에서 "Spatialization 수행" 확인란을 선택 하 고, HRTF 사이를 선택 하거나, 위의 사용자 정의 aux 전송 설정을 사용 하지 않도록 설정 하 여 spatializing를 두 번 사용 하지 않도록 합니다. Spatialization 모드는 소리 뱅크를 다시 생성 해야 하기 때문에 실시간으로 변경할 수 없습니다. Unreal을 다시 시작 하 고 play를 방문 하 여 ' Spatialization 수행 ' soundbanks 같은 믹서 플러그 인 구성 변경을 선택 해야 합니다.

![Wto믹서 플러그 인 Spatialization 설정의 스크린샷](media/mixer-spatial-settings.png)

불행 하 게 다른 개체 기반 spatializer 플러그 인은 믹서 플러그인으로 구현 될 때 현재 지원 될 수 없으며, 현재는 단일 행위자 믹서에 할당 된 여러 믹서 플러그 인을 허용 하지 않습니다.  

## <a name="7-audio-setup-in-unreal"></a>7. 실제 오디오에서 오디오 설정
* 먼저 게임 수준을 굽기 하 여에 `Content\Acoustics`배치 되는 acoustics 자산을 생성 해야 합니다. [Unreal 굽기 자습서](unreal-baking.md) 를 참조 하 고 여기를 다시 시작 하세요. 일부 미리 구운 수준은 샘플 패키지에 포함 되어 있습니다.
* 장면에 Acoustics Space 행위자를 만듭니다. 전체 수준의 acoustics를 나타내는 수준에서 이러한 행위자 중 하나만 만듭니다. 

    ![Acoustics Space 행위자 생성을 보여 주는 Unreal 편집기의 스크린샷](media/create-acoustics-space.png)

* 이제 Acoustics Space 행위자의 Acoustics 데이터 슬롯에 구운 음향 데이터 자산을 할당 합니다. 이제 장면에 acoustics!

    ![Acoustics 자산 할당을 보여 주는 Unreal 편집기의 스크린샷](media/acoustics-asset-assign.png)

* 이제 빈 행위자를 추가 하 고 다음을 수행 합니다.

    ![빈 행위자에서 Acoustics 구성 요소 사용을 보여 주는 Unreal 편집기의 스크린샷](media/acoustics-component-usage.png)

1. 행위자에 Acoustics Audio 구성 요소를 추가 합니다. 이 구성 요소는 Project Acoustics의 기능을 사용 하 여 wtoaudio 구성 요소를 확장 합니다.
2. 시작 시 재생 상자는 기본적으로 선택 되어 있으며,이는 수준 시작 시 연결 된 이벤트를 트리거합니다.
3. Acoustics 매개 변수 표시 확인란을 사용 하 여 소스에 대 한 화면에 있는 디버그 정보를 인쇄 합니다.  
    ![디버그 값을 사용 하는 소리 소스에서 Unreal editor Acoustics 패널의 스크린샷](media/debug-values.png)
4. 일반적인 워크플로 별 wtoevent 할당
5. 공간 오디오 사용이 꺼져 있는지 확인 합니다. 현재 특정 오디오 구성 요소에 대해 Project Acoustics를 사용 하는 경우 Acoustics에 대 한 Wto의 공간 오디오 엔진을 동시에 사용할 수 없습니다.

모든 설정을 완료했습니다. 장면 주위로 이동 하 고 음향 효과를 살펴보세요.

## <a name="next-steps"></a>다음 단계
* Acoustics 프로젝트에 대 한 [디자인](unreal-workflow.md) 자습서 (unreal/wto)
* 게임 장면에 대해 [bakes를 수행 하는 방법을 알아봅니다](unreal-baking.md) . 
