---
title: Project Acoustics Unreal 디자인 자습서
titlesuffix: Azure Cognitive Services
description: 이 자습서에서는 Unreal 및 Wwise의 Project Acoustics 디자인 워크플로에 대해 설명합니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 57bde67ac2259b3847f59f95eaefba9c6fddf13e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316204"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Project Acoustics Unreal/Wwise 디자인 자습서
이 자습서에서는 Unreal 및 Wwise의 Project Acoustics 디자인 설정 및 워크플로에 대해 설명합니다.

소프트웨어 필수 구성 요소:
* Project Acoustics Wwise 및 Unreal 플러그 인이 포함된 Unreal 프로젝트

Project Acoustics를 사용하여 Unreal 프로젝트를 가져오려면 다음을 수행해야 합니다.
* [Project Acoustics Unreal 통합](unreal-integration.md) 지침에 따라 Project Acoustics를 Unreal 프로젝트에 추가합니다.
* 또는 [Project Acoustics 프로젝트 샘플](unreal-quickstart.md)을 사용합니다.

## <a name="setup-project-wide-wwise-properties"></a>프로젝트 수준의 Wwise 속성 설정
Wwise에는 Project Acoustics 플러그 인이 Wwise 오디오 DSP를 구동하는 방식에 영향을 주는 글로벌 장애 및 폐색 곡선이 있습니다.

### <a name="design-wwise-occlusion-curves"></a>Wwise 폐색 곡선 디자인
Project Acoustics가 활성 상태이면 Wwise에서 설정한 폐색 볼륨, LPF(저역 필터) 및 HPF(고역 필터) 곡선에 반응합니다. 폐색 값이 100인 경우 볼륨 곡선 유형을 -100dB 값의 선형으로 설정하는 것이 좋습니다.

이 설정을 사용하면 Project Acoustics 시뮬레이션에서 -18dB의 폐색을 계산하는 경우 아래 곡선의 X=18에 입력하고 해당 Y 값이 적용된 감쇠가 됩니다. 절반 폐색을 수행하려면 엔드포인트를 -100dB 대신 -50dB로 설정하거나 -200dB로 설정하여 폐색을 과장합니다. 게임에 가장 적합한 곡선을 조정하고 미세 튜닝할 수 있습니다.
 
