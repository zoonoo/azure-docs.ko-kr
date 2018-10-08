---
title: Acoustics의 디자인 프로세스 개요 - Cognitive Services
description: 이 문서에서는 프로젝트 Acoustics 워크플로의 세 단계 각각에서 디자인 의도를 표현하는 방법을 설명합니다.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 8f594be67c4677fae00cb01598d3899e30dae1e8
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433227"
---
# <a name="design-process-overview"></a>디자인 프로세스 개요
프로젝트 Acoustics 워크플로의 세 단계인 준비 전 장면 설정, 소리 원본 배치 및 준비 후 디자인에서 디자인 의도를 표현할 수 있습니다. 이 프로세스에서는 디자이너가 장면에서 소리가 발생하는 방식을 계속 제어하지만 반향 볼륨 배치와 관련된 태그는 덜 필요합니다.

## <a name="pre-bake-design"></a>준비 전 디자인
준비 전 장면 설정 프로세스는 소리 파형 시뮬레이션에 사용되는 장면 및 메타데이터를 생성하며, 폐색, 반사 및 반향을 제공하기 위해 시뮬레이션에 참여하는 장면 요소가 선택됩니다. 장면에 대한 메타데이터는 각 장면 요소에 대한 음향 재질 선택에 해당합니다. 음향 재질은 각 표면에서 다시 반사되는 소리 에너지의 양을 제어합니다.

모든 화면에 대한 기본 흡수 계수는 높은 반사율을 나타내는 0.04입니다. 장면 전체에서 여러 다른 재질의 흡수 계수를 조정하여 미학 및 게임 플레이 효과를 얻을 수 있습니다. 이러한 효과는 수신자가 장면의 한 영역에서 다른 영역으로의 전환 효과음을 들을 때 특히 두드러지게 들립니다. 예를 들어, 어두은 반향 룸에서 밝은 비반향 실외로 전환하는 장면은 전환 효과를 높여줍니다. 이러한 효과를 달성하려면 실외 장면 재질의 흡수 계수를 더 높게 조정합니다.

룸의 지정된 재질의 반향 시간은 흡수 계수와 반비례 관계가 있으며 대부분의 재질은 흡수율 값이 0.01~0.20 범위를 갖습니다. 이 범위를 벗어나는 흡수 계수의 재질은 흡수력이 매우 뛰어납니다.

![반향 시간 그래프](media/ReverbTimeGraph.png)

[준비 UI 안내](bake-ui-walkthrough.md)에서는 준비 전 컨트롤에 대해 자세히 설명합니다.

## <a name="sound-source-placement"></a>소리 원본 배치
런타임 시 복셀 및 프로브 점을 확인하면 입체 화상으로 만들어진 기하 도형 내에서 소리 원본이 중단되는 문제를 디버그하는 데 도움이 될 수 있습니다. 복셀 그리드 표시와 프로브 점 표시 간을 전환하려면 장면 보기의 오른쪽 위에 있는 Gizmo 메뉴에서 해당 확인란을 클릭합니다. 소리 원본이 벽 복셀 내에 있는 경우 공기 복셀로 이동합니다.

![Gizmo 메뉴](media/GizmosMenu.png)  

복셀 표시는 게임의 시각적 구성 요소에 변환이 적용되었는지를 확인하는 데 도움이 될 수 있습니다. 변환이 적용된 경우 **Acoustics Manager**를 호스트하는 GameObject에도 동일한 변환을 적용합니다.

## <a name="post-bake-design"></a>준비 후 디자인
준비 결과는 장면 전체에서 모든 원본 수신기 위치 쌍에 대한 폐색 및 반향 매개 변수로 ACE 파일에 저장됩니다. 물리적으로 정확한 이 결과는 프로젝트에서 있는 그대로 사용할 수 있으며 디자인하기에 좋은 시작점이 됩니다. 준비 후 디자인 프로세스는 런타임에 준비 결과 매개 변수를 변환하기 위한 규칙을 지정합니다.

### <a name="distance-based-attenuation"></a>거리 기반 감쇠
**Microsoft Acoustics** Unity Spatializer 플러그 인이 제공하는 오디오 DSP는 Unity 편집기에 기본 제공되는 원본별 거리 기반 감쇠를 유지합니다. 거리 기반 감쇠에 대한 컨트롤은 **3D Sound Settings**(3D 소리 설정) 아래에 있는 소리 원본의 **Inspector**(검사기) 패널에 있는 **Audio Source**(오디오 원본) 구성 요소에 있습니다.

![거리 감쇠](media/distanceattenuation.png)

### <a name="tuning-scene-parameters"></a>장면 매개 변수 조정
모든 원본에 대한 매개 변수를 조정하려면 Unity의 **Audio Mixer**(오디오 믹서)에서 채널 스트립을 클릭한 후 **Acoustics Mixer**(음향 효과 믹서) 효과에서 매개 변수를 조정합니다.

![믹서 사용자 지정](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>원본 매개 변수 조정
**AcousticsSourceCustomization** 원본에 스크립트를 연결하면 해당 원본에 대한 매개 변수를 조정할 수 있습니다. 스크립트를 연결하려면 **Inspector**(검사기) 패널 맨 아래의 **Add Component**(구성 요소 추가)를 클릭하고 **Scripts(스크립트) > Acoustics Source Customization(음향 원본 사용자 지정)** 으로 이동합니다. 스크립트에는 다음 세 가지 매개 변수가 있습니다.

![원본 사용자 지정](media/SourceCustomization.png)

* **Reverb Power Adjust**(반향 파워 조정) - 반향 파워(dB)를 조정합니다. 음수 값은 소리가 좀 더 끊어지게 만들지만 양수 값은 소리를 좀 더 반향적으로 만듭니다.
* **Decay Time Scale**(감쇠 시간 배율) - 감쇠 시간에 대한 승수를 조정합니다. 예를 들어, 준비 결과가 감쇠 시간을 750밀리초로 지정한 상태에서 이 값이 1.5로 설정되면 원본에 적용되는 감쇠 시간은 1,125밀리초입니다.
* **Enable Acoustics**(음향 효과 사용) - 이 원본에 음향 효과가 적용되는지 여부를 제어합니다. 선택 취소되면 원본은 음향 효과 없이 HRTF로 공간화됩니다. 즉, 수평 및 감쇠 시간과 같은 동적 반향 매개 변수와 방해 및 폐색이 없음을 의미합니다. 반향은 고정된 수평 및 감쇠 시간을 사용하여 계속 적용됩니다.

특정 미학 또는 게임 플레이 효과에 도달하기 위해 원본마다 다른 설정이 필요할 수 있습니다. 대화는 한 가지 가능한 예입니다. 게임 플레이에서는 대화가 좀 더 지능적이어야 하지만, 사람의 귀는 음성 반향에 좀 더 익숙합니다. 반향 파워를 낮추어 대화에서 말하기 요소를 줄임으로써 이러한 측면을 고려할 수 있습니다.
