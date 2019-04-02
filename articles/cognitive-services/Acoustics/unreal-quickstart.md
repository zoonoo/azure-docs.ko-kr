---
title: Unreal을 사용한 Project Acoustics 빠른 시작
titlesuffix: Azure Cognitive Services
description: 샘플 콘텐츠를 사용하여 Unreal 및 Wwise에서 Project Acoustics 디자인 컨트롤을 실험하고 Windows Desktop에 배포합니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 1575c4f4a1c96a84823f76e8e98e76de3c2ace86
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313025"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Project Acoustics Unreal/Wwise 빠른 시작
이 빠른 시작에서는 Unreal Engine 및 Wwise를 위한 제공된 샘플 콘텐츠를 사용하여 Project Acoustics 디자인 컨트롤을 실험합니다.

소프트웨어 요구 사항:
* [Unreal Engine 4.21](https://www.unrealengine.com/)
* [Wwise 2018.1.6](https://www.audiokinetic.com/products/wwise/)

## <a name="download-the-sample-package"></a>샘플 패키지 다운로드
[Project Acoustics Unreal + Wwise 샘플 패키지](https://www.microsoft.com/download/details.aspx?id=58090)를 다운로드합니다. 예제 패키지에는 Unreal Engine 프로젝트, 해당 Unreal 프로젝트에 대한 Wwise 프로젝트 및 Project Acoustics Wwise 플러그 인이 포함되어 있습니다.

## <a name="set-up-the-project-acoustics-sample-project"></a>Project Acoustics 샘플 프로젝트 설정
Project Acoustics Unreal/Wwise 샘플 프로젝트를 설정하려면 먼저 Wwise에 Project Acoustics 플러그 인을 설치해야 합니다. 그런 다음, Unreal 프로젝트에 Wwise 이진 파일을 배포하고 Project Acoustics를 지원하기 위해 Wwise의 Unreal 플러그 인을 조정합니다.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Project Acoustics Wwise 플러그 인 설치
Wwise 시작 관리자를 연 다음, **플러그 인** 탭의 **새 플러그 인 설치**에서 **디렉터리에서 추가**를 선택합니다. 다운로드한 패키지에 포함된 `AcousticsWwisePlugin\ProjectAcoustics` 디렉터리를 선택합니다.

![Wwise 플러그 인 설치 옵션을 보여주는 Wwise 시작 관리자의 스크린샷](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Project Acoustics Unreal 샘플 프로젝트에 Wwise 이진 파일 추가
Wwise 시작 관리자에서 **Unreal Engine** 탭을 선택한 다음, **최근 Unreal Engine 프로젝트** 옆의 햄버거 메뉴를 클릭하고 **프로젝트 찾아보기**를 선택합니다. 패키지 `AcousticsSample\AcousticsGame\AcousticsGame.uproject`에서 샘플 Unreal 프로젝트 `.uproject` 파일을 엽니다.

![Wwise 시작 관리자 Unreal 탭의 스크린샷](media/wwise-unreal-tab.png)

그런 다음, Project Acoustics 샘플 프로젝트 옆에 있는 **프로젝트에서 Wwise 통합**을 클릭합니다.

![Acoustics Game Unreal 프로젝트를 보여주는 Wwise 시작 관리자의 스크린샷](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Wwise의 Unreal 플러그 인 기능 확장
Project Acoustics Unreal 플러그 인의 경우 Wwise Unreal 플러그 인 API에서 추가 동작이 노출되어야 합니다. 이러한 수정 작업을 자동화하려면 Project Acoustics Unreal 플러그 인과 함께 제공되는 일괄 처리 파일을 실행합니다.
* `AcousticsGame\Plugins\ProjectAcoustics\Resources` 내부에서 `PatchWwise.bat`를 실행합니다.

    ![Wwise 프로젝트를 패치하는 스크립트를 보여주는 Windows 탐색기 창의 스크린샷](media/patch-wwise-script.png)

* DirectX SDK가 설치되어 있지 않은 경우 `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`에DXSDK_DIR이 포함된 줄을 주석으로 처리해야 합니다.

    ![DXSDK 주석 처리를 보여주는 코드 편집기의 스크린샷](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Unreal 프로젝트를 엽니다. 
모듈을 다시 작성하라는 메시지가 표시되면 예를 클릭합니다.

빌드 장애 시 프로젝트를 열지 못할 경우 Project Acoustics Wwise 플러그 인이 Project Acoustics 샘플 프로젝트에 사용된 것과 동일한 버전의 Wwise에 설치되었는지 확인합니다.

## <a name="experiment-with-project-acoustics-design-controls"></a>Project Acoustics 디자인 컨트롤을 사용하여 실험
Unreal 편집기에서 재생 단추를 클릭하여 장면에 어떤 소리가 나는지 들어봅니다. 바탕 화면에서 W, A, S, D 및 마우스를 사용하여 이동합니다. 더 많은 컨트롤에 대한 키보드 바로 바기를 보려면 **F1** 키를 누릅니다. 사용해볼 수 있는 일부 디자인 활동은 다음과 같습니다.

### <a name="modify-occlusion-and-transmission"></a>폐색 및 전송 수정
각 Unreal 사운드 행위자에 원본별 Project Acoustics 디자인 컨트롤이 있습니다.

![Unreal Editor Acoustics 디자인 컨트롤의 스크린샷](media/demo-scene-sound-source-design-controls.png)

**폐색** 승수가 1보다 큰 경우(기본값이 1) 폐색이 과장됩니다. 1보다 작게 설정하면 폐색 효과는 더 감지하기 힘듭니다.

벽을 통한 전송을 사용하도록 설정하려면 **전송(dB)** 슬라이더를 최하위 수준으로 이동시킵니다. 

### <a name="modify-wetness-for-a-source"></a>원본의 습성 수정
습성이 거리에 따라 변화하는 정도를 변경하려면 **지각 거리 변형**을 사용합니다. Project Acoustics는 거리에 따라 매끄럽게 변화하고 지각 거리 단서를 제공하는 시뮬레이션에서 공간 전체의 습성 수준을 계산합니다. 거리 관련 습성 수준을 높여서 거리 변형을 높이면 이 효과가 과장됩니다. 변형 값이 1보다 작으면 거리 관련 반향 변화를 감지하기 어려워집니다. 이 효과는 **습성(dB)** 을 조정하여 세부 조정할 수도 있습니다.

**감쇠 시간 비율**을 조정하여 전체 공간의 감쇠 시간을 늘립니다. 시뮬레이션 결과가 감쇠 시간이 1.5초인 경우를 고려합니다. **감쇠 시간 비율**을 2로 설정하면 3초의 원본에 감쇠 시간이 적용됩니다.

### <a name="modify-distance-based-attenuation"></a>거리 기반 감쇠 수정
Project Acoustics Wwise Mixer 플러그 인은 Wwise에 기본 제공되는 원본별 거리 기반 감쇠를 유지합니다. 이 곡선을 변경하면 건성 경로 수준이 변경됩니다. Project Acoustics 플러그 인은 시뮬레이션 및 디자인 컨트롤에 의해 지정된 습성-건성 혼합을 유지하기 위해 습성 수준을 조정합니다.

![시뮬레이션 경계 전에 감쇠가 0으로 수렴되는 Wwise 감쇠 곡선 패널의 스크린샷](media/demo-sounds-attenuation.png)

Project Acoustics는 각 시뮬레이션된 플레이어 위치 주변 중앙에 배치되는 “시뮬레이션 지역” 상자에서 계산을 수행합니다. 샘플 패키지의 음향 효과 자산은 45m의 시뮬레이션 지역으로 베이킹되었으며, 감쇠는 45m 전에 0으로 떨어지도록 설계되었습니다. 이 대칭은 엄격한 요건은 아니지만, 수신기로부터 45m 이내의 기하 도형만 소리를 폐색시킨다는 주의 사항이 있습니다.

## <a name="next-steps"></a>다음 단계
* [Project Acoustics](unreal-integration.md) 플러그 인을 Unreal 프로젝트에 통합
* 자체 준비를 위해 [Azure 계정 만들기](create-azure-account.md)