![Wwise 폐색 곡선 편집기의 스크린샷](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Wwise 장애 곡선 비활성화
Wwise 장애 곡선은 건성 수준(dry level)에 영향을 미치지만, Project Acoustics는 디자인 컨트롤과 시뮬레이션을 사용하여 습/건 비율(wet/dry ratio)을 적용합니다. 장애 볼륨 곡선을 사용하지 않도록 설정하는 것이 좋습니다. 습성을 설계하려면 나중에 설명하는 Wetness Adjust(습성 조정) 컨트롤을 사용합니다.
 
장애 LPF/HPF 곡선을 다른 용도로 사용하는 경우 X=0, Y=0으로 설정했는지 확인합니다. 즉 장애가 없는 경우 LPF 또는 HPF가 없습니다.

![Wwise 장애 곡선 편집기의 스크린샷](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Project Acoustics 믹서 매개 변수 디자인
Project Acoustics Bus(Project Acoustics 버스)의 믹서 플러그 인 탭을 방문하여 글로벌 반향 속성을 제어할 수 있습니다. "Project Acoustics Mixer(Custom)"(Project Acoustics 믹서(사용자 지정))를 두 번 클릭하여 믹서 플러그 인의 설정 패널을 엽니다.

또한 믹서 플러그 인에 "Perform Spatialization"(공간화 수행) 옵션이 있음을 확인할 수 있습니다. Project Acoustic의 기본 제공 공간화를 사용하려면 "Perform Spatialization"(공간화 수행) 확인란을 선택하고 HRTF 또는 Paning(패닝) 중에서 선택합니다. 설정한 Dry Aux(건성 보조) 버스를 사용하지 않도록 설정해야 합니다. 그렇지 않으면 직접 경로가 두 번 들립니다. "Wetness Adjust(습성 조정)" 및 "Reverb Time Scale Factor(반향 시간 배율)"를 사용하여 반향 믹스를 글로벌로 제어합니다. 'Perform Spatialization(공간화 수행)' 확인란과 같은 믹서 플러그 인 구성 변경 내용을 선택하려면 Unreal을 다시 시작한 다음, 재생하기 전에 soundbank를 다시 생성해야 합니다.

![Project Acoustics Wwise 믹서 플러그 인 옵션의 스크린샷](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Wwise 액터-믹서 계층 구조에서 Project Acoustics 디자인 컨트롤을 설정합니다.
개별 액터-믹서의 매개 변수를 제어하려면 Actor-Mixer(액터-믹서)를 두 번 클릭한 다음, 해당 믹서 플러그 인 탭을 클릭합니다. 여기서는 소리별 수준에서 모든 매개 변수를 변경할 수 있습니다. 이러한 값은 Unreal 쪽에서 설정한 값과 결합됩니다(아래 설명 참조). 예를 들어 Project Acoustics Unreal 플러그 인에서 개체에 대한 Outdoorness Adjustment(실외 상태 조정)를 0.5로 설정하고 Wwise에서 -0.25로 설정하면 해당 소리에 적용된 실외 상태 조정은 0.25입니다.

![Wwise 액터-믹서 계층 구조의 사운드 믹서별 설정 스크린샷](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>보조 버스의 건성 전송과 출력 버스의 습성 전송이 있는지 확인합니다.
필수 액터-믹서 설정은 Wwise에서 일반적인 건성 및 습성 라우팅을 상호 교환합니다. 액터-믹서의 출력 버스(Project Acoustics Bus로 설정됨)에서 대한 반향 신호와 사용자 정의 보조 버스를 따라 건성 신호를 생성합니다. 이 라우팅은 Project Acoustics Wwise 플러그 인에서 사용하는 Wwise 믹서 플러그 인 API의 기능으로 인해 필요합니다.

![Project Acoustics에 대한 음성 디자인 지침을 보여주는 Wwise 편집기의 스크린샷](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>거리 감쇠 곡선 설정
Project Acoustics를 사용하는 액터-믹서에서 사용되는 감쇠 곡선에 "출력 버스 볼륨"으로 설정된 사용자 정의 보조 전송이 있는지 확인합니다. Wwise에서 이 작업은 기본적으로 새로 만든 감쇠 곡선에 대해 수행됩니다. 기존 프로젝트를 마이그레이션하는 경우 곡선 설정을 확인합니다. 

기본적으로 Project Acoustics 시뮬레이션에는 플레이어 위치 주변으로 45m의 반경이 있습니다. 일반적으로 감쇠 곡선은 해당 거리 주변에서 -200dB로 설정하는 것이 좋습니다. 이 거리는 가혹한 제약 조건이 아닙니다. 무기와 같은 일부 소리의 경우 더 큰 반경이 필요할 수도 있습니다. 이러한 경우 플레이어 위치에서 45m 이내의 기하 도형만 참여한다는 점에 주의해야 합니다. 플레이어가 방에 있고 소리 원본이 방 밖에서 100m 떨어진 곳에 있으면 제대로 폐색됩니다. 원본이 방에 있고 플레이어가 방 밖에서 100m 떨어진 곳에 있으면 제대로 폐색되지 않습니다.

![Wwise 감쇠 곡선의 스크린샷](media/atten-curve.png)

## <a name="set-up-scene-wide-project-acoustics-properties"></a>장면 전체 Project Acoustics 속성 설정

Acoustics Space(음향 공간) 액터는 시스템의 동작을 수정하고 디버깅에 유용한 많은 컨트롤을 표시합니다.

![Unreal 음향 공간 컨트롤의 스크린샷](media/acoustics-space-controls.png)

* **Acoustics Data(음향 데이터):** Content/Acoustics 디렉터리에서 베이킹된 음향 자산을 할당해야 합니다. Project Acoustics 플러그 인에서 Content/Acoustics 디렉터리를 프로젝트에서 패키징된 디렉터리에 자동으로 추가합니다.
* **Tile Size(타일 크기):** 음향 데이터를 RAM에 로드하려는 수신기 주변 영역의 범위입니다. 플레이어 바로 주변의 수신기 프로브가 로드되는 한 결과는 모든 프로브에 대한 음향 데이터의 로드와 동일합니다. 타일이 클수록 RAM이 더 많이 사용되지만 디스크 I/O는 줄어듭니다.
* **Auto Stream(자동 스트림):** 사용하도록 설정하면 수신기가 로드된 영역의 가장자리에 도달할 때 새 타일에 자동으로 로드됩니다. 사용하지 않도록 설정하면 코드 또는 청사진을 통해 새 타일을 수동으로 로드해야 합니다.
* **Cache Scale(캐시 크기 조정):** 음향 쿼리에 사용되는 캐시의 크기를 제어합니다. 캐시가 작을수록 RAM이 더 적게 사용되지만 각 쿼리에 대한 CPU 사용량이 늘어날 수 있습니다.
* **Acoustics Enabled(음향 효과 사용):** 음향 시뮬레이션을 빠르게 A/B 전환할 수 있게 하는 디버그 컨트롤입니다. 이 컨트롤은 전달 구성에서 무시됩니다. 특정 오디오 버그가 음향 계산 또는 Wwise 프로젝트의 다른 문제에서 비롯된 것인지를 확인하는 데 유용한 컨트롤입니다.
* **Update Distances(거리 업데이트):** 거리 쿼리에 사전 베이킹된 음향 정보를 사용하려면 이 옵션을 사용합니다. 이러한 쿼리는 광선 발사(ray cast)와 비슷하지만, 미리 계산되어 있으므로 CPU를 훨씬 적게 사용합니다. 예를 들어 수신기에 가장 가까운 표면에서 분리된 반향에 사용하는 것입니다. 이를 완전히 활용하려면 코드 또는 Blueprints(청사진)를 사용하여 거리를 쿼리해야 합니다.
* **Draw Stats(통계 그리기):** UE의 `stat Acoustics`에서 CPU 정보를 제공할 수 있지만, 이 상태 디스플레이에서는 화면의 왼쪽 위에 현재 로드된 맵, RAM 사용량 및 기타 상태 정보가 표시됩니다.
* **Draw Voxels(복셀 그리기):** 런타임 보간 중에 사용된 복셀 그리드를 표시하는 수신기 근처에 복셀을 오버레이합니다. 방출기가 런타임 복셀 안에 있으면 음향 쿼리가 실패합니다.
* **Draw Probes(프로브 그리기):** 이 장면에 대한 모든 프로브를 표시합니다. 로드 상태에 따라 색이 달라집니다.
* **Draw Distances(거리 그리기):** Update Distances(거리 업데이트)를 사용하도록 설정하면 수신기 주변의 정량화된 방향으로 수신기와 가장 가까운 표면에 상자가 표시됩니다.

## <a name="actor-specific-acoustics-design-controls"></a>액터별 음향 컨트롤 디자인
이러한 디자인 컨트롤에는 Unreal의 개별 오디오 구성 요소에 대한 범위가 지정됩니다.

![Unreal 오디오 구성 요소 컨트롤의 스크린샷](media/audio-component-controls.png)

* **cclusion Multiplier(폐색 승수):** 폐색 효과를 제어합니다. 값이 1보다 크면 폐색을 증폭시킵니다. 값이 1보다 작으면 폐색을 최소화합니다.
* **Wetness Adjustment(습성 조정):** 추가 반향 dB
* **Decay Time Multiplier(감쇠 시간 승수):** 음향 시뮬레이션의 출력에 따라 RT60을 배가 방식으로 제어합니다.
* **Outdoorness Adjustment(실외 상태 조정):** 반향의 실외 상태를 제어합니다. 값이 0에 가까우면 실내에 더 가깝고, 1에 가까우면 실외에 더 가깝습니다. 이 조정은 부가적이므로 -1로 설정하면 실내에 적용되고 +1로 설정하면 실외에 적용됩니다.
* **Transmission Db(전송 Db):** 가시 거리 기반의 거리 감쇠와 결합된 이 음 크기를 사용하여 벽을 통과하는 추가 소리를 렌더링합니다.
* **Wet Ratio Distance Warp(습성 비율 거리 뒤틀림):** 직접 경로에 영향을 주지 않고 원본의 반향 특성을 더 가깝거나 더 멀리 있는 것처럼 조정합니다.
* **Show Acoustic Parameters(음향 매개 변수 표시):** 디버그 정보를 게임 내 구성 요소의 위쪽에 직접 표시합니다(전달 구성이 아닌 경우에만 해당).

## <a name="blueprint-functionality"></a>청사진 기능
Acoustics Space(음향 공간) 액터는 청사진을 통해 액세스할 수 있으며, 수준 스크립팅을 통해 맵을 로드하거나 설정을 수정하는 것과 같은 기능을 제공합니다. 여기서 제공하는 두 가지 예는 다음과 같습니다.

### <a name="add-finer-grained-control-over-streaming-load"></a>스트리밍 로드보다 더 세분화된 제어 추가
플레이어 위치에 따라 자동으로 스트림하는 대신 음향 데이터 스트리밍을 직접 관리하려면 Force Load Tile(타일 강제 로드) 청사진 기능을 사용할 수 있습니다.

![Unreal의 Blueprint 스트리밍 옵션 스크린샷](media/blueprint-streaming.png)

* **Target(대상):** AcousticsSpace 액터
* **Center Position(가운데 위치):** 데이터가 로드되어야 하는 영역의 중심
* **Unload Probes Outside Tile(타일 외부 프로브 언로드):** 이 옵션을 선택하면 새 영역에 없는 모든 프로브가 RAM에서 언로드됩니다. 이 옵션을 선택하지 않으면 새 영역이 메모리에 로드되고 기존 프로브도 메모리에 로드됩니다.
* **Block on Completion(완료 시 차단):** 타일 로드를 동기화 방식으로 수행합니다.

타일 크기는 Force Load Tile(타일 강제 로드)을 호출하기 전에 이미 설정되어 있어야 합니다. 예를 들어 ACE 파일을 로드하고, 타일 크기를 설정하고, 한 영역에서 스트림하려면 다음과 같이 수행할 수 있습니다.

![Unreal의 스트리밍 설정 옵션 스크린샷](media/streaming-setup.png)

### <a name="optionally-query-for-surface-proximity"></a>필요에 따라 표면 근접성 쿼리
수신기 주변의 특정 방향에서 표면에 대한 근접성을 확인하려면 Query Distance(거리 쿼리) 기능을 사용할 수 있습니다. 이 기능은 방향 지연 반사 구동 및 표면 근접성으로 구동되는 다른 게임 논리에 유용할 수 있습니다. 이 쿼리는 음향 조회 테이블에서 결과를 가져오므로 광선 발사(ray cast)보다 비용이 적게 듭니다.

![Blueprint 거리 쿼리 예제의 스크린샷](media/distance-query.png)

* **Target(대상):** AcousticsSpace 액터
* **Look Direction(보기 방향):** 수신기를 중심으로 쿼리할 방향입니다.
* **Distance(거리):** 쿼리에 성공하는 경우 가장 가까운 표면까지의 거리입니다.
* **Return Value(반환 값):** 쿼리에 성공하는 경우 true 부울 값이며, 그렇지 않은 경우 false입니다.

## <a name="next-steps"></a>다음 단계
* [디자인 프로세스](design-process.md)의 기본 개념을 살펴봅니다.
* [Azure 계정을 만들어](create-azure-account.md) 사용자 고유의 장면을 베이킹합니다.


