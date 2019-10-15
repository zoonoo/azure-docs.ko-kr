---
title: Project Acoustics Unity 음향 효과 베이킹 자습서
titlesuffix: Azure Cognitive Services
description: 이 자습서에서는 Unity에서 Project Acoustics를 사용하는 음향 효과 베이킹에 대해 설명합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243117"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Project Acoustics Unity 음향 효과 베이킹 자습서
이 자습서에서는 Unity에서 Project Acoustics를 사용하여 음향 효과 베이킹을 수행하는 방법에 대해 설명합니다.

소프트웨어 요구 사항:
* Windows 또는 MacOS용 [Unity 2018.2+](https://unity3d.com)
* [Unity 프로젝트에 통합된 Project Acoustics 플러그 인](unity-integration.md) 또는 [Project Acoustics Unity 콘텐츠 샘플](unity-quickstart.md)
* *선택 사항:* 클라우드 컴퓨팅을 사용하여 베이킹을 가속화하는 [Azure Batch 계정](create-azure-account.md)

## <a name="open-the-project-acoustics-bake-window"></a>Project Acoustics 음향 효과 베이킹 창 열기
Unity의 **Window**(창) 메뉴에서 **Acoustics**(음향 효과)를 선택합니다.

![Window 메뉴에서 Acoustics 옵션이 강조 표시된 Unity 편집기](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>탐색 메시 만들기
Project Acoustics는 탐색 메시를 사용하여 시뮬레이션에 대한 수신기 프로브 지점을 배치합니다. Unity [탐색 메시 워크플로](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)를 사용할 수 있습니다. 또는 다른 3D 모델링 패키지를 사용하여 사용자 고유의 메시를 디자인할 수 있습니다.

## <a name="mark-acoustic-scene-objects"></a>음향 장면 개체 표시
Project Acoustics는 시뮬레이션을 위해 다음 두 가지 형식의 장면 개체를 사용합니다.
- 시뮬레이션에서 소리를 반향하고 폐색하는 개체
- 시뮬레이션에서 수신기 프로브 지점을 제한하는 플레이어 탐색 메시

두 개체 형식은 모두 **Objects**(개체) 탭을 사용하여 표시됩니다.

개체를 표시하면 *AcousticsGeometry* 또는 *AcousticsNavigation* 구성 요소가 개체에 추가되므로 [표준 Unity 구성 요소 워크플로](https://docs.unity3d.com/Manual/UsingComponents.html)를 사용하여 개체를 표시하거나 표시 해제할 수도 있습니다. 메시 렌더러 및 지형만 표시할 수 있습니다. 다른 모든 개체 유형은 무시됩니다. 이 확인란은 영향을 받는 모든 개체를 표시하거나 표시하지 않습니다.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>음향 폐색 및 반향 기하 도형 표시
**Acoustics** 창의 **Objects**(개체) 탭을 엽니다. 소리를 폐색, 반향 또는 흡수해야 하는 경우 개체를 *음향 기하 도형*으로 표시합니다. 음향 효과 기하 도형에는 바닥, 벽, 지붕, 창 및 창 유리, 깔개 및 대형 가구 등이 포함될 수 있습니다. 이러한 개체에 임의의 복잡도 수준을 사용할 수 있습니다. 시뮬레이션하기 전에 장면이 복셀화되므로 잎이 많은 나무와 같이 매우 세밀한 메시를 베이킹하는 경우 간소화된 개체에 비해 더 많은 비용이 드는 것은 아닙니다.

보이지 않는 충돌 메시와 같이 음향 효과에 영향을 주지 않아야 하는 항목은 포함시키지 않습니다.

프로브 계산(**Probes**(프로브) 탭을 통해) 중에 개체의 변환은 베이킹 결과에서 수정됩니다. 장면에 표시된 개체가 나중에 이동하면 프로브 계산 및 베이킹을 다시 수행해야 합니다.

### <a name="mark-the-navigation-mesh"></a>탐색 메시 표시
Unity 워크플로를 통해 만든 탐색 메시는 Acoustics 시스템에서 선택합니다. 고유한 메시를 사용하려면 **Objects**(개체) 탭에서 해당 메시를 표시합니다.

### <a name="for-reference-the-objects-tab-parts"></a>참조: 개체 탭 부분
탭 페이지의 부분(설명 뒤에 그림이 나옴)은 다음과 같습니다.

1. 탭 선택 단추입니다(**Objects**(개체) 탭이 선택됨). 이러한 단추를 사용하여 음향 효과 베이킹의 다양한 단계를 왼쪽에서 오른쪽으로 진행합니다.
1. 이 탭을 사용하여 수행할 수 있는 작업에 대한 간략한 설명입니다.
1. 계층 구조 창에서 사용할 수 있는 필터. 이러한 옵션을 사용하여 계층 구조 창을 지정된 형식의 개체로 필터링하면 쉽게 표시할 수 있습니다. 음향 효과에 대한 개체를 아직 표시하지 않은 경우 마지막 두 옵션을 선택하면 아무 것도 표시되지 않습니다. 그러나 이러한 옵션은 개체를 표시한 후에 찾는 데 도움이 됩니다.
1. 개체를 선택하지 않으면 이 섹션에는 장면에 있는 모든 개체의 상태가 표시됩니다.
    * 합계: 숨겨지지 않은 활성 개체의 총 수입니다.
    * Ignored(무시): 메시 렌더러 또는 지형이 아닌 개체의 수입니다.
    * Mesh(메시): 메시 렌더러 개체의 수입니다.
    * Terrain(지형): 지형 개체의 수입니다.
    * Geometry(기하 도형): **Acoustics Geometry**(음향 효과 기하 도형)로 표시된 메시 또는 지형 개체의 수입니다.
    * Navigation(탐색): **Acoustics Navigation**(음향 효과 탐색)으로 표시된 메시 또는 지형 개체의 수입니다. 이 수에는 Unity NavMesh가 포함되지 않습니다.
1. 메시 렌더러 및 지형만 있는 장면에서 '표시 가능한' 개체의 총 수입니다. 확인란을 사용하여 해당 개체를 음향 효과에 대한 기하 도형 또는 탐색으로 표시합니다(해당 구성 요소 추가).
1. 아무 개체도 선택하지 않으면 필요에 따라 표시할 개체를 선택하라는 미리 알림이 표시됩니다. 또한 하나 또는 두 개의 확인란을 선택하여 장면의 모든 개체를 표시할 수도 있습니다.
1. 선택한 개체가 있으면 이 섹션에는 선택한 개체의 상태만 표시됩니다.
1. 선택한 '표시 가능' 개체의 총 수입니다. 확인란을 선택하거나 선택 취소하면 선택한 개체만 표시되거나 표시되지 않습니다.

장면에서 선택한 항목이 없는 경우 **Objects** 탭은 다음과 같습니다.

![선택한 항목이 없는 Acoustics Objects 탭](media/objects-tab-no-selection-detail.png)

장면 또는 계층 구조 창에서 선택한 항목이 있는 경우 탭은 다음 그림과 같습니다.

![선택한 항목이 있는 Acoustics Objects 탭](media/objects-tab-selection-detail.png)

일부 개체는 표시되지만 일부 개체가 표시되지 않은 경우 해당 확인란에는 다음 그림과 같이 "혼합된" 값이 표시됩니다.

![혼합된 선택 항목의 아이콘이 강조 표시된 Acoustics Objects 탭](media/mixed-object-selection-detail.png)

모든 항목을 표시하려면 이 확인란을 선택합니다. 모든 개체의 표시를 해제하려면 선택 취소합니다.

개체를 기하 도형 및 탐색 둘 다를 위한 개체로 표시할 수 있습니다.

## <a name="select-acoustic-materials"></a>음향 효과 재료 선택
개체가 표시되면 **Materials**(재료) 단추를 선택합니다. 그런 다음, 음향 효과 재료를 할당합니다. Project Acoustic 재료 시스템은 Unity 시각적 재료 시스템과 연결되어 있습니다. 두 개체에 별도의 음향 효과 재료가 포함되도록 하려면 별도의 시각적 재료가 있어야 합니다.

음향 효과 재료 선택은 각 표면에서 다시 반향되는 소리 에너지의 양을 결정합니다. 기본 음향 효과 재료의 흡수성은 철강과 비슷합니다. Project Acoustics는 시각적 재료 이름에 기반한 재료를 제안합니다. **Custom**(사용자 지정) 음향 효과 재료를 재료에 할당하여 조정 가능한 흡수 계수 슬라이더를 활성화할 수도 있습니다.

실내에서 지정된 재료의 잔향 시간은 흡수 계수와 반비례합니다. 대부분의 재료는 0.01~0.20의 범위에서 흡수 값을 갖습니다. 이 범위를 벗어나는 흡수 계수가 있는 재료는 흡수성이 매우 높습니다.

![그래프에서는 잔향 시간과 흡수 계수의 음의 상관 관계를 보여 줍니다.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>참조: Materials(재질) 탭 부분
![Unity의 Acoustics Materials(재료) 탭](media/materials-tab-detail.png)
1. 이 탭을 표시하는 **Materials** 단추입니다.
2. 이 탭을 사용하여 수행할 수 있는 작업에 대한 간략한 설명입니다.
3. 이 확인란을 선택하면 **Acoustics Geometry**(음향 효과 기하 도형)로 표시된 개체에서 사용하는 재료만 나열됩니다. 그렇지 않으면 장면에 사용된 모든 재료가 나열됩니다.
4. 이러한 옵션을 사용하여 **Acoustics**(음향 효과) 열(#6)의 메뉴에서 옵션 순서를 변경합니다. 음향 효과 재료를 **Name**(이름) 또는 **Absorptivity**(흡수성) 기준으로 낮음에서 높음으로 정렬합니다.
5. 장면에 사용되는 재료의 사전순으로 정렬된 목록입니다. **Show Marked Only**(표시된 재료만 표시) 확인란을 선택하면(#3), **Acoustics Geometry**로 표시된 개체에서 사용하는 재료만 표시됩니다. 재료를 사용하는 장면의 모든 개체를 선택하려면 여기서 해당 재료를 선택합니다.
6. 장면 재료가 할당된 음향 효과 재료입니다. 해당 장면 재료에 할당된 음향 효과 재료를 변경할 항목을 선택합니다. 이러한 메뉴의 정렬 순서를 변경하려면 **Sort Acoustics By**(음향 효과 정렬 기준) 옵션(#4)을 사용합니다.
7. 왼쪽 열(#6)에서 선택한 재료의 음향 효과 흡수 계수를 표시합니다. 값이 0이면 완전히 반향(흡수 없음)됨을 의미하고, 값이 1이면 완전히 흡수(반향 없음)됨을 의미합니다. 선택한 재료가 **Custom**(사용자 지정)이 아니면 흡수 계수를 변경할 수 없습니다.
8. **Custom**으로 표시된 재료의 경우 슬라이더가 활성화됩니다. 슬라이더를 이동하거나 값을 입력하여 흡수 계수를 할당할 수 있습니다.

## <a name="calculate-and-review-listener-probe-locations"></a>수신기 프로브 위치 계산 및 검토
재료가 할당되면 **Probes**(프로브) 탭으로 전환합니다. **Calculate**(계산)를 선택하여 시뮬레이션을 위해 수신기 프로브 지점을 배치합니다.

### <a name="what-the-calculate-button-does"></a>"Calculate" 단추의 기능
**Calculate** 단추는 선택한 음향 효과 장면 기하 도형을 사용하여 시뮬레이션할 장면을 준비합니다.

- 장면 메시에서 기하 도형을 가져오고, *복셀 볼륨*을 계산합니다. 복셀 볼륨은 작은 입방형 "복셀"로 구성된 전체 장면의 볼륨입니다. 복셀 크기는 **Simulation Resolution**(시뮬레이션 해상도) 설정으로 제어하는 시뮬레이션 주파수에 따라 결정됩니다. 각 복셀은 "open air"(실외)로 표시되거나 장면 기하 도형을 포함합니다. 복셀에 기하 도형이 포함되면 복셀에는 해당 기하 도형에 할당된 재료의 흡수 계수가 태그로 지정됩니다.
- 탐색 메시를 사용하여 수신기 프로브 지점을 배치합니다. 알고리즘은 공간 범위와 시뮬레이션 시간 및 파일 크기에 대한 경쟁 문제의 균형을 조정합니다. 좁은 복도와 작은 공간에는 항상 약간의 범위가 있어야 합니다. 일반적인 프로브 지점 수는 작은 장면의 100개에서 큰 장면의 수천 개에 이르기까지 다양합니다.

장면의 크기 및 컴퓨터의 속도에 따라, 이러한 계산이 몇 분 정도 걸릴 수 있습니다.

### <a name="review-voxel-and-probe-placement"></a>복셀 및 프로브 배치 검토
복셀 데이터와 프로브 지점 위치를 모두 미리 보고 장면을 베이킹할 준비가 되었는지 확인합니다. 불완전한 탐색 메시 또는 누락되거나 추가된 음향 효과 기하 도형은 일반적으로 미리 보기에서 쉽게 볼 수 있습니다. **Gizmos** 메뉴를 사용하여 복셀 및 프로브 배치를 사용하거나 사용하지 않도록 설정합니다.

![Unity의 Gizmos 메뉴](media/gizmos-menu.png)

음향 효과 기하 도형이 포함된 복셀은 녹색 정육면체로 표시됩니다. 장면을 검색하고, 기하 도형이어야 하는 모든 항목에 복셀이 있는지 확인합니다. 복셀이 표시되려면 장면 카메라가 개체의 약 5미터 이내에 있어야 합니다.

저해상도 및 고해상도를 사용하여 만든 복셀을 비교하면 저밀도 복셀이 2배 더 크다는 것을 알 수 있습니다.

![Unity 편집기의 저밀도 복셀 미리 보기](media/voxel-cubes-preview.png)

시뮬레이션 결과는 런타임에 수신기 프로브 지점 위치 간에 보간됩니다. 플레이어가 장면에서 이동할 것으로 예상되는 위치 근처에 프로브 지점이 있는지 확인합니다.

![Unity 편집기의 프로브 미리 보기](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>장면 이름 변경에 대한 주의
장면 이름은 프로브 지점 배치 및 복셀화를 저장하는 파일에 장면을 연결하는 데 사용됩니다. 프로브 지점을 계산한 후에 장면 이름이 바뀌면 재료 할당 및 배치 데이터가 손실되므로 다시 실행해야 합니다.

### <a name="for-reference-parts-of-the-probes-tab"></a>참조: Probes(프로브) 탭의 부분
![Unity의 Acoustics Probes(프로브) 탭](media/probes-tab-detail.png)

1. 이 탭을 표시하는 **Probes** 단추입니다.
2. 이 탭에서 수행할 수 있는 작업에 대한 간략한 설명입니다.
3. 이러한 옵션을 사용하여 저밀도 또는 고밀도 시뮬레이션 해상도를 설정합니다. 저밀도일수록 더 빠르지만, 장단점이 있습니다. 자세한 내용은 [베이킹 해상도](bake-resolution.md)를 참조하세요.
4. 음향 효과 데이터 파일을 저장할 위치를 지정합니다. 폴더 선택기에 액세스하려면 " **...** " 단추를 선택합니다. 기본 위치는 *Assets/AcousticsData*입니다. *Editor* 하위 폴더도 이 위치에 만들어집니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [베이킹 프로세스에서 추가된 데이터 파일](#Data-Files)을 참조하세요.
5. 여기에 지정된 접두사는 이 장면에 대한 데이터 파일의 이름을 지정하는 데 사용됩니다. 기본값은 "Acoustics_ *[장면 이름]* "입니다.
6. 프로브가 계산되면 방금 설명한 컨트롤을 사용할 수 없습니다. 새 설정으로 다시 계산할 수 있도록 계산을 지우고 컨트롤을 사용하도록 설정하려면 **Clear**(지우기) 단추를 선택합니다.
7. 장면을 복셀화하고 프로브 지점 위치를 계산하려면 **Calculate**(계산) 단추를 선택합니다. 계산은 머신에서 로컬로 수행됩니다. 베이킹을 수행하려면 먼저 이 작업을 수행해야 합니다.

이 버전의 Project Acoustics에서는 프로브를 수동으로 배치할 수 없습니다. **Probes** 탭에서 자동화된 프로세스를 사용합니다.

저밀도 또는 고밀도 해상도에 대한 자세한 내용은 [베이킹 해상도](bake-resolution.md)를 참조하세요.

## <a name="bake-your-scene-by-using-azure-batch"></a>Azure Batch를 사용하여 장면 베이킹
Azure Batch 서비스를 사용하여 클라우드의 컴퓨팅 클러스터에서 장면을 베이킹할 수 있습니다. Project Acoustics Unity 플러그 인은 Azure Batch에 직접 연결하여 각 베이킹마다 Azure Batch 클러스터를 인스턴스화, 관리 및 분해합니다. **Bake**(베이킹) 탭에서 Azure 자격 증명을 입력하고, 클러스터 머신의 종류와 크기를 선택한 다음, **Bake** 단추를 선택합니다.

### <a name="for-reference-parts-of-the-bake-tab"></a>참조: Bake(베이킹) 탭 부분
![Unity의 Acoustics Bake(베이킹) 탭](media/bake-tab-details.png)

1. 이 탭을 표시하는 **Bake** 단추입니다.
2. 이 페이지에서 수행할 수 있는 작업에 대한 간략한 설명입니다.
3. Azure 계정을 만든 후에 Azure 자격 증명을 이러한 필드에 입력합니다. 자세한 내용은 [Azure Batch 계정 만들기](create-azure-account.md)를 참조하세요.
4. Acoustics 도구 세트에 대한 Docker 이미지 태그 필드입니다.
5. Azure Portal을 열어 구독을 관리하고, 사용량을 모니터링하고, 청구 정보를 확인합니다.
6. 계산에 사용할 Azure Batch 컴퓨팅 노드 유형을 지정합니다. 노드 유형은 Azure 데이터 센터 위치에서 지원해야 합니다. 확실하지 않으면 **Standard_F8s_v2**로 둡니다.
7. 계산에 사용할 노드의 수입니다. 이 수는 베이킹 시간에 영향을 줍니다. Azure Batch 코어 할당으로 제한됩니다. 기본 할당은 두 개의 8코어 노드 또는 하나의 16코어 노드만 허용하지만 확장할 수 있습니다. 코어 할당 제약 조건에 대한 자세한 내용은 [Azure Batch 계정 만들기](create-azure-account.md)를 참조하세요.
8. [우선 순위가 낮은 노드](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)를 사용하도록 컴퓨팅 풀을 구성하려면 이 확인란을 선택합니다. 우선 순위가 낮은 컴퓨팅 노드의 비용은 훨씬 저렴합니다. 하지만 항상 사용할 수 있는 것은 아니지만 언제든지 선점될 수도 있습니다.
9. **Probes**(프로브) 탭에서 계산되는 장면에 대한 프로브 수입니다. 프로브 수는 클라우드에서 실행해야 하는 시뮬레이션의 수를 결정합니다. 프로브보다 더 많은 노드를 지정할 수 없습니다.
10. 노드 시작 시간을 제외하고 클라우드에서 작업을 실행하는 데 걸리는 예상 시간입니다. 작업 실행이 시작되면 이 필드에는 결과가 반환될 때까지의 예상 시간이 표시됩니다.
11. 시뮬레이션을 실행하는 데 필요한 컴퓨팅 시간의 총 크기입니다. 이 값은 Azure에서 사용되는 노드 컴퓨팅 시간의 총 크기입니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Azure 베이킹 비용 예측](#Estimating-bake-cost)을 참조하세요.
12. 작업이 완료되면 이 메시지에서 베이킹 결과가 저장되는 위치를 알려줍니다.
13. *(고급 사용 전용:)* 이 단추는 Unity에서 사용자가 제출한 베이킹을 제거하도록 합니다. 예를 들어 다른 머신을 사용하여 결과를 다운로드한 경우 **Clear State**(상태 지우기) 단추를 선택하여 해당 작업을 제거합니다. 준비되면 결과 파일이 다운로드되지 *않습니다*. *작업을 취소하는 것과는 다릅니다. 실행 중인 작업은 클라우드에서 계속 실행됩니다.*
14. 베이킹을 클라우드에 제출하려면 이 단추를 선택합니다. 작업이 실행되는 동안 이 단추는**Cancel Job**(작업 취소)이 됩니다.
15. [PC에서 음향 효과 시뮬레이션을 처리](#Local-bake)하도록 준비하려면 이 단추를 선택합니다.
16. 이 영역에는 준비 상태가 표시됩니다. 베이킹이 완료되면 "Downloaded"(다운로드됨)가 표시됩니다.

언제든지 [Azure Portal](https://portal.azure.com)에서 활성 작업, 컴퓨팅 풀 및 스토리지에 대한 전체 정보를 얻을 수 있습니다.

작업이 실행되는 동안 **Bake** 단추 레이블이 **Cancel Job**으로 변경됩니다. 진행 중인 작업을 취소하려면 이 단추를 사용합니다. 확인하라는 메시지가 표시됩니다. 작업 취소는 실행 취소할 수 없습니다. 취소하면 결과를 사용할 수 없지만, 사용된 Azure 컴퓨팅 시간에 대한 요금은 계속 청구됩니다.

베이킹이 시작되면 Unity를 닫을 수 있습니다. 프로젝트, 노드 유형 및 노드 수에 따라, 클라우드 준비에 몇 시간이 소요될 수 있습니다. 프로젝트를 다시 로드하고 Acoustics(음향 효과) 창을 열면 준비 작업 상태가 업데이트됩니다. 작업이 완료되면 출력 파일이 다운로드됩니다.

보안을 위해 Azure 자격 증명이 로컬 머신에 저장되고, Unity 편집기와 연결됩니다. 이러한 자격 증명은 Azure에 대한 보안 연결을 설정하는 데만 사용됩니다.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Azure Portal에서 실행 중인 작업의 상태 확인

1. **Bake**(베이킹) 탭에서 베이킹 작업 ID를 찾습니다.

    ![Bake 탭에서 강조 표시된 Unity 베이킹 작업 ID](media/unity-job-id.png)  

2. [Azure Portal](https://portal.azure.com)을 열고, 베이킹에 사용된 Batch 계정으로 이동하고, **작업**을 선택합니다.

    ![Azure Portal의 작업 링크](media/azure-batch-jobs.png)  

3. 작업 목록에서 작업 ID를 검색합니다.

   ![Azure Portal에서 강조 표시된 베이킹 작업 상태](media/azure-bake-job-status.png)  

4. 작업 ID를 선택하여 관련 작업 상태와 전체 작업 상태를 확인합니다.

   ![베이킹 작업 상태](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a> Azure 베이킹 비용 예측

베이킹 비용을 예측하려면 기간인 **예상 컴퓨팅 비용** 값으로 시작합니다. 이 값과 선택한 **VM 노드 유형**에 대한 현지 통화 단위의 시간당 비용을 곱합니다. 결과에는 노드를 시작하고 실행하는 데 필요한 노드 시간이 포함되지 않습니다.

예를 들어 노드 유형에 대해 **Standard_F8s_v2**를 선택한다고 가정합니다. 이 경우 비용은 시간당 $0.40입니다. **예상 컴퓨팅 비용**이 3시간 57분인 경우 작업을 실행하는 데 예상되는 비용은 $0.40 * 4시간 이하 = $1.60 이하입니다. 실제 비용은 노드를 시작하는 데 추가되는 시간으로 인해 조금 더 높아질 수 있습니다.

시간당 노드 비용은 [Azure Batch 가격](https://azure.microsoft.com/pricing/details/virtual-machines/linux)에서 확인합니다. (범주로 **컴퓨팅 최적화** 또는 **고성능 컴퓨팅**을 선택합니다.)

## <a name="Local-bake"></a> PC에서 장면 베이킹
사용자의 PC에서 장면을 베이킹할 수도 있습니다. 이 방법은 Azure Batch 계정을 만들기 전에 작은 장면에 대한 음향 효과를 실험하는 데 유용할 수 있습니다. 그러나 로컬 음향 효과 시뮬레이션은 장면의 크기에 따라 오래 걸릴 수 있습니다.

### <a name="minimum-hardware-requirements"></a>최소 하드웨어 요구 사항
* 8개 이상의 코어와 32GB의 RAM이 있는 x86-64 프로세서
* Docker를 실행하도록 [활성화된 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

예를 들어 Intel Xeon E5-1660 @ 3GHz 및 32GB RAM을 갖춘 8코어 머신에서 테스트한 결과 다음과 같습니다.
* 100개의 프로브가 있는 작은 크기의 장면의 경우 저밀도 해상도 베이킹에서 약 2시간, 고밀도 해상도 베이킹에서 약 32시간이 걸렸습니다.
* 1,000개의 프로브가 있는 중간 크기의 장면의 경우 저밀도 해상도 베이킹에서 약 20시간, 고밀도 해상도 베이킹에서 약 21일이 걸렸습니다.

### <a name="set-up-docker"></a>Docker 설정
시뮬레이션을 처리할 PC에 Docker를 설치하고 구성합니다.
1. [Docker Desktop](https://www.docker.com/products/docker-desktop)을 설치합니다.
2. Docker 설정을 열고, **고급**으로 이동하고, 8GB 이상의 RAM에 대한 리소스를 구성합니다. Docker에 할당할 수 있는 CPU가 많을수록 베이킹이 더 빨리 수행됩니다.  
![Docker 설정 샘플](media/docker-settings.png)
1. **공유 드라이브**로 이동하여 처리에 사용되는 드라이브에 대한 공유를 설정합니다.  
![Docker 공유 드라이브 옵션](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>로컬 베이킹 실행
1. **Bake**(베이킹) 탭에서 **Prepare Local Bake**(로컬 베이킹 준비) 단추를 선택합니다. 그런 다음, 입력 파일과 실행 스크립트를 저장할 폴더 위치를 선택합니다. 그러면 최소 하드웨어 요구 사항을 충족하는 한 모든 머신에서 베이킹을 실행할 수 있습니다. 폴더를 해당 머신에 복사하여 Docker를 설치합니다.
2. 시뮬레이션을 시작하려면 Windows에서 *runlocalbake.bat* 스크립트를 실행하거나, MacOS에서 *runlocalbake.sh* 스크립트를 실행합니다. 이 스크립트는 시뮬레이션 처리에 필요한 도구 세트를 사용하여 Project Acoustics Docker 이미지를 가져와서 시뮬레이션을 시작합니다.
3. 시뮬레이션이 완료되면 결과 *.ace* 파일을 Unity 프로젝트에 다시 복사합니다. Unity에서 이 파일을 이진 파일로 인식하도록 하려면 ".bytes"를 파일 확장명에 추가합니다(예: "Scene1.ace.bytes"). 시뮬레이션에 대한 자세한 로그는 *AcousticsLog.txt*에 저장됩니다. 문제가 발생하면 이를 진단하는 데 도움이 되도록 이 파일을 검사합니다.

## <a name="Data-Files"></a> 베이킹 프로세스에서 추가된 데이터 파일

베이킹 프로세스 중에 다음 4개의 데이터 파일이 만들어집니다. 하나에는 시뮬레이션 결과가 포함되며 제목과 함께 제공됩니다. 다른 나머지 파일에는 Unity 편집기 관련 데이터가 저장됩니다.

시뮬레이션 결과:
* *Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes*: 이 파일은 런타임 조회 테이블입니다. 여기에는 시뮬레이션 결과와 복셀화된 음향 효과 장면 요소가 포함되어 있습니다. 이 파일의 이름과 위치는 **Probes**(프로브) 탭에서 변경할 수 있습니다.

   *시뮬레이션 결과 파일을 삭제하지 않도록 주의하세요. 이 파일은 장면을 다시 베이킹하는 경우를 제외하고는 복구할 수 없습니다.*

데이터 파일 편집기:
* *Assets/Editor/[SceneName]\_AcousticsParameters.asset*: 이 파일에는 Acoustics UI의 필드에 입력하는 데이터가 저장됩니다. 이 파일의 이름과 위치는 변경할 수 없습니다.
* *Assets/AcousticsData/Editor/Acoustics_[SceneName].vox*: 이 파일에는 **Probes**(프로브) 탭의 **Calculate**(계산) 단추를 선택하는 경우 계산되는 복셀화된 음향 효과 기하 도형 및 재료 속성이 저장됩니다. 이 파일의 이름과 위치는 **Probes**(프로브) 탭에서 변경할 수 있습니다.
* *Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml*: 이 파일에는 **Calculate**를 선택하는 경우 계산되는 시뮬레이션 매개 변수가 저장됩니다. 이 파일의 이름과 위치는 **Probes**(프로브) 탭에서 변경할 수 있습니다.

## <a name="set-up-the-acoustics-lookup-table"></a>음향 조회 테이블 설정
**Project Acoustics** prefab을 프로젝트 패널에서 장면으로 끌어 놓습니다.

![Unity의 Acoustics prefab](media/acoustics-prefab.png)

**ProjectAcoustics** 게임 개체를 선택하고, 해당 검사기 패널로 이동합니다. 베이킹 결과의 위치를 지정합니다(*Assets/AcousticsData*의 .ace 파일). Acoustics Manager(음향 효과 관리자) 스크립트로 끌어 놓거나 텍스트 상자 옆에 있는 원 단추를 선택합니다.

![Unity의 Acoustics Manager prefab](media/acoustics-manager.png)

## <a name="next-steps"></a>다음 단계
* [Unity 디자인 컨트롤](unity-workflow.md)을 살펴봅니다.
* [Project Acoustics 디자인 개념](design-process.md)을 살펴봅니다.
