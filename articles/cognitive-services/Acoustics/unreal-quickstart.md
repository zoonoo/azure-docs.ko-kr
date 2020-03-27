---
title: Unreal을 사용한 Project Acoustics 빠른 시작
titlesuffix: Azure Cognitive Services
description: 샘플 콘텐츠를 사용하여 Unreal 및 Wwise에서 Project Acoustics 디자인 컨트롤을 실험하고 Windows 데스크톱에 배포합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "72242913"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Project Acoustics Unreal/Wwise 빠른 시작
이 빠른 시작에서는 Unreal Engine 및 Wwise에 대한 샘플 콘텐츠를 사용하여 Project Acoustics 디자인 컨트롤을 실험합니다.

샘플 콘텐츠를 사용하기 위한 소프트웨어 요구 사항:
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>샘플 패키지 다운로드
[Project Acoustics Unreal 및 Wwise 샘플 패키지](https://www.microsoft.com/download/details.aspx?id=58090)를 다운로드합니다. 샘플 패키지에는 다음이 포함됩니다.
- Unreal Engine 프로젝트
- Unreal 프로젝트에 대한 Wwise 프로젝트
- Project Acoustics Wwise 플러그 인

## <a name="set-up-the-project-acoustics-sample-project"></a>Project Acoustics 샘플 프로젝트 설정
먼저 Project Acoustics 플러그 인을 Wwise에 설치합니다. 다음으로, Wwise 이진 파일을 Unreal 프로젝트에 배포합니다. 그런 다음, Project Acoustics를 지원하도록 Wwise Unreal 플러그 인을 수정합니다.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Project Acoustics Wwise 플러그 인 설치
Wwise Launcher를 엽니다. **Plugins**(플러그 인) 탭의 **Install New Plug-ins**(새 플러그 인 설치)에서 **Add from directory**(디렉터리에서 추가)를 선택합니다. 다운로드한 패키지에 포함된 *AcousticsWwisePlugin\ProjectAcoustics* 디렉터리를 선택합니다.

![Wwise Launcher에 Wwise 플러그 인을 설치하는 옵션](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Project Acoustics Unreal 샘플 프로젝트에 Wwise 이진 파일 추가
1. Wwise Launcher에서 **Unreal Engine** 탭을 선택합니다. 
1. **Recent Unreal Engine Projects** 옆에 있는 "햄버거"(아이콘) 메뉴를 선택한 다음, **Browse for project**(프로젝트 찾아보기)를 선택합니다. *AcousticsSample\AcousticsGame\AcousticsGame.uproject* 패키지에서 샘플 Unreal 프로젝트 *.uproject* 파일을 엽니다.

   ![Wwise Launcher의 Unreal 탭](media/wwise-unreal-tab.png)

3. Project Acoustics 샘플 프로젝트 옆에 있는 **Integrate Wwise in Project**(프로젝트에서 Wwise 통합)을 클릭합니다.

   ![Wwise Launcher에 통합 옵션이 강조 표시된 Acoustics Game Unreal 프로젝트가 보입니다.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Wwise Unreal 플러그 인 기능 확장
Project Acoustics Unreal 플러그 인에는 Wwise Unreal 플러그 인 API에서 노출된 추가 동작이 필요합니다. Project Acoustics Unreal 플러그 인과 함께 제공된 일괄 처리 파일을 실행하여 이러한 수정을 자동화합니다.
* *AcousticsGame\Plugins\ProjectAcoustics\Resources* 내에서 *PatchWwise.bat*를 실행합니다.

    ![Windows 탐색기 창에 강조 표시되어 있는 Wwise 프로젝트를 패치하는 스크립트](media/patch-wwise-script.png)

* DirectX SDK가 설치되어 있지 않으면, 사용하는 Wwise의 버전에 따라 *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*에 `DXSDK_DIR`을 포함하는 줄을 주석으로 처리해야 할 수도 있습니다.

    ![주석 처리된 "DXSDK"를 보여주는 코드 편집기](media/directx-sdk-comment.png)

* Visual Studio 2019를 사용하여 컴파일 하는 경우: Wwise와의 연결 오류를 해결하려면 *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*에서 기본값 *VSVersion*을 **vc150**으로 수동으로 변경합니다.

    ![VSVersion이 "vc150"으로 변경된 코드 편집기](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Unreal 프로젝트 열기 
Unreal 프로젝트를 열면 모듈을 다시 빌드하라는 메시지가 표시됩니다. **예**를 선택합니다.

빌드 장애로 인해 프로젝트를 열지 못할 경우 Project Acoustics Wwise 플러그 인이 Project Acoustics 샘플 프로젝트에 사용된 것과 동일한 버전의 Wwise에 설치되었는지 확인합니다.

2019.1보다 오래된 [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 버전을 사용하는 경우에는 Project Acoustics 샘플 프로젝트를 사용하여 사운드 뱅크를 생성할 수 없습니다. Wwise 버전 2019.1을 샘플 프로젝트에 통합해야 합니다.

## <a name="experiment-with-project-acoustics-design-controls"></a>Project Acoustics 디자인 컨트롤을 사용하여 실험
Unreal 편집기에서 재생 단추를 선택하여 장면에 어떤 소리가 나는지 들어봅니다. W, A, S, D 키와 마우스를 사용하여 이동합니다. 추가 컨트롤에 대한 키보드 바로 바기를 보려면 F1 키를 선택합니다.

다음 정보는 시도해 볼 몇 가지 디자인 활동을 설명합니다.

### <a name="modify-occlusion-and-transmission"></a>폐색 및 전송 수정
각 Unreal 사운드 액터에는 원본별 Project Acoustics 디자인 컨트롤이 있습니다.

![Unreal Editor Acoustics 디자인 컨트롤](media/demo-scene-sound-source-design-controls.png)

**Occlusion**(폐색) 승수가 1보다 큰 경우(기본값이 1) 폐색이 과장됩니다. 1보다 작게 설정하면 폐색 효과가 더 미묘해집니다.

벽을 통한 전송을 사용하도록 설정하려면 **Transmission(dB)** 슬라이더를 최저 수준에서 멀리 이동합니다.

### <a name="modify-wetness-for-a-source"></a>원본의 습성 수정
습한 정도가 거리에 따라 변화하는 정도를 변경하려면 **Perceptual Distance Warp**(지각 거리 왜곡)을 사용합니다. Project Acoustics는 시뮬레이션을 통해 공간 전체의 습한 수준을 계산합니다. 이 수준은 거리에 따라 고르게 달라지며 지각 거리 신호를 제공합니다. 이 효과를 과장하려면 거리 관련 습한 수준을 높여서 거리 왜곡을 늘립니다. 왜곡 값이 1 미만이면 거리 기반 반향 변화가 더 미묘해집니다. 이러한 효과는 **Wetness(dB)** (습한 정도) 설정을 통해 보다 미세하게 조정할 수 있습니다.

공간 전체에서 감쇠 시간을 늘리려면 **Decay Time Scale**(감쇠 시간 비율)을 조정합니다. 시뮬레이션 결과의 감쇠 시간이 1.5초인 경우를 고려합니다. **Decay Time Scale**(감쇠 시간 비율)을 2로 설정하면 감쇠 시간 3초가 원본에 적용됩니다.

### <a name="modify-distance-based-attenuation"></a>거리 기반 감쇠 수정
Project Acoustics Wwise 믹서 플러그 인은 Wwise에 기본 제공되는 원본별 거리 기반 감쇠를 유지합니다. 이 곡선을 변경하면 건조 경로 수준이 변경됩니다. Project Acoustics 플러그 인은 시뮬레이션 및 디자인 컨트롤에 의해 지정된 습윤/건조 믹스를 유지하기 위해 습한 수준을 조정합니다.

![시뮬레이션 경계 전에 감쇠가 0으로 진행되는 것을 보여주는 Wwise 감쇠 곡선 패널](media/demo-sounds-attenuation.png)

Project Acoustics는 시뮬레이션된 각 플레이어 위치를 중심으로 하는 "시뮬레이션 영역" 상자에서 계산을 수행합니다. 샘플 패키지의 음향 자산은 시뮬레이션 영역 반경이 45m로 베이킹되었습니다. 감쇠는 45미터 전에 0으로 떨어지도록 설계되었습니다. 이러한 감소가 엄격한 요구 사항은 아니지만 리스너로부터 45미터 내에 있는 기하 도형만 사운드를 차단한다는 주의 사항이 있습니다.

## <a name="next-steps"></a>다음 단계
* [Project Acoustics 플러그 인을 Unreal 프로젝트에 통합](unreal-integration.md)합니다.
* 자체 베이크를 위해 [Azure 계정을 만듭니다](create-azure-account.md).
