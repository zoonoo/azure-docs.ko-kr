---
title: 프로젝트 음향 언리얼 과 와이즈 통합
titlesuffix: Azure Cognitive Services
description: 이 문서에서는 프로젝트 어쿠스틱 언리얼 과 와이즈 플러그인을 프로젝트에 통합하는 것을 설명합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243054"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>프로젝트 음향 언리얼 과 와이즈 통합
이 문서에서는 프로젝트 어쿠스틱 플러그인 패키지를 기존 언리얼 및 와이즈 게임 프로젝트에 통합하는 방법에 대해 설명합니다.

소프트웨어 요구 사항:
* [언리얼 엔진](https://www.unrealengine.com/) 4.20+
* [오디오 키네틱 와이즈](https://www.audiokinetic.com/products/wwise/) 2018.1
* [언리얼을 위한 와이즈 플러그인](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Wwise 언리얼 플러그인 대신 Wwise SDK를 직접 통합하는 경우 프로젝트 어쿠스틱 언리얼 플러그인을 참조하고 Wwise API 호출을 조정하십시오.

Wwise 이외의 오디오 엔진과 함께 프로젝트 음향을 사용하려면 [프로젝트 음향 토론 포럼에서](https://github.com/microsoft/ProjectAcoustics/issues)향상된 요청을 합니다. 프로젝트 어쿠스틱 언리얼 플러그인을 사용하여 음향 데이터를 쿼리하고 엔진에 API를 호출할 수 있습니다.

## <a name="download-project-acoustics"></a>프로젝트 음향 다운로드
아직 프로젝트 [음향 언리얼 및 와이즈 플러그인 패키지를](https://www.microsoft.com/download/details.aspx?id=58090) 다운로드하지 않은 경우.

패키지에는 언리얼 엔진 플러그인과 와이즈 믹서 플러그인이 포함되어 있습니다. 언리얼 플러그인은 편집기와 런타임 통합을 제공합니다. 게임 플레이 중에 프로젝트 어쿠스틱 언리얼 플러그인은 각 프레임의 각 게임 오브젝트에 대한 오클루전과 같은 파라미터를 계산합니다. 이러한 매개 변수는 Wwise API 호출로 변환됩니다.

## <a name="integration-steps"></a>통합 단계

다음 단계에 따라 패키지를 설치하고 게임에 배포합니다.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>프로젝트 음향 믹서 플러그인 설치
1. 와이즈 런처를 엽니다. **플러그인** 탭에서 새 플러그인 **설치**아래에서 **디렉터리에서 추가를**선택합니다.

    ![와이즈 런처에 플러그인 설치](media/wwise-install-new-plugin.png)

1. 다운로드 패키지에 있는 *AcousticsWwisePlugin\프로젝트 음향* 디렉토리를 선택합니다. 그것은 Wwise 믹서 플러그인 번들을 포함합니다.

   와이즈는 플러그인을 설치합니다. 프로젝트 음향은 Wwise에 설치된 플러그인 목록에 나타나야 합니다.  
![프로젝트 음향 설치 후 Wwise 설치 플러그인 목록](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>게임에 Wwise 배포 해제
Wwise를 이미 통합한 경우에도 Wwise를 게임에 다시 배포할 수 있습니다. 이 단계는 프로젝트 음향 Wwise 플러그인을 통합합니다.

   > [!NOTE]
   > **엔진 플러그인:** 언리얼 C++ 프로젝트에서 Wwise를 게임 플러그인으로 설치한 경우 이 단계를 건너뜁니다. 예를 들어 언리얼 프로젝트가 블루프린트 전용이기 때문에 엔진 플러그인으로 대신 설치하는 경우 믹서 플러그인을 사용하는 Wwise 배포가 더 복잡합니다. 더미 빈 언리얼 C ++ 프로젝트를 만듭니다. 언리얼 에디터가 열리면 닫고 나머지 절차를 따라 Wwise를 더미 프로젝트에 배포합니다. 그런 다음 배포된 Wwise 플러그인을 복사합니다.
 
1. 와이즈 런처에서 **언리얼 엔진** 탭을 선택합니다. **최근 언리얼 엔진 프로젝트** 옆에 있는 "햄버거"(아이콘) 메뉴를 선택한 다음 프로젝트 **찾아보기를**선택합니다. 게임의 언리얼 프로젝트 *.project* 파일을 엽니다.

    ![와이즈 런처 언리얼 탭](media/wwise-unreal-tab.png)

1. **프로젝트에서 와이즈 통합을** 선택하거나 프로젝트에서 **Wwise 수정을**선택합니다. 이 단계는 프로젝트 음향 믹서 플러그인을 포함하여 프로젝트에 Wwise 바이너리를 통합합니다.

   > [!NOTE]
   > **엔진 플러그인:** Wwise를 엔진 플러그인으로 사용하고 앞에서 설명한 대로 더미 프로젝트를 만든 경우 Wwise가 배포한 폴더를 *복사합니다.* *[UESource]\엔진\플러그인\Wwise를*통해 붙여 넣기 . *[DummyUProject]는* 빈 언리얼 C++ 프로젝트 경로이며, *[UESource]는* 언리얼 엔진 소스가 설치된 곳입니다. 폴더를 복사한 후 더미 프로젝트를 삭제할 수 있습니다.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>게임에 프로젝트 음향 언리얼 플러그인 추가
 
1. 플러그인 패키지에 *언리얼\프로젝트 음향* 폴더를 복사합니다. 새 폴더 *[UProjectDir]\플러그인\프로젝트 음향을*만듭니다. *[UProjectDir]* *.uproject*

   > [!NOTE]
   > **엔진 플러그인**: Wwise를 엔진 플러그인으로 사용하는 경우 프로젝트 어쿠스틱을 언리얼 엔진 플러그인으로 사용해야 합니다. 이전에 인용한 대상 디렉터리 대신 *[UESource]\Engine\플러그인\프로젝트 음향을*사용합니다.

1. 프로젝트 음향 폴더와 함께 *Wwise* 폴더가 표시되는지 *확인합니다.* 그것은 당신이 이전에 배포 믹서 플러그인에 대한 바이너리와 함께 Wwise 플러그인이 포함되어 있습니다.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Wwise Unreal 플러그 인 기능 확장
프로젝트 어쿠스틱 언리얼 플러그인은 [이러한 지침에](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)따라 Wwise 언리얼 플러그인 API에서 노출된 추가 동작이 필요합니다. 패치 절차를 자동화하기 위한 일괄 처리 파일이 포함되어 있습니다.

* 내부 *플러그인\프로젝트음향\자원,* *패치와이즈.bat를*실행합니다. 다음 예제 이미지는 AcousticsGame 샘플 프로젝트를 사용합니다.

    ![Wwise를 패치하는 스크립트가있는 Windows 탐색기 창강조](media/patch-wwise-script.png)

* DirectX SDK가 설치되어 있지 않은 경우: 사용 중이던 Wwise 버전에 따라 `DXSDK_DIR` *AcousticsGame\플러그인\Wwise\Source\AkAudio\AkAudio.Build.cs에*포함된 줄에 주석을 달아야 할 수 있습니다.

    !['DXSDK'를 보여주는 코드 편집기는 댓글을 달았습니다.](media/directx-sdk-comment.png)

* Visual Studio 2019를 사용하여 컴파일하는 경우: Wwise와의 연결 오류를 해결하려면 `VSVersion` *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs에서* **vc150으로**수동으로 기본값을 변경합니다.

    !["VSVersion"을 표시하는 코드 편집기는 "vc150"으로 변경되었습니다.](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>게임을 빌드하고 파이썬이 활성화되어 있는지 확인

1. 게임을 컴파일하고 올바르게 빌드되었는지 확인합니다. 빌드되지 않은 경우 계속하기 전에 이전 단계를 주의 깊게 확인하십시오.

1. 언리얼 에디터에서 프로젝트를 엽니다.

    > [!NOTE]
    > **엔진 플러그인:** 당신은 엔진 플러그인으로 ProjectAcoustics를 사용하는 경우, 또한 "내장"플러그인에서 활성화되어 있는지 확인합니다.

    프로젝트 음향이 통합되었음을 나타내는 새 모드가 표시됩니다.

    ![언리얼로 가득 찬 음향 모드](media/acoustics-mode-full.png)

1. 편집기 통합이 올바르게 작동할 수 있도록 언리얼 엔진용 Python 플러그인이 활성화되어 있는지 확인합니다.

    ![언리얼 에디터의 파이썬 확장이 활성화되었습니다.](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>프로젝트 음향을 사용하도록 Wwise 프로젝트 설정

예제 Wwise 프로젝트는 샘플 다운로드에 포함되어 있습니다. 이 지침과 함께 보는 것이 좋습니다. 이 문서의 뒷화면은 이 프로젝트의 스크린샷입니다.

#### <a name="bus-setup"></a>버스 설정
프로젝트 음향 언리얼 플러그인은 정확한 이름이 `Project Acoustics Bus`있는 버스의 관련 믹서 플러그인을 찾습니다. 이 이름이 같은 새 오디오 버스를 만듭니다. 믹서 플러그인은 다양한 구성에서 작동 할 수 있습니다. 그러나 지금은 리버브 처리에만 사용된다고 가정합니다. 이 버스는 음향을 사용하는 모든 소스에 대한 혼합 리버브 신호를 전달합니다. 그것은 어떤 버스 혼합 구조로 업스트림을 혼합 할 수 있습니다. 예제는 샘플 다운로드에 포함 된 Wwise 샘플 프로젝트에서 여기에 표시 됩니다.

![프로젝트 음향 버스를 보여주는 와이즈 버스](media/acoustics-bus.png)

1. 버스의 채널 구성을 *1.0,* *2.0, 4.0,* *5.1*또는 *7.1로*설정합니다. *4.0* 다른 설정으로 인해 버스에 출력이 생성되지 않습니다.

    ![프로젝트 음향 버스에 대한 채널 구성 옵션](media/acoustics-bus-channel-config.png)

1. 프로젝트 음향 버스 세부 사항으로 이동하고 **믹서 플러그인** 탭을 볼 수 있는지 확인합니다.

    ![프로젝트 음향 버스에 대한 믹서 플러그인 탭Wwise 사용](media/mixer-tab-enable.png)

1. **믹서 플러그인** 탭으로 이동하여 버스에 프로젝트 음향 믹서 플러그인을 추가합니다.

    ![Wwise 버스에 프로젝트 음향 믹서 플러그인을 추가하는 방법의 다이어그램](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>행위자 믹서 계층 구조 설정
최상의 성능을 위해 Project Acoustics는 오디오 디지털 신호 처리를 모든 소스에 동시에 적용합니다. 그래서, 플러그인은 믹서 플러그인으로 작동해야합니다. 출력 버스는 일반적으로 건식 출력 신호를 운반하지만 와이즈는 출력 버스에믹서 플러그인이 있어야합니다. 프로젝트 음향은 건조 신호가 보조 버스를 통해 라우팅되고 젖은 신호가 `Project Acoustics Bus`에 전달되도록 요구합니다. 다음 프로세스는 이 신호 흐름에 대한 점진적 마이그레이션을 지원합니다.

*발자국,* *무기*및 기타 를 포함하는 액터 믹서 계층 구조가 있는 기존 프로젝트가 최상위 수준이라고 가정해 보입니다. 각각의 드라이 믹스에 대한 해당 출력 버스가 있습니다. 음향을 사용하기 위해 발자국을 마이그레이션한다고 가정해 보겠습니다. 먼저, 발자국 출력 버스의 자식인 드라이 서브믹스를 운반할 해당 보조 버스를 만듭니다. 예를 들어, 정확한 이름은 중요하지 않지만 다음 이미지에서 "Dry" 접두사를 사용하여 버스를 구성했습니다. 발자국 버스에 있던 미터 나 효과는 여전히 이전과 같이 작동합니다.

![권장 와이즈 드라이 믹스 설정](media/wwise-dry-mix-setup.png)

다음으로 다음과 같이 Footsteps 액터 믹서의 버스 출력 구조를 수정하고 *프로젝트 어쿠스틱 버스를* **출력 버스로**설정하고 *사용자* 정의 보조 버스로 Dry_Footsteps 설정합니다.

![권장 Wwise 액터 믹서 버스 설정](media/actor-mixer-bus-settings.png)

이제 모든 발자국은 음향 처리를 얻고 프로젝트 음향 버스에 자신의 리버브를 출력합니다. 드라이 신호는 Dry_Footsteps 통해 라우팅되고 평소와 같이 공간화됩니다.

프로젝트 음향은 전 세계에 3D 위치가 있는 사운드에만 적용됩니다. [Wwise 문서에](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)따라 위치 지정 속성을 그림과 같이 설정해야 합니다. **3D 공간화** 설정은 필요에 따라 *위치* 또는 *위치 + 방향일* 수 있습니다.

![권장 Wwise 액터 위치 설정](media/wwise-positioning.png)

출력 버스를 *프로젝트 어쿠스틱* **버스로** 업스트림으로 혼합하는 다른 버스로 설정할 수 없습니다. Wwise는 믹서 플러그인에이 요구 사항을 부과합니다.

발자국 행위자 믹서 계층구조의 자식이 음향을 사용하지 않도록 하려면 "부모 재정의"를 사용하여 옵트아웃할 수 있습니다.

게임 정의 또는 사용자 정의 송리버브를 게임내 모든 액터 믹서에서 사용하는 경우 해당 액터 믹서에서 리버브를 두 번 적용하지 않도록 해제합니다.

#### <a name="spatialization"></a>공간화
프로젝트 음향 Wwise 믹서 플러그인은 기본적으로 컨볼루션 리버브를 적용하여 Wwise가 패닝 공간화를 수행하도록 합니다. 이 기본 리버브 전용 구성에서 프로젝트 음향을 사용하는 경우 드라이 믹스에서 모든 채널 구성 및 공간화 방법을 사용할 수 있습니다. 따라서 거의 모든 공간화기를 프로젝트 음향 반향과 혼합하고 일치시킬 수 있습니다. 당신의 옵션은 [앰비소닉스 기반의 바이노럴 공간화와](https://www.audiokinetic.com/products/ambisonics-in-wwise/) [윈도우 소닉을](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)포함한다.
 
프로젝트 음향에는 오브젝트 기반 고해상도 HRTF 렌더링 및 패닝을 모두 지원하는 선택적 공간화가 포함되어 있습니다. 믹서 플러그인 설정에서 **공간화 수행** 확인란을 선택하고 *HRTF* 또는 *패닝*중에서 선택합니다. 또한 사용자 정의 보조는 프로젝트 음향 믹서 플러그인 및 Wwise에 의해 두 번 공간화를 방지하기 위해 모든 드라이 버스에 전송을 사용하지 않도록 설정합니다. 공간화 모드는 건전한 뱅크 재생이 필요하기 때문에 실시간으로 변경할 수 없습니다. 언리얼을 다시 시작한 다음 믹서 플러그인 구성 변경 사항을 통합하기 위해 재생을 선택하기 전에 사운드뱅크를 다시 생성합니다(예: **공간화 수행** 확인란 설정)

![와이즈 믹서 플러그인 공간화 설정](media/mixer-spatial-settings.png)

안타깝게도 다른 오브젝트 기반 공간화 플러그인은 현재 지원되지 않습니다. 그들은 믹서 플러그인으로 구현하고, Wwise는 하나의 액터 믹서에 할당 할 여러 믹서 플러그인을 허용하지 않습니다.  

### <a name="audio-setup-in-unreal"></a>언리얼 오디오 설정
1. 먼저 *콘텐츠\음향에*배치될 음향 자산을 생성하려면 게임 레벨을 구워야 합니다. [언리얼 베이크 튜토리얼을](unreal-baking.md)참조하십시오. 일부 미리 베이크된 수준은 샘플 패키지에 포함되어 있습니다.

1. 장면에서 음향 공간 액터를 만듭니다. 전체 레벨의 음향을 나타내기 때문에 레벨에서 이러한 액터 중 하나만 만듭니다.

    ![언리얼 에디터의 음향 공간 배우 제작](media/create-acoustics-space.png)

1. 구운 음향 데이터 자산을 음향 공간 액터의 음향 데이터 슬롯에 할당합니다. 이제 장면에 음향이 있습니다!

    ![언리얼 에디터의 음향 자산 할당](media/acoustics-asset-assign.png)

1. 빈 액터를 추가합니다. 다음과 같이 구성합니다.

    ![언리얼 에디터는 빈 액터에서 음향 구성 요소 사용을 보여줍니다.](media/acoustics-component-usage.png)

       
    <sup>1</sup> 액터에 음향 오디오 구성 요소를 추가합니다. 이 구성 요소는 Wwise 오디오 구성 요소에 프로젝트 음향 기능을 추가합니다.
        
    <sup>2</sup> **시작 시 재생** 상자가 기본적으로 선택됩니다. 이 설정은 레벨 시작 시 관련 Wwise 이벤트를 트리거합니다.</li>
         
    <sup>3</sup> **음향 표시 매개 변수** 표시 확인란을 사용하여 소스에 대한 화면 디버그 정보를 인쇄합니다.

    ![디버그 값이 활성화된 음원의 언리얼 에디터 어쿠스틱 패널](media/debug-values.png)

    <sup>4</sup> 일반적인 Wwise 워크플로에 따라 Wwise 이벤트를 할당합니다.
       
    <sup>5</sup> 공간 **오디오 사용이** 꺼져 있는지 확인합니다. 특정 오디오 구성 요소에 프로젝트 음향을 사용하는 경우, 당신은 동시에 음향Wwise의 공간 오디오 엔진을 사용할 수 없습니다.</li>
       
모든 설정을 완료했습니다. 장면을 돌아 다니며 음향 효과를 탐구하십시오!

## <a name="next-steps"></a>다음 단계
* 프로젝트 [음향 언리얼 /와이즈 디자인 자습서를](unreal-workflow.md)보십시오.
* 게임 장면에 대해 [베이킹을 수행하는 방법을](unreal-baking.md) 알아보세요.
