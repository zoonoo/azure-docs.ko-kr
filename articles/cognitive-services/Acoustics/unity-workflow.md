---
title: Project Acoustics Unity 디자인 자습서
titlesuffix: Azure Cognitive Services
description: 이 자습서에서는 Unity의 Project Acoustics 디자인 워크플로에 대해 설명합니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 01783aa12f586f61583b1503c796f9b523770104
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310628"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Project Acoustics Unity 디자인 자습서
이 자습서에서는 Unity의 Project Acoustics 디자인 도구 및 워크플로에 대해 설명합니다.

필수 조건:
* Windows용 Unity 2018.2 이상
* 베이킹한 음향 효과 자산을 사용한 Unity 장면

이 자습서에서는 두 가지 방법으로 베이킹한 음향 효과 자산을 사용하여 Unity 장면을 가져올 수 있습니다.
* [Unity 프로젝트에 Project Acoustics를 추가](unity-integration.md)한 다음, [Azure Batch 계정을 가져온](create-azure-account.md) 다음, [Unity 장면을 베이킹](unity-baking.md)합니다.
* 또는 [Project Acoustics Unity 샘플 콘텐츠](unity-quickstart.md)를 사용합니다.

## <a name="review-design-process-concepts"></a>디자인 프로세스 개념 검토
Project Acoustics는 공통 오디오 DSP(디지털 신호 처리) 방법을 사용하여 원본을 처리하며, 폐색, 습성/건성 혼합 및 반향 테일 길이(RT60)를 비롯한 친숙한 음향 효과를 제어할 수 있습니다. 하지만 핵심 [Project Acoustics 디자인 프로세스 개념](design-process.md)은 이러한 속성을 직접 설정하는 대신 시뮬레이션 결과를 사용하여 이러한 속성을 구동하는 방법을 제어한다는 것입니다. 각 컨트롤에 대한 기본 설정은 물리적으로 정확한 음향 효과를 나타냅니다.

## <a name="design-acoustics-for-each-source"></a>각 원본에 대한 디자인 음향 효과
Project Acoustics는 다양한 원본 관련 음향 효과 디자인 컨트롤을 제공합니다. 이 기능을 사용하면 일부 원본을 강조하고 다른 원본은 덜 강조하면서 장면에서 혼합을 제어할 수 있습니다.

### <a name="adjust-distance-based-attenuation"></a>거리 기반 감쇠 조정
**Project Acoustics** Unity Spatializer 플러그 인이 제공하는 오디오 DSP는 Unity 편집기에 기본 제공되는 원본별 거리 기반 감쇠를 유지합니다. 거리 기반 감쇠에 대한 컨트롤은 **3D Sound Settings**(3D 소리 설정) 아래에 있는 소리 원본의 **Inspector**(검사기) 패널에 있는 **Audio Source**(오디오 원본) 구성 요소에 있습니다.

![Unity 거리 감쇠 옵션 패널의 스크린샷](media/distance-attenuation.png)

Acoustics는 플레이어 위치에 따라 중앙에 배치되는 “시뮬레이션 지역” 상자에서 계산을 수행합니다. 소리 원본이 이 시뮬레이션 지역 외부에 있는 플레이어에서 멀리 떨어진 경우 해당 상자 내의 기하 도형만 소리 전파에 영향을 미치며(예: 폐색 유발), Occluder가 플레이어 근처에 있을 때 소리 전파 효과가 좋습니다. 그러나 플레이어가 열린 공간에 있지만 Occluder가 멀리 있는 소리 원본 가까이에 있을 경우 소리가 비현실적으로 잘 들릴 수 있습니다. 이러한 경우 Microsoft에서 제안하는 해결 방법은 플레이어에서 상자 가장자리까지의 기본 수평 거리인 약 45m 위치에서 소리 감쇠가 0이 되도록 조정하는 것입니다.

