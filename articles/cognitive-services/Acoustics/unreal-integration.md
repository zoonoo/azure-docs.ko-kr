---
title: 소음 Unreal 및 Wwise 통합 프로젝트
titlesuffix: Azure Cognitive Services
description: 이 방법 설명 프로젝트 소음 Unreal 및 Wwise 플러그 인 프로젝트에 통합을 합니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 6207808efb9bee327afd2de21ffa59535acf4e55
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704803"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>소음 Unreal 및 Wwise 통합 프로젝트
이 방법은 기존 Wwise 및 Unreal 게임 프로젝트에 프로젝트 소음 플러그 인 패키지의 자세한 통합 단계를 제공합니다. 

소프트웨어 요구 사항:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 또는 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Unreal 용 Wwise 플러그 인](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Wwise SDK의 직접 통합 Wwise Unreal 플러그 인을 사용 하는 대신를 사용 하는 경우 프로젝트 소음 Unreal 플러그 인을 참조 하 고 Wwise API 호출을 조정 합니다.

프로젝트 소음 Wwise 이외의 오디오 엔진을 사용 하려는 경우에 향상 된 기능 요청을 수행 합니다 [프로젝트 소음 토론 포럼](https://github.com/microsoft/ProjectAcoustics/issues)합니다. 데이터를 쿼리하고 소음 엔진에 대 한 API 호출을 확인 한 다음 프로젝트 소음 Unreal 플러그 인을 사용할 수 있습니다.

## <a name="download-project-acoustics"></a>프로젝트 소음 다운로드
이미 않았다면 다운로드 합니다 [플러그 인 패키지를 프로젝트 소음 Unreal & Wwise](https://www.microsoft.com/download/details.aspx?id=58090)). 

패키지에는 Unreal Engine 플러그 인 및 Wwise mixer 플러그 인을 포함 했습니다. Unreal 플러그 인 편집기 및 런타임 통합을 제공합니다. 게임 플레이 하는 동안 프로젝트 소음 Unreal 플러그 인 각 프레임 폐색 각 게임 개체에 대해 같은 매개 변수를 계산합니다. 이러한 매개 변수는 Wwise API 호출으로 변환 됩니다.

## <a name="review-integration-steps"></a>통합 단계를 검토 합니다.

패키지를 설치 하 고 게임에 배포 하려면 이러한 기본 단계가 있습니다.
1. 프로젝트 소음 Wwise mixer 플러그 인 설치
2. (다시) Wwise 게임에 배포 합니다. 이 단계는 게임 프로젝트로 mixer 플러그 인을 전파합니다.
3. 게임에 프로젝트 소음 Unreal 플러그 인을 추가 합니다.
4. Wwise의 Unreal 플러그 인 기능 확장
5. 게임을 빌드 및 Python을 사용할 수 확인
6. Wwise 프로젝트가 프로젝트 소음을 사용 하도록 설정
7. Unreal에 오디오 설치

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. 프로젝트 소음 mixer 플러그 인 설치
* Wwise 시작 관리자를 연 다음, **플러그 인** 탭의 **새 플러그 인 설치**에서 **디렉터리에서 추가**를 선택합니다. 

    ![Wwise 시작 관리자에서 플러그 인을 설치 하는 스크린샷](media/wwise-install-new-plugin.png)

* 다운로드한 패키지에 포함된 `AcousticsWwisePlugin\ProjectAcoustics` 디렉터리를 선택합니다. Wwise mixer 플러그 인 번들을 포함합니다.

* Wwise는 플러그 인을 설치 합니다. 프로젝트 소음 해야 이제 Wwise 설치 된 플러그 인 목록에 나타납니다.
![프로젝트 소음 설치가 스크린 샷의 Wwise 설치 된 플러그 인 목록](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (다시) Wwise 게임에 배포
Wwise을 이미 통합 한 경우에 Wwise 게임에 다시 배포 합니다. 이 프로젝트 소음 Wwise 플러그 인을 선택합니다.

* **엔진 플러그 인:** Wwise는 Unreal의 게임 플러그 인으로 설치할 경우 C++ 프로젝트에서이 단계를 건너뜁니다. 경우에 대 한 인스턴스 Unreal 프로젝트는 mixer 플러그 인을 사용 하 여 청사진만 Wwise 배포 이므로 더 복잡 한 대신는 엔진 플러그 인으로 설치 됩니다. 더미, 빈 Unreal 만들기 C++ 프로젝트, Unreal 편집기가 열리고 경우 닫기 및 Wwise이 더미 프로젝트를 배포 하는 나머지 절차를 수행 합니다. 다음 배포 Wwise 플러그 인을 복사 합니다.
 
* Wwise 시작 관리자에서 **Unreal Engine** 탭을 선택한 다음, **최근 Unreal Engine 프로젝트** 옆의 햄버거 메뉴를 클릭하고 **프로젝트 찾아보기**를 선택합니다. 게임 Unreal 프로젝트를 열고 `.uproject` 파일입니다.

    ![스크린 샷의 Wwise 시작 관리자의 Unreal 탭](media/wwise-unreal-tab.png)

* 클릭 **프로젝트에서 통합 Wwise** 또는 **프로젝트의 수정 Wwise**합니다. (다시)이이 단계는 이제 프로젝트 소음 mixer 플러그 인을 포함 하 여 프로젝트에 Wwise 이진 파일을 통합 합니다.

* **엔진 플러그 인:** 위와 같이 더미 프로젝트를 만들 Wwise 배포 폴더를 복사 하는 Wwise 엔진 플러그 인을 사용 하는 경우: `[DummyUProject]\Plugins\Wwise` 붙여넣습니다 `[UESource]\Engine\Plugins\Wwise`합니다. `[DummyUProject]` 빈 Unreal는 C++ 프로젝트 경로 및 `[UESource]` Unreal Engine 소스 설치 해야 합니다. 완료 되 면 복사 더미 프로젝트를 삭제할 수 있습니다.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. 게임에 프로젝트 소음 Unreal 플러그 인을 추가 합니다.
 
* 복사를 `Unreal\ProjectAcoustics` 폴더에 플러그 인 새 폴더를 만들고 패키지 `[UProjectDir]\Plugins\ProjectAcoustics`여기서 `UProjectDir` 는 게임의 프로젝트 폴더를 포함 하는 `.uproject` 파일.
  * **플러그 인 엔진**: Wwise는 엔진 플러그 인을 사용 하는 경우 프로젝트 음향도는 Unreal engine 플러그 인으로 사용 해야 합니다. 위의 대상 디렉터리를 대신 사용 하 여: `[UESource]\Engine\Plugins\ProjectAcoustics`합니다.

* 확인을 `Wwise` 와 함께 폴더는 `ProjectAcoustics` 폴더입니다. 위의 2 단계에서에서 배포 수 (re-)는 mixer 플러그 인에 대 한 이진 파일 함께 Wwise 플러그 인을 포함 합니다.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Wwise의 Unreal 플러그 인 기능 확장
* 프로젝트 소음 Unreal 플러그 인 추가 동작이 필요 당 Wwise Unreal 플러그 인 API에서 공개 해서는 [이러한 지침](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)합니다. 패치 절차를 자동화할 배치 파일을 포함 했습니다. 
* `Plugins\ProjectAcoustics\Resources` 내부에서 `PatchWwise.bat`를 실행합니다. 아래 예제 이미지 AcousticsGame 샘플 프로젝트를 사용합니다.

    ![제공 된 스크립트 Wwise 패치를 Windows 탐색기의 스크린샷 창 강조 표시](media/patch-wwise-script.png)

* DirectX SDK가 설치되어 있지 않은 경우 `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`에DXSDK_DIR이 포함된 줄을 주석으로 처리해야 합니다.

    ![DXSDK 주석 처리를 보여주는 코드 편집기의 스크린샷](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. 게임을 빌드 및 Python을 사용할 수 확인

* 게임에 컴파일하고 올바르게 빌드되는지 확인 합니다. 그렇지 않은 경우 계속 하기 전에 신중 하 게 이전 단계를 확인 합니다. 
* Unreal 편집기에서 프로젝트를 엽니다. 
* **엔진 플러그 인:** ProjectAcoustics 엔진 플러그 인을 사용 하는 경우 또한 확인 활성화 되어 있는지, 플러그 인 "기본 제공" 아래에 나열 합니다.
* 프로젝트 소음 통합 않았음을 나타냅니다 이라는 새로운 모드가 표시 됩니다.

    ![스크린 샷의 Unreal 소음 모드 전체 표시](media/acoustics-mode-full.png)

* Python 플러그 인을 사용 하도록 설정 하는 Unreal에 확인 합니다. 이것이 편집기 통합이 제대로 작동 하려면 필요 합니다.

    ![Unreal 편집기에서 Python 확장을 사용 하도록 설정 하는 스크린샷](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Wwise 프로젝트 설치

예제 Wwise 프로젝트 샘플 다운로드에 포함 되어 있습니다. 다음이 지침과 함께 보면 하는 것이 좋습니다. 아래 스크린샷에서이 프로젝트에서 가져옵니다.

### <a name="bus-setup"></a>Bus 설치
* 이 사용 하 여 버스에 연결 된 mixer 플러그 인에 대 한 플러그 인을 프로젝트 소음 Unreal 보입니다 ***정확한*** 이름: `Project Acoustics Bus`합니다. 이 이름을 가진 새 오디오 버스를 만듭니다. Mixer 플러그 인 다양 한 구성을 작업할 수 있지만 지금은 처리 잔 향만 수행 하는 데 사용할 가정 합니다. 이 버스 소음을 사용 하는 모든 원본에 대 한 혼합된 반향 신호가 전달 됩니다. 업스트림 구조를 혼합 하는 모든 버스에 혼합할 수, 예는 아래에서 샘플 다운로드에 포함 된 Wwise 샘플 프로젝트에서 가져온 합니다.

    ![프로젝트 소음 버스를 보여 주는 스크린 샷의 Wwise 버스](media/acoustics-bus.png)

* 버스에서 채널 구성 중 하나로 설정 해야 하는 경우: `1.0, 2.0, 4.0, 5.1 or 7.1`합니다. 이 버스의 출력이 다른 configs 발생 합니다.

    ![프로젝트 소음 버스에 대 한 채널 구성 옵션의 스크린샷](media/acoustics-bus-channel-config.png)

* 이제 프로젝트 소음에 bus 세부 정보 및 Mixer 플러그 인 탭이 표시 되도록 이동

    ![스크린 샷의 Wwise 프로젝트 소음 버스 Mixer 플러그 인 탭을 사용 하도록 설정 하는 방법](media/mixer-tab-enable.png)

* Mixer 플러그 인 탭으로 이동 하 고 플러그 인 프로젝트 소음 mixer 버스에 추가

    ![예제의 화면의 Wwise bus 프로젝트 소음 Mixer 플러그 인을 추가 하는 방법](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>행위자 mixer 계층 설치
* 성능상의 이유로 프로젝트 소음 오디오 DSP 소스에 적용 됩니다 모든 동시에 합니다. Mixer 플러그 인으로 작동 하도록 플러그 인이 필요 합니다. Wwise 출력 bus dry 출력 신호를 일반적으로 전달 되지만 mixer 플러그 인 출력 버스에 필요 합니다. 프로젝트 소음 젖은 신호 전달 되는 동안 dry 신호 aux 버스를 통해 라우팅할 필요는 `Project Acoustics Bus`합니다. 다음 프로세스에는이 신호 흐름에 점진적으로 마이그레이션을 지원합니다.

* 예를 들어 최상위 수준에서 전례, 무기, 등을 포함 하는 행위자 mixer 계층 구조를 사용 하 여 기존 프로젝트가 있는 합니다. 각 해당 dry 조합에 대 한 해당 출력 버스를 있습니다. 소음을 사용 하는 이름을 마이그레이션하려 예를 들어 있습니다. 먼저 이름 출력 버스의 자식인 dry 믹스가 수행 하는 해당 보조 버스를 만듭니다. 예를 들어 사용 했습니다 "Dry"를 접두사 아래 이미지에서이 구성에 정확한 이름은 중요 하지 않지만. 모든 미터 또는 효과 더욱 발전 시켰습니다 버스에 사용 했던 이전 처럼 유효 합니다.

    ![권장 되는 Wwise Dry 혼합 설정의 스크린샷](media/wwise-dry-mix-setup.png)

* 다음 이름 행위자 mixer에 bus 출력 구조를 다음과 같이 수정 출력 버스 및 Dry_Footsteps로 프로젝트 소음 버스를 사용 하 여 사용자 정의 aux 버스로 설정 합니다.

    ![권장 되는 Wwise 행위자 Mixer Bus 설치 스크린샷](media/actor-mixer-bus-settings.png)

* 이제 모든 이름을 소음 처리 가져와 해당 반향 프로젝트 소음 버스를 출력 합니다. Dry 신호 Dry_Footsteps 라우팅된 이며 일반적으로 spatialized 됩니다.

* 프로젝트 소음 전 세계는 3D 위치에 있는 소리에만 적용 됩니다. 다음 [Wwise 설명서](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), 표시 된 것 처럼 위치 지정 속성을 설정할 수 있어야 합니다. 필요에 따라 "3D 공간화" 설정을 "위치" 또는 "위치 + 방향" 수 있습니다.

    ![권장 되는 Wwise 행위자 위치 설정의 스크린샷](media/wwise-positioning.png)

* 출력 버스에 업스트림 혼합 하는 몇 가지 다른 버스 설정을 **프로젝트 소음 Bus** 작동 하지 않습니다. Wwise는 mixer 플러그 인에이 요구 사항을 적용합니다.

* 소음을 사용 하지 않도록 이름 행위자 mixer 계층에서 자식을 하려는 경우 사용할 수 있습니다 항상 "부모 재정의"에 참여 하지 않음에.

* 게임 또는 사용자 정의 보냅니다 반향 게임에서 모든 행위자 mixer에 대 한를 사용 하는 경우 해당에서 해제 반향을 두 번 적용 하지 않으려면이 행위자 mixer 합니다.

### <a name="spatialization"></a>공간화
기본적으로 프로젝트 소음 Wwise mixer 플러그 인에는 나선 반향을, 터치식 이동 공간화 위해 Wwise 종료 적용 됩니다. 이 기본 반향 전용 구성에서 프로젝트 소음 사용 하는 경우 자유롭게 채널 구성과 공간화 메서드를 사용 하 여 프로그램 dry 조합에 프로젝트 소음 반향을 사용 하 여 거의 모든 입체 음향 맞게 조정할 수 있습니다. 사항이 [binaural spatializers Ambisonics 기반](https://www.audiokinetic.com/products/ambisonics-in-wwise/) 하 고 [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)합니다.
 
프로젝트 소음 고해상도 HRTF 렌더링 개체 기반 및 이동을 모두 지 원하는 선택적 입체 음향을 포함 합니다. Mixer 플러그 인 설정에 "수행 공간화" 확인란 및 HRTF 또는 이동 중에서 선택 하 고 위에 설정의 모든 반복 금지 버스에 모두 프로젝트 소음 mixer 플러그 인 및 Wwise을 두 번 spatializing 방지 하려면 사용자 정의 aux 보냅니다를 사용 하지 않도록 설정 합니다. 사운드 은행 재생성 필요 하기 때문에 실시간으로 공간화 모드를 변경할 수 없습니다. Unreal, 다시 시작 다음 soundbanks mixer 플러그 인 구성 변경 내용이 수행 공간화 확인란 같은 플레이 도달 하기 전에 다시 생성 해야 합니다.

![스크린 샷의 Wwise Mixer 플러그 인 공간화 설정](media/mixer-spatial-settings.png)

그러나 다른 입체 음향 개체 기반 플러그 인 mixer 플러그 인으로 구현 되 고 Wwise 단일 행위자-mixer에 할당 하는 여러 mixer 플러그 인 현재 허용 하지 않습니다 지금은 지원할 수 없습니다.  

## <a name="7-audio-setup-in-unreal"></a>7. Unreal에 오디오 설치
* 에 배치 됩니다 소음 자산을 생성 하는 프로그램 게임 수준 적용 해야 하는 먼저 `Content\Acoustics`합니다. 참조를 [Unreal 적용 자습서](unreal-baking.md) 하 고 여기에 다시 시작 합니다. 일부 미리 구운된 수준이 예제 패키지에 포함 됩니다.
* 장면에 소음 공간 행위자를 만듭니다. 이러한 행위자 중 하나에서 만들 수준 전체 수준의 소음을 나타냄으로. 

    ![소음 공간 행위자의 생성을 보여 주는 Unreal 스크린 샷 편집기](media/create-acoustics-space.png)

* 이제 소음 공간 행위자에 대해 소음 데이터 슬롯을 구운된 어쿠스틱 데이터 자산에 할당 합니다. 장면 소음이 되었습니다!

    ![편집기가 howing 소음 자산 배정 Unreal 스크린 샷](media/acoustics-asset-assign.png)

* 이제 빈 행위자를 추가 하 고 다음을 수행 합니다.

    ![빈 행위자에서 소음 구성 요소 사용을 보여 주는 Unreal 스크린 샷 편집기](media/acoustics-component-usage.png)

1. 행위자는 소음 오디오 구성 요소를 추가 합니다. 이 구성 요소 프로젝트 소음에 대 한 기능을 사용 하 여 Wwise 오디오 구성 요소를 확장합니다.
2. 재생 시작 상자 수준 시작 시 관련된 Wwise 이벤트를 트리거할 기본적으로 확인 됩니다.
3. 화면 인쇄를 소음 매개 변수 표시 확인란을 사용 하 여 원본에 대 한 정보를 디버그 합니다.
    ![사용 하도록 설정 하는 디버그 값을 사용 하 여 사운드 원본 편집기 소음 패널 Unreal 스크린 샷](media/debug-values.png)
4. 일반적인 Wwise 워크플로 당 Wwise 이벤트를 할당 합니다.
5. 사용 하 여 공간 오디오 꺼져 있는지 확인 합니다. 이때 프로젝트 소음 특정 오디오 구성 요소를 사용 하는 경우 사용할 수 없습니다 동시에 Wwise의 공간 오디오 엔진 소음 합니다.

모든 설정을 완료했습니다. 음향 효과 탐색 하 고 장면 이동!

## <a name="next-steps"></a>다음 단계
* [디자인](unreal-workflow.md) Unreal/Wwise에서 프로젝트 소음에 대 한 자습서
* [베이 크 작업을 수행 하는 방법을 알아봅니다](unreal-baking.md) 게임에 장면에 대 한 
