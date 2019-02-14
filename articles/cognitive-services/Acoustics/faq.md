---
title: 프로젝트 Acoustics에 대한 질문과 대답
titlesuffix: Azure Cognitive Services
description: 이 페이지에서는 다운로드 지침 및 준비 프로세스를 포함하여 프로세스 Acoustics에 대한 질문과 대답을 제공합니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 12dda5d7af1236ec2d7eddbe025b8fdba47d7bca
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881077"
---
# <a name="frequently-asked-questions"></a>질문과 대답

## <a name="what-is-project-acoustics"></a>프로젝트 Acoustics란?

프로젝트 Acoustics Unity 플러그 인은 런타임 전에 정적 조명과 비슷한 사운드 웨이브 동작을 계산하는 음향 시스템입니다. 클라우드는 파동 물리학 계산을 수행하므로 런타임 CPU 비용이 낮습니다.  

## <a name="where-can-i-download-the-plugin"></a>이 플러그 인을 어디에서 다운로드할 수 있나요?

Acoustics 플러그 인 평가에 관심이 있는 분은 [여기](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)서 디자이너 미리 보기에 등록하세요.

## <a name="is-azure-used-at-runtime"></a>Azure는 런타임에 사용되나요?

아니요. 클라우드 통합은 사전 계산 단계 동안에만 사용됩니다.
 
## <a name="what-is-simulation-input"></a>시뮬레이션 입력이란 무엇인가요? 

시뮬레이션 입력은 3D 장면, 가상 환경 또는 게임 수준입니다. 프로젝트 Acoustics는 원활한 폐색 및 분산을 포함하는 소리 물리학을 밀접하게 모델링하는 3D 볼륨 파형 시뮬레이션을 수행합니다.
 
## <a name="what-is-the-runtime-cost"></a>런타임 비용이란 무엇인가요?

Acoustics는 프레임별로 원본당 약 0.01%의 CPU를 사용합니다. RAM 사용량은 장면 크기에 따라 달라지며, 10~100MB 범위일 수 있습니다.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>수평 기하 도형을 간소화해야 하나요? 삼각형 수를 제어해야 하나요? 물이 새지 않는 메시를 만들어야 하나요?

 아니요. 시스템이 자세한 수평 기하 도형을 직접 수집합니다. 내부 처리를 위해 입체 화상으로 만들어집니다.
 
## <a name="whats-in-the-runtime-lookup-table"></a>런타임 조회 테이블에는 무엇이 있나요?

ACE 파일은 다양한 원본과 수신기 위치 쌍 간의 음향 매개 변수 테이블입니다.
 
## <a name="can-it-handle-moving-sources"></a>이동 원본을 처리할 수 있나요?

예. **Microsoft Acoustics** Unity Spatializer 플러그 인은 현재 원본 및 수신기 위치를 사용하여 각 오디오 처리 틱에서 조회 테이블을 참조합니다. Spatializer의 DSP는 각 틱의 음향 처리 매개 변수를 원활하게 업데이트합니다.
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>동적 기하 도형을 처리할 수 있나요? 닫히는 문을 처리할 수 있나요? 떨어져 나가는 벽을 처리할 수 있나요?

 아니요. 음향 매개 변수는 게임 수준의 정적 상태에 따라 미리 계산됩니다. 음향 효과에서 도어 기하 도형을 분리한 후, 설정된 기술을 사용하여 소멸 가능하고 이동 가능한 게임 개체의 상태에 따라 추가 폐색을 적용하는 것이 좋습니다.
 
## <a name="does-it-handle-materials"></a>재질을 처리하나요?

예. 재질은 사용자 수준의 실제 재질 이름에서 선택되며 흡수성을 갖습니다.
 
## <a name="what-do-the-probes-represent"></a>"프로브"는 무엇을 나타내나요?

프로브는 가능한 플레이어 위치를 샘플링한 것입니다. 각 프로브는 프로브 위치에서 시작된 장면의 별도 파형 시뮬레이션을 나타냅니다. 런타임 시 수신기 위치에 대한 음향 매개 변수는 근처의 프로브 위치에서 보간됩니다.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>클라우드에서 그렇게 많은 계산이 수행되는 이유는 무엇인가요? 사용자에게 어떤 이득이 있나요?

프로젝트 Acoustics는 아주 복잡한 가상 환경에서도 정확하고 안정적인 음향 매개 변수를 제공하며 모든 아키텍처 측면을 고려합니다. 수동 작업 없는 원활한 폐색/장애와 볼륨을 발생하지 않는 동적 반향 변형을 제공합니다. 유지되는 모든 작업에서는 런타임 동안 CPU가 부족합니다.

## <a name="what-exactly-happens-during-baking"></a>"준비" 중에 정확히 어떤 작업이 수행되나요?

시스템은 잠재적인 플레이어 위치를 고려하여 균일한 간격의 "프로브" 샘플 위치 집합을 생성합니다. 평준화에 대한 준비는 각 프로브에 대한 독립적인 태스크로 구성됩니다. 시스템은 프로브의 중심에 있는 큐브형 "시뮬레이션 지역"을 고려하고, 해당 지역 내에서 최대 25cm 해상도로 자세한 파형 시뮬레이션을 수행합니다.

## <a name="next-steps"></a>다음 단계
* [샘플 장면](sample-walkthrough.md) 탐색

