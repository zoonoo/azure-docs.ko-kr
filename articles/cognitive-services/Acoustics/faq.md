---
title: Project Acoustics 질문과 대답
titlesuffix: Azure Cognitive Services
description: 이 페이지에서는 다운로드 지침 및 준비 프로세스를 포함하여 프로세스 Acoustics에 대한 질문과 대답을 제공합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: f0484fc9fc2af5514ba0f5b61277146a51757057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855040"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Project Acoustics 질문과 대답

## <a name="what-is-project-acoustics"></a>프로젝트 Acoustics란?

플러그 인의 Project Acoustics suite는 정적 조명과 유사 하 게 런타임 이전에 발생 하는 소리 웨이브 동작을 계산 하는 Acoustics 시스템입니다. 클라우드는 파동 물리학 계산을 수행하므로 런타임 CPU 비용이 낮습니다.  

## <a name="where-can-i-download-the-plugin"></a>이 플러그 인을 어디에서 다운로드할 수 있나요?

[Project Acoustics Unity 플러그 인](https://www.microsoft.com/download/details.aspx?id=57346) 또는 [Project Acoustics unreal plugin](https://www.microsoft.com/download/details.aspx?id=58090)을 다운로드할 수 있습니다.

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Project Acoustics에서 x &lt;&gt; 플랫폼을 지원 하나요?

Project Acoustics platform 지원은 고객의 요구에 따라 진화 합니다. [Project Acoustics issue 포럼](https://github.com/microsoft/ProjectAcoustics/issues) 에서 microsoft에 문의 하 여 추가 플랫폼에 대 한 지원에 대해 문의 하세요.

## <a name="is-azure-used-at-runtime"></a>Azure는 런타임에 사용되나요?

아니요. 클라우드 통합은 사전 계산 단계 동안에만 사용됩니다.
 
## <a name="what-is-simulation-input"></a>시뮬레이션 입력이란 무엇인가요? 

시뮬레이션 입력은 3D 장면, 가상 환경 또는 게임 수준입니다. 프로젝트 Acoustics는 원활한 폐색 및 분산을 포함하는 소리 물리학을 밀접하게 모델링하는 3D 볼륨 파형 시뮬레이션을 수행합니다.
 
## <a name="what-is-the-runtime-cost"></a>런타임 비용이란 무엇인가요?

Acoustics는 프레임별로 원본당 약 0.01%의 CPU를 사용합니다. RAM 사용량은 장면 크기에 따라 달라지며, 10~100MB 범위일 수 있습니다.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>수평 기하 도형을 간소화해야 하나요? 삼각형 수를 제어해야 하나요? 물이 새지 않는 메시를 만들어야 하나요?

아니요. 시스템이 자세한 수평 기하 도형을 직접 수집합니다. 내부 처리를 위해 입체 화상으로 만들어집니다.
 
## <a name="whats-in-the-runtime-lookup-table"></a>런타임 조회 테이블에는 무엇이 있나요?

ACE 파일에는 다양 한 소스 및 수신기 위치 쌍 간의 어쿠스틱 매개 변수 및 매개 변수 보간에 사용 되는 voxelized 장면 기 하 도형에 대 한 표가 포함 되어 있습니다.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>프로젝트에서 이동 소스를 처리 하는 것을 Acoustics 수 있나요?

예, Project Acoustics는 조회 테이블을 검색 하 고 각 틱에서 오디오 DSP를 업데이트 하므로 원본 및 수신기 이동을 처리할 수 있습니다.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>프로젝트 Acoustics 동적 기 하 도형을 처리할 수 있나요? 닫히는 문을 처리할 수 있나요? 떨어져 나가는 벽을 처리할 수 있나요?

아니요. 음향 매개 변수는 게임 수준의 정적 상태에 따라 미리 계산됩니다. 도어 기 하 도형을 acoustics에서 종료 한 다음, 설정 된 기술을 사용 하 여 소멸 가능한 및 이동 가능한 게임 개체의 상태에 따라 추가 폐색을 적용 하는 것이 좋습니다.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>프로젝트 Acoustics 음향 자료를 사용 하나요?

예. 재질은 사용자 수준의 실제 재질 이름에서 선택되며 흡수성을 갖습니다.
 
## <a name="what-do-the-probes-represent"></a>"프로브"는 무엇을 나타내나요?

프로브는 가능한 플레이어 위치를 샘플링한 것입니다. 각 프로브는 프로브 위치에서 시작된 장면의 별도 파형 시뮬레이션을 나타냅니다. 런타임 시 수신기 위치에 대한 음향 매개 변수는 근처의 프로브 위치에서 보간됩니다.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>클라우드에서 그렇게 많은 컴퓨팅이 수행되는 이유는 무엇인가요? 사용자에게 어떤 이득이 있나요?

프로젝트 Acoustics는 아주 복잡한 가상 환경에서도 정확하고 안정적인 음향 매개 변수를 제공하며 모든 아키텍처 측면을 고려합니다. 볼륨 그리기를 수동으로 수행 하지 않고도 부드러운 폐색 및 장애물 및 동적 반향 변형을 제공 합니다. 유지되는 모든 작업에서는 런타임 동안 CPU가 부족합니다.

## <a name="what-exactly-happens-during-baking"></a>"준비" 중에 정확히 어떤 작업이 수행되나요?

굽기는 각 수신기 프로브를 중심으로 하는 cuboid 시뮬레이션 지역의 음향 웨이브 시뮬레이션으로 구성 됩니다.

## <a name="next-steps"></a>다음 단계
* [Project Acoustics Unity 샘플 콘텐츠](unity-quickstart.md) 또는 [unreal 샘플 콘텐츠](unreal-quickstart.md) 를 사용해 보세요.

