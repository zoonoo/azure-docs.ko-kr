---
title: Project Acoustics Unreal 및 Wtointegration
titlesuffix: Azure Cognitive Services
description: 이 문서에서는 프로젝트 Acoustics Unreal 및 wto플러그 인을 프로젝트에 통합 하는 방법을 설명 합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243054"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project Acoustics Unreal 및 Wtointegration
이 문서에서는 Project Acoustics 플러그 인 패키지를 기존 Unreal 및 Wtogame 프로젝트에 통합 하는 방법을 설명 합니다.

소프트웨어 요구 사항:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic wwise](https://www.audiokinetic.com/products/wwise/)
* [Unreal의 wto플러그 인](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Wtounreal 플러그 인 대신 WTOSDK의 직접 통합을 사용 하는 경우 프로젝트 Acoustics Unreal 플러그 인을 참조 하 고 WTOAPI 호출을 조정 합니다.

Wto이외의 오디오 엔진과 함께 Project Acoustics를 사용 하려면 [Project Acoustics 토론 포럼](https://github.com/microsoft/ProjectAcoustics/issues)에서 향상 된 요청을 수행 합니다. Acoustics Unreal 플러그 인 프로젝트를 사용 하 여 Acoustics 데이터를 쿼리하고 엔진에 대 한 API 호출을 수행할 수 있습니다.

## <a name="download-project-acoustics"></a>프로젝트 다운로드 Acoustics
[Project Acoustics Unreal 및 wto플러그 인 패키지](https://www.microsoft.com/download/details.aspx?id=58090) 를 아직 다운로드 하지 않은 경우 다운로드 합니다.

Unreal Engine 플러그 인 및 Wto믹서 플러그 인이 패키지에 포함 됩니다. Unreal 플러그 인은 편집기 및 런타임 통합을 제공 합니다. 게임을 진행 하는 동안 프로젝트 Acoustics Unreal 플러그 인은 각 프레임에 대 한 각 게임 개체의 폐색와 같은 매개 변수를 계산 합니다. 이러한 매개 변수는 Wtoapi 호출로 변환 됩니다.

## <a name="integration-steps"></a>통합 단계

패키지를 설치 하 고 게임에 배포 하려면 다음 단계를 수행 합니다.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Project Acoustics 믹서 플러그 인을 설치 합니다.
1. Wto시작 관리자를 엽니다. **플러그** 인 탭의 **새 플러그 인 설치**에서 **디렉터리에서 추가**를 선택 합니다.

    ![Wto시작 관리자에 플러그 인 설치](media/wwise-install-new-plugin.png)

1. 다운로드 패키지에 있는 *AcousticsWwisePlugin\ProjectAcoustics* 디렉터리를 선택 합니다. 여기에는 Wto믹서 플러그 인 번들이 포함 됩니다.

   플러그 인을 설치 합니다. 프로젝트 Acoustics 설치 된 플러그 인 목록에 표시 되어야 합니다.  
![Project Acoustics 설치 후에 설치 된 플러그 인 목록](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>게임에 배포 취소
이미 통합 한 경우에도 게임에 다시 배포 합니다. 이 단계에서는 Acoustics wto플러그 인 프로젝트를 통합 합니다.

   > [!NOTE]
   > **엔진 플러그 인:** Unreal C++ 프로젝트에 게임 플러그 인을 설치한 경우이 단계를 건너뜁니다. 예를 들어, Unreal 프로젝트가 청사진 으로만 사용 되기 때문에 엔진 플러그 인으로 설치 된 경우에는 믹서 플러그 인을 사용한 Wwise 배포가 더 복잡 합니다. 빈 빈 비 실제 C++ 프로젝트를 만듭니다. 열려 있지 않은 경우에는 Unreal 편집기를 닫고 나머지 절차에 따라 더미 프로젝트를 배포 합니다. 그런 다음 배포 된 모든 플러그 인을 복사 합니다.
 
1. Wto시작 관리자에서 **Unreal Engine** 탭을 선택 합니다. **최근 Unreal Engine 프로젝트** 옆에 있는 "햄버거" (아이콘) 메뉴를 선택 하 고 **프로젝트 찾아보기**를 선택 합니다. 게임의 Unreal project *. 프로젝트* 파일을 엽니다.

    ![Wto시작 관리자 탭](media/wwise-unreal-tab.png)

1. 프로젝트에서 **통합** 또는 **프로젝트의 수정을**선택 합니다. 이 단계에서는 프로젝트 Acoustics 믹서 플러그 인을 포함 하 여 프로젝트에 Wto바이너리를 통합 합니다.

   > [!NOTE]
   > **엔진 플러그 인:** 엔진 플러그 인으로 Wtos를 사용 하 고 앞에서 설명한 대로 더미 프로젝트를 만든 경우에는 배포 된 폴더를 복사 합니다. *[DummyUProject] \Plugins\wiss*. *[Uesource] \Engine\Plugins\Wwise*에 붙여넣습니다. *[DummyUProject]* 는 빈 비 실제 C++ 프로젝트 경로 이며 *[Uesource]* 는 unreal Engine 소스가 설치 되는 위치입니다. 폴더를 복사한 후 더미 프로젝트를 삭제할 수 있습니다.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>게임에 프로젝트 Acoustics Unreal 플러그 인 추가
 
1. 플러그 인 패키지의 *Unreal\ProjectAcoustics* 폴더를 복사 합니다. 새 폴더 *[Uprojectdir] \Plugins\ProjectAcoustics*을 만듭니다. 여기서 *[uprojectdir]* 은 *uproject* 파일을 포함 하는 게임의 프로젝트 폴더입니다.

   > [!NOTE]
   > **엔진 플러그**인: 엔진 플러그 인으로 Wtoas를 사용 하는 경우 Project Acoustics를 Unreal engine 플러그 인으로 사용 해야 합니다. 이전에 언급 한 대상 디렉터리 대신 *[uesource]* 을 사용 합니다.

1. *ProjectAcoustics* 폴더와 함께 *wtofolder* 가 표시 되는지 확인 합니다. 여기에는 이전에 배포한 믹서 플러그 인의 이진과 함께 Wto플러그 인이 포함 됩니다.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>실제 플러그 인 기능 확장
Acoustics Unreal 플러그 인 프로젝트에는 [이러한 지침](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)에 따라 Wtounreal 플러그 인 API에서 노출 되는 추가 동작이 필요 합니다. 패치 절차를 자동화 하는 배치 파일을 포함 했습니다.

* *Plugins\ProjectAcoustics\Resources*내에서 *PatchWwise*를 실행 합니다. 다음 예제 이미지에서는 AcousticsGame 샘플 프로젝트를 사용 합니다.

    ![강조 표시 된 패치 스크립트가 포함 된 Windows 탐색기 창](media/patch-wwise-script.png)

* DirectX SDK가 설치 되어 있지 않으면 다음을 수행 합니다. 사용 중인 AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs의 버전에 따라,에서-0을 @no__t 포함 하는 줄을 주석으로 처리 해야 할 수 있습니다.

    ![' DXSDK '이 주석으로 표시 된 코드 편집기](media/directx-sdk-comment.png)

* Visual Studio 2019을 사용 하 여 컴파일하는 경우: 연결 오류를 해결 하려면 *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* 의 기본 `VSVersion` 값을 **이름이 microsoft.vc150**로 수동으로 변경 합니다.

    !["VSVersion"이 "이름이 microsoft.vc150"로 변경 된 코드 편집기](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>게임을 빌드하고 Python이 사용 하도록 설정 되어 있는지 확인 합니다.

1. 게임을 컴파일하고 올바르게 빌드되는지 확인 합니다. 빌드 되지 않은 경우 계속 진행 하기 전에 이전 단계를 신중 하 게 확인 합니다.

1. Unreal 편집기에서 프로젝트를 엽니다.

    > [!NOTE]
    > **엔진 플러그 인:** ProjectAcoustics를 엔진 플러그 인으로 사용 하는 경우 "기본 제공" 플러그 인에서 사용할 수 있는지도 확인 합니다.

    프로젝트 Acoustics 통합 되었음을 나타내는 새 모드가 표시 됩니다.

    ![Acoustics 모드가 완전 하지 않음](media/acoustics-mode-full.png)

1. 편집기 통합이 제대로 작동 하도록 Unreal의 Python 플러그 인을 사용 하도록 설정 했는지 확인 합니다.

    ![진짜 편집기에서 Python 확장을 사용 하도록 설정](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Project Acoustics를 사용 하도록 Wtoproject 설정

예제 Wtoproject는 샘플 다운로드에 포함 되어 있습니다. 이러한 지침과 함께 확인 하는 것이 좋습니다. 이 문서의 뒷부분에 나오는 스크린샷는이 프로젝트에서 가져온 것입니다.

#### <a name="bus-setup"></a>버스 설정
프로젝트 Acoustics Unreal 플러그 인은 정확한 이름이 `Project Acoustics Bus` 인 버스에서 연결 된 믹서 플러그 인을 찾습니다. 동일한 이름을 가진 새 오디오 버스를 만듭니다. 믹서 플러그 인은 다양 한 구성에서 작동할 수 있습니다. 그러나 지금은 반향 처리에만 사용 된다고 가정 합니다. 이 버스는 Acoustics을 사용 하는 모든 원본에 대해 혼합 된 반향 신호를 전달 합니다. 업스트림을 버스 믹싱 구조체로 혼합할 수 있습니다. 예제는 샘플 다운로드에 포함 된 Wtosample 프로젝트에서 볼 수 있습니다.

![프로젝트 Acoustics Bus를 보여 주는 Wtobus](media/acoustics-bus.png)

1. 버스의 채널 구성을 *1.0*, *2.0*, *4.0*, *5.1*또는 *7.1*로 설정 합니다. 다른 설정으로 인해 버스에 출력이 나타나지 않습니다.

    ![Project Acoustics Bus에 대 한 채널 구성 옵션](media/acoustics-bus-channel-config.png)

1. Project Acoustics Bus 세부 정보로 이동 하 여 **믹서 플러그** 인 탭을 볼 수 있는지 확인 합니다.

    ![Project Acoustics Bus에 대 한 믹서 플러그 인 탭 사용](media/mixer-tab-enable.png)

1. **믹서 플러그** 인 탭으로 이동 하 여 프로젝트 acoustics 믹서 플러그 인을 버스에 추가 합니다.

    ![Wtobus에 Project Acoustics 믹서 플러그 인을 추가 하는 방법에 대 한 다이어그램](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>행위자-믹서 계층 설정
최상의 성능을 위해 Project Acoustics는 모든 원본에 오디오 디지털 신호 처리를 동시에 적용 합니다. 따라서 플러그 인은 믹서 플러그 인으로 작동 해야 합니다. 출력 버스가 일반적으로 드라이 출력 신호를 전달 하지만 wto는 출력 버스에 믹서 플러그 인을 사용 해야 합니다. Project Acoustics를 사용 하려면는는 aux 버스를 통해 마른 신호를 라우팅해야 하는 반면,는 `Project Acoustics Bus`에 전달 됩니다. 다음 프로세스는이 신호 흐름으로의 점진적 마이그레이션을 지원 합니다.

*전례*, *무기*및 기타를 최상위 수준에 포함 하는 행위자 믹서 계층 구조를 포함 하는 기존 프로젝트가 있다고 가정해 봅니다. 각에는 해당 하는 출력 버스가 포함 되어 있습니다. Acoustics를 사용 하도록 전례를 마이그레이션하 려 한다고 가정해 보겠습니다. 먼저 전례 출력 버스의 자식인 마른 서브 믹스를 운반 하는 해당 aux bus를 만듭니다. 예를 들어, 정확한 이름이 중요 하지는 않지만 다음 이미지에서 "건조" 접두사를 사용 하 여 버스를 구성 했습니다. 전례 bus에 대 한 모든 미터 또는 효과는 여전히 이전 처럼 작동 합니다.

![권장 Wto마른 조합 설정](media/wwise-dry-mix-setup.png)

그런 다음, 전례 *Acoustics bus* 를 **출력 버스로**설정 하 고 *Dry_Footsteps* 를 사용자 정의 보조 버스로 설정 하 여 다음과 같이 행위자-믹서의 bus 출력 구조를 수정 합니다.

![권장 Wto행위자 믹서 버스 설정](media/actor-mixer-bus-settings.png)

이제 모든 전례가 acoustics 처리를 가져오고 프로젝트 Acoustics Bus에서 반향를 출력 합니다. 마른 신호는 일반적인 방법으로 Dry_Footsteps 및 spatialized를 통해 라우팅됩니다.

Project Acoustics는 전 세계에 3D 위치가 있는 소리에만 적용 됩니다. 다음 단계별 [설명서](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)를 참조 하십시오. 배치 속성을 표시 된 대로 설정 해야 합니다. **3D Spatialization** 설정은 필요에 따라 *위치* 또는 *위치 + 방향* 중 하나를 사용할 수 있습니다.

![권장 Wto행위자 위치 설정](media/wwise-positioning.png)

업스트림을 *Project Acoustics bus*로 혼합 하는 다른 버스에는 **출력 버스** 를 설정할 수 없습니다. 이 요구 사항은 믹서 플러그 인에 적용 됩니다.

전례 행위자-믹서 계층의 자식에서 acoustics를 사용 하지 않도록 하려면 해당 자식에 대해 "override parent"를 사용 하 여 옵트아웃 (opt out) 할 수 있습니다.

게임에 정의 된 또는 사용자 정의를 사용 하는 경우에는 게임의 행위자 믹서에서 반향에 대해 게임 정의 또는 사용자 정의 보내기를 사용 하 여 반향를 두 번 적용 하지 않도록 합니다.

#### <a name="spatialization"></a>Spatialization
Project Acoustics wto혼합기 플러그 인은 기본적으로 컨볼루션 반향을 적용 하 여 spatialization 이동 하는 작업을 수행 합니다. 이 기본 반향 전용 구성에서 Project Acoustics를 사용 하는 경우 마른 조합에서 모든 채널 구성 및 spatialization 메서드를 사용할 수 있습니다. 따라서 거의 모든 spatializer를 프로젝트 Acoustics 잔과 혼합 하 고 일치 시킬 수 있습니다. 옵션에는 [Ambisonics 기반 binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) 및 [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)이 포함 됩니다.
 
프로젝트 Acoustics에는 개체 기반 고해상도 HRTF 렌더링 및 이동을 모두 지 원하는 선택적 spatializer이 포함 되어 있습니다. 믹서 플러그 인 설정에서 **Spatialization 수행** 확인란을 선택 하 고 *Hrtf* 또는 *패닝*중 하나를 선택 합니다. 또한 프로젝트 Acoustics 믹서 플러그 인 및 Wwise를 사용 하 여 spatializing를 두 번 사용 하지 않도록 모든 마른 버스에 대 한 사용자 정의 보조 송신을 사용 하지 않도록 설정 합니다. Spatialization 모드는 소리 뱅크를 다시 생성 해야 하기 때문에 실시간으로 변경할 수 없습니다. Unreal을 다시 시작 하 고 재생을 선택 하 여 믹서 플러그 인 구성 변경 (예: **Spatialization 수행** 확인란 설정)을 통합 하기 전에 soundbanks을 다시 생성 합니다.

![Wto믹서 플러그 인 Spatialization 설정](media/mixer-spatial-settings.png)

그러나 다른 개체 기반 spatializer 플러그 인은 현재 지원 되지 않습니다. 이러한 기능은 믹서 플러그 인으로 구현 되며, 단일 행위자 믹서에 여러 믹서 플러그 인을 할당 하는 것을 허용 하지 않습니다.  

### <a name="audio-setup-in-unreal"></a>실제 오디오에서 오디오 설정
1. 먼저 *Content\Acoustics*에 배치 되는 acoustics 자산을 생성 하기 위해 게임 수준을 굽기 해야 합니다. [Unreal 굽기 자습서](unreal-baking.md)를 참조 하세요. 일부 미리 구운 수준은 샘플 패키지에 포함 되어 있습니다.

1. 장면에 Acoustics space 행위자를 만듭니다. 이러한 행위자는 전체 수준의 acoustics를 나타내므로 한 수준 에서만 만듭니다.

    ![Unreal 편집기에서 Acoustics space 행위자 만들기](media/create-acoustics-space.png)

1. Acoustics space 행위자의 Acoustics 데이터 슬롯에 구운 음향 데이터 자산을 할당 합니다. 이제 장면에 acoustics!

    ![Unreal 편집기에서 자산 할당 Acoustics](media/acoustics-asset-assign.png)

1. 빈 행위자를 추가 합니다. 다음과 같이 구성 합니다.

    ![Unreal 편집기는 빈 행위자에 Acoustics 구성 요소 사용을 보여 줍니다.](media/acoustics-component-usage.png)

       
    <sup>1</sup> 행위자에 Acoustics audio 구성 요소를 추가 합니다. 이 구성 요소는 Project Acoustics 기능을 Wtoaudio 구성 요소에 추가 합니다.
        
    <sup>2</sup> **시작 시 재생** 상자는 기본적으로 선택 됩니다. 이 설정은 수준 시작 시 연결 된 이벤트를 트리거합니다.</li>
         
    <sup>3</sup> **Acoustics 매개 변수 표시** 확인란을 사용 하 여 소스에 대 한 화면에 있는 디버그 정보를 인쇄 합니다.

    ![디버그 값을 사용 하는 소리 소스에서 Unreal editor Acoustics 패널](media/debug-values.png)

    <sup>4</sup> 일반적인 워크플로 당 wtoevent를 할당 합니다.
       
    <sup>5</sup> **공간 오디오 사용** 이 꺼져 있는지 확인 합니다. 특정 오디오 구성 요소에 Project Acoustics를 사용 하는 경우 Acoustics에 대 한 Wto의 공간 오디오 엔진을 동시에 사용할 수 없습니다.</li>
       
모든 설정을 완료했습니다. 장면 주위로 이동 하 고 음향 효과를 살펴보세요.

## <a name="next-steps"></a>다음 단계
* [Project Acoustics Unreal/Wtodesign 자습서](unreal-workflow.md)를 사용해 보세요.
* 게임 장면에 대해 [bakes를 수행 하는 방법을](unreal-baking.md) 알아봅니다.