![Unity SpeakerMode 옵션 패널의 스크린샷](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>폐색 및 전송 조정
**AcousticsAdjust** 원본에 스크립트를 연결하면 해당 원본에 대한 매개 변수를 조정할 수 있습니다. 스크립트를 연결하려면 **Inspector**(검사기) 패널 맨 아래의 **Add Component**(구성 요소 추가)를 클릭하고 **Scripts(스크립트) > Acoustics Adjust(음향 조정)** 로 이동합니다. 이 스크립트에는 다음 6개의 컨트롤이 있습니다.

![Unity AcousticsAdjust 스크립트의 스크린샷](media/acoustics-adjust.png)

* **Enable Acoustics**(음향 효과 사용) - 이 원본에 음향 효과가 적용되는지 여부를 제어합니다. 선택을 취소하면 원본이 HRTF 또는 이동을 통해 입체화되지만 음향 효과는 사용되지 않습니다. 즉, 레벨 및 감소 시간과 같은 장애, 폐색 또는 동적 반향 매개 변수가 사용되지 않습니다. 반향은 고정된 수평 및 감쇠 시간을 사용하여 계속 적용됩니다.
* **폐색** - 음향 시스템에서 계산되는 폐색 dB 수준에 승수를 적용합니다. 이 승수가 1보다 큰 경우 폐색이 과장되고, 1보다 작으면 폐색 효과를 느끼기 어려우며, 이 값이 0이면 폐색이 사용되지 않도록 설정됩니다.
* **전송(dB)** - 기하 도형을 통한 전송으로 인해 야기되는 감쇠(dB)를 설정합니다. 전송을 사용하지 않도록 설정하려면 이 슬라이더를 가장 낮은 수준으로 설정합니다. Acoustics는 장면 기하 도형 주위에 도달할 경우 초기의 건성 오디오를 공간화합니다. 전송은 가시 방향으로 공간화된 추가적인 건성 도착을 제공합니다. 원본에 대한 거리 감쇠 곡선도 적용됩니다.

### <a name="adjust-reverberation"></a>반향 조정
* **습성(dB)** - 원본에서의 거리에 따라 반향력(dB)을 조정합니다. 음수 값은 소리가 좀 더 끊어지게 만들지만 양수 값은 소리를 좀 더 반향적으로 만듭니다. 곡선 컨트롤(녹색 선)을 클릭하여 곡선 편집기를 표시합니다. 점을 클릭하여 추가한 후 끌어 원하는 함수를 만드는 방식으로 곡선을 수정합니다. X축은 원본에서의 거리이며, Y축은 반향 조정 크기(dB)입니다. 곡선 편집에 대한 자세한 내용은 [Unity 설명서](https://docs.unity3d.com/Manual/EditingCurves.html)를 참조하세요. 곡선을 기본값으로 다시 설정하려면 **Wetness**(습성)를 마우스 오른쪽 단추로 클릭하고 **Reset**(재설정)을 선택합니다.
* **Decay Time Scale**(감쇠 시간 배율) - 감쇠 시간에 대한 승수를 조정합니다. 예를 들어, 준비 결과가 감쇠 시간을 750밀리초로 지정한 상태에서 이 값이 1.5로 설정되면 원본에 적용되는 감쇠 시간은 1,125밀리초입니다.
* **Outdoorness**(야외성) - 원본에 대한 반향이 발생하는 위치의 “야외성”에 대한 음향 시스템 예측값을 추가로 조정합니다. 이 값을 1로 설정하면 소리가 항상 완전히 야외에서 나오며, -1로 설정하면 소리가 완전히 실내에서 나옵니다.

**AcousticsAdjustExperimental** 스크립트를 원본에 연결하면 해당 원본의 매개 변수를 실험적으로 좀 더 조정해볼 수 있습니다. 스크립트를 연결하려면 **Inspector**(검사기) 패널 맨 아래의 **Add Component**(구성 요소 추가)를 클릭하고 **Scripts(스크립트) > Acoustics Adjust Experimental(실험적 음향 조정)** 로 이동합니다. 현재 다음과 같은 1개의 실험적 컨트롤이 있습니다.

![Unity AcousticsAdjustExperimental 스크립트의 스크린샷](media/acoustics-adjust-experimental.png)

* **Perceptual Distance Warp**(지각 거리 변형) - 건성-습성 비율을 계산하는 데 사용되는 거리에 지수 변형을 적용합니다. 음향 시스템은 거리에 따라 매끄럽게 변화하고 지각 거리 단서를 제공하는 공간 전체의 습성 수준을 계산합니다. 변형 값이 1보다 크면 거리 관련 반향 수준이 늘어나고 소리가 "멀리서" 들리므로 이 효과가 과장됩니다. 반면 변형 값이 1보다 작으면 거리 관련 반향 변화를 느끼기 어려워지고 소리가 좀 더 "가깝게" 들립니다.

## <a name="design-acoustics-for-all-sources"></a>모든 원본에 대한 디자인 음향 효과
모든 원본의 매개 변수를 조정하려면 Unity의 **Audio Mixer**에서 채널 스트립을 클릭한 후 **Project Acoustics Mixer** 효과에서 매개 변수를 조정합니다.

![Project Acoustics Unity Mixer 사용자 지정 패널의 스크린샷](media/mixer-parameters.png)

* **습도 조정** - 원본 수신기 거리를 기준으로 하여 장면의 모든 원본에서 반향력(dB)을 조정합니다. 음수 값은 소리가 좀 더 끊어지게 만들지만 양수 값은 소리를 좀 더 반향적으로 만듭니다.
* **RT60 배율 조정** - 반향 시간의 증가 배율 조정기입니다.
* **이동 사용** - 오디오가 양이(0)로 출력되는지 아니면 다중 채널 이동(1)으로 출력되는지를 제어합니다. 1이 아닌 모든 값은 양이 출력을 나타냅니다. 양이 출력은 HRTF를 통해 헤드폰용으로 입체화되고 다중 채널 출력은 VBAP를 통해 다중 채널 서라운드 스피커 시스템용으로 입체화됩니다. 다중 채널 패너를 사용하는 경우 **프로젝트 설정** > **오디오**에서 확인 가능한 디바이스 설정과 일치하는 스피커 모드를 선택해야 합니다.

## <a name="check-proper-sound-source-placement"></a>적절한 소리 원본 배치 확인
점유된 복셀 내부에 배치된 소리 원본은 음향 효과 처리를 받지 못합니다. 복셀은 가시적인 장면 기하 도형을 지나 확장되기 때문에 원본을 복셀 내부에 배치할 수는 있지만, 시각적 기하 도형에 의해 폐색되지 않은 것처럼 보입니다. **장면** 보기의 오른쪽 상단에 있는 **Gizmos** 메뉴의 복셀 그리드 확인란을 전환하여 Project Acoustics 복셀을 볼 수 있습니다.

![Unity Gizmo 메뉴의 스크린샷](media/gizmos-menu.png)  

또한 복셀 표시는 게임의 시각적 구성 요소에 변환이 적용되었는지를 확인하는 데 도움이 될 수 있습니다. 변환이 적용된 경우 **Acoustics Manager**를 호스트하는 GameObject에도 동일한 변환을 적용합니다.

### <a name="bake-time-vs-run-time-voxels"></a>베이킹 시간 및 런타임 복셀 비교
게임 디자인 시간에 편집기 창에서, 런타임 시 게임 창에서 복셀을 볼 수 있습니다. 복셀의 크기는 이러한 보기에서 다릅니다. 이는 음향 효과 런타임 보간법이 더 미세한 복셀 그리드를 사용하여 보간 결과를 더 매끄럽게 하기 때문입니다. 소리 원본 배치는 런타임 복셀을 사용하여 확인해야 합니다.

디자인 타임 복셀:

![디자인 타임 동안 Project Acoustics 복셀의 스크린샷](media/voxels-design-time.png)

런타임 복셀:

![런타임 동안 Project Acoustics 복셀의 스크린샷](media/voxels-runtime.png)

## <a name="next-steps"></a>다음 단계
* [디자인 프로세스](design-process.md)의 개념을 강조 표시하는 사례 연구 살펴보기

