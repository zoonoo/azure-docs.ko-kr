---
title: 프로젝트 Acoustics 시작
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작 가이드는 Unity 프로젝트에서 플러그 인을 통합하고, 장면을 준비하고, 소리 원본에 음향 효과를 적용하는 방법을 보여 줍니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: a9cc8c7b4cdcc05d38bc0d68561fc9d86305b0cb
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879917"
---
# <a name="getting-started-with-project-acoustics"></a>프로젝트 Acoustics 시작
이 빠른 시작 가이드는 Unity 프로젝트에서 플러그 인을 통합하고, 장면을 준비하고, 소리 원본에 음향 효과를 적용하는 방법을 보여 줍니다. 이 빠른 시작에서는 먼저 [Azure Batch 계정](create-azure-account.md)을 만들어야 합니다. 이 가이드에서는 Unity 사용 경험이 있다고 가정합니다.

## <a name="download-the-plugin"></a>플러그 인 다운로드
[여기](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)에서 등록하여 디자이너 미리 보기에 가입합니다.

## <a name="supported-platforms-for-quickstart"></a>빠른 시작이 지원되는 플랫폼
* [Unity 2018.2+](http://www.unity3d.com)
  * 프로젝트를 **.NET 4.x Equivalent**(.NET 4.x 동급) 스크립팅 런타임 버전으로 설정해야 합니다. 
  * Windows 기반 Unity 편집기가 필요합니다.

## <a name="import-the-plugin"></a>플러그 인 가져오기
프로젝트에 음향 UnityPackage를 가져옵니다. 
* Unity에서 **Assets(자산) > Import Package(패키지 가져오기) > Custom Package...(사용자 지정 패키지...)** 로 이동합니다.

![패키지 가져오기](media/ImportPackage.png)  

* **MicrosoftAcoustics.unitypackage**를 선택합니다.

기존 프로젝트에 플러그 인을 가져오려는 경우 프로젝트의 루트에 C# 컴파일러에 대한 옵션을 지정하는 **mcs.rsp** 파일이 이미 있을 것입니다. 프로젝트 Acoustics 플러그 인과 함께 제공되는 mcs.rsp 파일에 해당 파일의 내용을 병합해야 합니다.

## <a name="enable-the-plugin"></a>플러그 인을 사용하도록 설정
음향 효과 도구 키트의 준비 부분에는 .NET 4.x 스크립팅 런타임 버전이 필요합니다. 패키지를 가져오면 Unity 플레이어 설정이 업데이트됩니다. 이 설정을 적용하려면 Unity를 다시 시작합니다.

![플레이어 설정](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>탐색 메시 만들기
표준 [Unity 워크플로](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)를 사용하여 프로젝트에 대한 탐색 메시를 만듭니다. 사용자 고유의 탐색 메시를 사용하는 방법에 대한 내용은 [준비 UI 연습](bake-ui-walkthrough.md)을 참조하세요.

## <a name="mark-static-meshes-for-acoustics"></a>정적 메시를 음향 효과용으로 표시
Unity에서 **Windows(창) > Acoustics(음향 효과)** 를 사용하여 음향 효과 창을 표시합니다. 이 창은 검사기 옆에 고정할 수 있습니다.

![Acoustics(음향 효과) 창 열기](media/WindowAcoustics.png)

Unity의 계층 구조 창에서 선택한 모든 항목을 선택 취소합니다. 음향 **Objects**(개체) 탭에서 “Acoustics Geometry(음향 기하 도형)” 확인란을 클릭하여 장면의 모든 메시 및 지형을 음향 기하 도형으로 표시합니다.

**Materials**(재질) 탭에서 장면에 사용되는 재질에 음향 재질을 할당합니다. **Default**(기본) 재질은 콘크리트와 동일한 흡수성을 갖습니다. 사용자 고유의 재질 속성을 지정하는 방법에 대한 자세한 내용은 [디자인 프로세스 페이지](design-process.md)를 참조하세요.

![Materials(재질) 탭](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>프로브 미리 보기
**Probes**(프로브) 탭에서 **Calculate**(계산)를 클릭합니다. 이 계산은 장면 크기에 따라 몇 분 정도 걸릴 수 있습니다. 계산이 완료되면 장면 보기에서 “프로브 점”이라고 하는 음향 효과 시뮬레이션 위치를 표시하는 부동 구가 표시됩니다. 장면 창에서 개체에 충분히 가까워지면 장면 입체 화상도 볼 수 있습니다. 녹색 복셀은 기하 도형으로 표시한 개체에 맞춰 정렬됩니다. 프로브 점 표시와 복셀 표시는 장면 오른쪽 위에 있는 Gizmo 메뉴에서 전환할 수 있습니다.

![GizmosPreview](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>장면 준비
**Bake**(준비) 탭에서 Azure 자격 증명을 입력한 후 **Bake**(준비)를 클릭합니다. Azure Batch 계정이 없으면 [권장 계정 설정에 대한 이 연습](create-azure-account.md)을 참조하세요.
준비가 완료되면 데이터 파일이 프로젝트의 **Assets/AcousticsData** 디렉터리에 자동으로 다운로드됩니다.

## <a name="set-up-audio-runtime-dsp"></a>오디오 런타임 DSP 설정
Unity의 Spatializer 프레임워크에서 음향 효과를 위한 오디오 DSP 런타임을 포함 하고 HRTF 기반 공간화와 통합합니다. 음향 처리를 사용하도록 설정하려면 **Edit(편집) > Project Settings(프로젝트 설정) > Audio(오디오)** 로 이동하여 **Microsoft Acoustics** Spatializer로 전환한 후 프로젝트에 대한 **Spatializer 플러그 인**으로 **Microsoft Acoustics**를 선택합니다. 또한 **DSP Buffer Size**(DSP 버퍼 크기)가 Best Performance(최고 성능)로 설정되어 있는지 확인합니다.

![프로젝트 설정](media/ProjectSettings.png)  

![프로젝트 Acoustics Spatializer](media/ChooseSpatializer.png)

오디오 믹서를 엽니다(**Window(창) > Audio Mixer(오디오 믹서)**). 1개의 그룹이 있는 1개 이상의 믹서가 있는지 확인합니다. 없는 경우 **Mixers**(믹서) 오른쪽에 있는 '+' 단추를 클릭합니다. 효과 섹션에서 채널 스트립 맨 아래를 마우스 오른쪽 단추로 클릭하고 **Microsoft Acoustics Mixer** 효과를 추가합니다. 한 번에 하나의 프로젝트 Acoustics Mixer만 지원됩니다.

![오디오 믹서](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>음향 조회 테이블 설정
프로젝트 패널의 **Microsoft Acoustics** prefab을 장면에 끌어다 놓습니다.

![Acoustics Prefab](media/AcousticsPrefab.png)

**ProjectAcoustics** 게임 개체 를 클릭하고 검사기 패널로 이동합니다. 준비 결과(**Assets/AcousticsData**의 .ACE 파일)를 Acoustics Manager 스크립트에 끌어다 놓거나 텍스트 상자 옆에 있는 원 단추를 클릭하여 해당 위치를 지정합니다.

![Acoustics Manager](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>소리 원본에 음향 효과 적용
오디오 원본을 만듭니다. AudioSource의 검사기 패널의 맨 아래에 있는 **Spatialize**(공간화) 확인란을 클릭합니다. **Spatial Blend**(공간 혼합)가 전체 3D로 설정되어 있는지 확인합니다.  

![오디오 원본](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>준비 후 디자인 적용
**Add Component**(구성 요소 추가)를 선택하고 **Scripts(스크립트) > Acoustics Adjust(음향 조정)** 를 클릭하여 스크립트 **AcousticsAdjust**를 장면의 소리 원본에 추가함으로써 추가 원본 디자인 매개 변수를 사용하도록 설정할 수 있습니다.

![AcousticsAdjust](media/AcousticsAdjust.png)

**Microsoft Acoustics Mixer**에 대한 매개 변수도 있습니다. 준비 후 디자인에 대한 자세한 내용은 [디자인 매개 변수](design-process.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [샘플 장면](sample-walkthrough.md) 시도
* 전체 [준비 기능](bake-ui-walkthrough.md) 집합에 대해 알아보기
* 자세한 [디자인 매개 변수](design-process.md)에 대해 알아보기

