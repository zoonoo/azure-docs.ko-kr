---
title: Unity를 사용한 Project Acoustics 빠른 시작
titlesuffix: Azure Cognitive Services
description: 샘플 콘텐츠를 사용하여 Unity에서 Project Acoustics 디자인 컨트롤을 실험하고 Windows Desktop에 배포합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "72243013"
---
# <a name="project-acoustics-unity-quickstart"></a>Project Acoustics Unity 빠른 시작
Unity용 Project Acoustics 샘플 콘텐츠를 사용하여 시뮬레이션 기반 디자인 컨트롤을 실험합니다.

소프트웨어 요구 사항:
* Windows용 [Unity 2018.2+](https://unity3d.com)
* [Project Acoustics 샘플 콘텐츠 패키지](https://www.microsoft.com/download/details.aspx?id=57346)

샘플 패키지에는 무엇이 포함되어 있나요?
* 기하 도형, 사운드 원본 및 게임 플레이 컨트롤을 사용한 Unity 장면
* Project Acoustics 플러그 인
* 장면에 대한 베이킹된 음향 효과 자산

## <a name="import-the-sample-package"></a>샘플 패키지 가져오기
새 Unity 프로젝트에 샘플 패키지를 가져옵니다.
1. Unity에서 **자산** > **패키지 가져오기** > **사용자 지정 패키지**로 이동합니다.

    ![Unity 패키지 가져오기 옵션](media/import-package.png)  

1. **ProjectAcoustics.unitypackage**를 선택합니다.

1. **가져오기** 단추를 선택하여 Unity 패키지를 프로젝트에 통합합니다.  
  
    ![Unity 패키지 가져오기 대화 상자](media/import-dialog.png)  

기존 프로젝트에 패키지를 가져오려면 [Unity 통합](unity-integration.md)에서 추가 단계 및 정보를 참조하세요.

>[!NOTE]
>가져오기가 완료되면 콘솔 로그에 몇 가지 오류 메시지가 표시됩니다. 다음 단계를 진행하고 Unity를 다시 시작합니다.

## <a name="restart-unity"></a>Unity 다시 시작
음향 효과 도구 키트의 준비 부분에는 .NET 4.*x* 스크립팅 런타임 버전이 필요합니다. 패키지를 가져오면 Unity 플레이어 설정이 업데이트됩니다. 이 설정을 적용하려면 Unity를 다시 시작합니다. 설정이 적용되었는지 확인하려면 **플레이어** 설정을 엽니다.

![Unity 프로젝트 설정 메뉴](media/player-settings.png)  

![.NET 4.x가 선택된 Unity 플레이어 설정 패널](media/net45.png)  

>[!NOTE]
>이 스크린샷은 Unity 2018.*x*에서 가져온 것입니다. 최신 버전의 Unity에서는 이미지가 다를 수 있습니다.

## <a name="open-the-project-acoustics-bake-window"></a>Project Acoustics 음향 효과 베이킹 창 열기
Unity의 **Window**(창) 메뉴에서 **Acoustics**(음향 효과)를 선택합니다.

![Window 메뉴에서 Acoustics 옵션이 강조 표시된 Unity 편집기](media/window-acoustics.png)

이동식 **Acoustics**(음향 효과) 창이 열립니다. 이 창에서는 음향 효과 시뮬레이션의 속성을 설정합니다.

![음향 효과 창이 열려 있는 Unity 편집기](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>디자인 컨트롤로 실험
*ProjectAcousticsSample* 폴더에서 샘플 장면을 열고 Unity 편집기에서 재생 단추를 선택합니다. W, A, S, D 키와 마우스를 사용하여 이동합니다. 장면에 음향이 어떻게 포함 또는 포함되지 않는지 비교하려면 R 키를 선택하고 오버레이 텍스트가 빨간색으로 변하고 "음향: 사용 안 함"이라고 말합니다. 더 많은 컨트롤에 대한 키보드 바로 바기를 보려면 F1 키를 선택합니다. 또한 마우스 오른쪽 단추를 클릭하여 작업을 선택한 다음, 마우스 왼쪽 단추를 클릭하여 해당 작업을 수행할 수도 있습니다.

*AcousticsAdjust* 스크립트는 샘플 장면의 사운드 소스에 연결됩니다. 이를 통해 원본별 디자인 매개 변수를 사용할 수 있습니다.

![Unity AcousticsAdjust 스크립트](media/acoustics-adjust.png)

다음 섹션에서는 사용 가능한 컨트롤을 사용하여 만들 수 있는 몇 가지 효과를 살펴봅니다. 각 컨트롤에 대한 자세한 정보는 [Project Acoustics Unity 디자인 자습서](unity-workflow.md)를 참조하세요.

### <a name="modify-distance-based-attenuation"></a>거리 기반 감쇠 수정
**Project Acoustics** Unity Spatializer 플러그 인의 오디오 디지털 신호 처리는 Unity 편집기에 기본 제공되는 원본별 거리 기반 감쇠를 유지합니다. 거리 기반 감쇠에 대한 컨트롤은 **3D Sound Settings**(3D 소리 설정) 아래에 있는 소리 원본의 **Inspector**(검사기) 패널에 있는 **Audio Source**(오디오 원본) 구성 요소에 있습니다.

![Unity 거리 감쇠 옵션 패널](media/distance-attenuation.png)

Project Acoustics는 플레이어 위치에 따라 중앙에 배치되는 “시뮬레이션 지역” 상자에서 계산을 수행합니다. 샘플 패키지의 음향 자산은 플레이어 주변에 있는 45m의 시뮬레이션 영역 크기에서 베이킹한 것입니다. 따라서 소리 감쇠는 약 45m에서 0으로 떨어지도록 설계되어야 합니다.

### <a name="modify-occlusion-and-transmission"></a>폐색 및 전송 수정
* **Occlusion**(폐색) 승수가 1보다 큰 경우(기본값이 1) 폐색이 과장됩니다. 폐색 효과를 더 감지하기 힘들게 하려면 1보다 작게 설정합니다.

* 벽을 통한 전송을 사용하도록 설정하려면 **전송(dB)** 슬라이더를 최하위 수준의 반대쪽으로 이동시킵니다.

### <a name="modify-wetness-for-a-source"></a>원본의 습성 수정
* 습한 정도가 거리에 따라 변화하는 정도를 변경하려면 **Perceptual Distance Warp**(지각 거리 왜곡)을 사용합니다. Project Acoustics는 시뮬레이션을 통해 지각 거리 단서를 제공하고 거리에 따라 매끄럽게 변화하는 습성 수준을 계산합니다. 거리 관련 습성 수준을 높여서 거리 변형을 높이면 이 효과가 과장됩니다. 왜곡 값이 1 미만이면 거리 기반 반향 변화가 더 미묘해집니다.

   이러한 효과를 보다 세밀하게 조정하려면 **Wetness(dB)** (습도(dB)) 설정을 변경합니다.

* 전체 공간의 감쇠 시간을 늘리려면 **Decay Time Scale**(감쇠 시간 비율)을 조정합니다. 특정 원본 수신기 위치 쌍에 대한 시뮬레이션 결과가 1.5초의 감쇠 시간이고 **Decay Time Scale**(감쇠 시간 비율)이 2로 설정된 경우 원본에 적용된 감쇠 시간은 3초입니다.

## <a name="next-steps"></a>다음 단계
* [Unity 기반 Project Acoustics 디자인 컨트롤](unity-workflow.md)에 대한 세부 정보를 읽어봅니다.
* [디자인 프로세스](design-process.md)의 기본 개념을 더 살펴봅니다.
* [Azure 계정을 만들어](create-azure-account.md) 사전 베이킹 및 베이킹 프로세스를 살펴봅니다.
