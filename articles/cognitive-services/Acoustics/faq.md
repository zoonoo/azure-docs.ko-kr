---
title: 프로젝트 음향 자주 묻는 질문
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
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770206"
---
# <a name="project-acoustics-frequently-asked-questions"></a>프로젝트 음향 자주 묻는 질문

## <a name="what-is-project-acoustics"></a>프로젝트 Acoustics란?

플러그인의 프로젝트 음향 제품군은 정적 조명과 유사한 런타임 전에 음파 동작을 계산하는 음향 시스템입니다. 클라우드는 파동 물리학 계산을 수행하므로 런타임 CPU 비용이 낮습니다.  

## <a name="where-can-i-download-the-plugin"></a>이 플러그 인을 어디에서 다운로드할 수 있나요?

프로젝트 음향 [유니티 플러그인](https://www.microsoft.com/download/details.aspx?id=57346) 또는 [프로젝트 음향 언리얼 플러그인을 다운로드할](https://www.microsoft.com/download/details.aspx?id=58090)수 있습니다.

## <a name="does-project-acoustics-support-ltxgt-platform"></a>프로젝트 음향이 &lt;x&gt; 플랫폼을 지원합니까?

프로젝트 음향 플랫폼 지원은 고객의 요구에 따라 진화합니다. [프로젝트 음향 문제 포럼에](https://github.com/microsoft/ProjectAcoustics/issues) 문의하여 추가 플랫폼에 대한 지원에 대해 문의하시기 바랍니다.

## <a name="is-azure-used-at-runtime"></a>Azure는 런타임에 사용되나요?

아니요. 클라우드 통합은 사전 계산 단계 동안에만 사용됩니다.
 
## <a name="what-is-simulation-input"></a>시뮬레이션 입력이란 무엇인가요? 

시뮬레이션 입력은 3D 장면, 가상 환경 또는 게임 수준입니다. 프로젝트 Acoustics는 원활한 폐색 및 분산을 포함하는 소리 물리학을 밀접하게 모델링하는 3D 볼륨 파형 시뮬레이션을 수행합니다.
 
## <a name="what-is-the-runtime-cost"></a>런타임 비용이란 무엇인가요?

Acoustics는 프레임별로 원본당 약 0.01%의 CPU를 사용합니다. RAM 사용량은 장면 크기에 따라 달라지며, 10~100MB 범위일 수 있습니다.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>수평 기하 도형을 간소화해야 하나요? 삼각형 수를 제어해야 하나요? 물이 새지 않는 메시를 만들어야 하나요?

아니요. 시스템이 자세한 수평 기하 도형을 직접 수집합니다. 내부 처리를 위해 입체 화상으로 만들어집니다.
 
## <a name="whats-in-the-runtime-lookup-table"></a>런타임 조회 테이블에는 무엇이 있나요?

ACE 파일에는 수많은 소스 및 리스너 위치 쌍 간의 음향 파라미터 테이블뿐만 아니라 매개변수 보간에 사용되는 복셀화된 장면 형상이 포함됩니다.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>프로젝트 음향이 움직이는 소스를 처리할 수 있습니까?

예. 프로젝트 어쿠스틱은 조회 테이블을 참조하고 각 틱에서 오디오 DSP를 업데이트하므로 움직이는 소스와 리스터를 처리할 수 있습니다.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>프로젝트 음향이 동적 형상을 처리할 수 있습니까? 닫히는 문을 처리할 수 있나요? 떨어져 나가는 벽을 처리할 수 있나요?

아니요. 음향 매개 변수는 게임 수준의 정적 상태에 따라 미리 계산됩니다. 문 형상을 음향에서 벗어난 다음 확립된 기술을 사용하여 파괴 가능 및 이동식 게임 오브젝트의 상태에 따라 추가 오클루전을 적용하는 것이 좋습니다.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>프로젝트 음향은 음향 재료를 사용합니까?

예. 재질은 사용자 수준의 실제 재질 이름에서 선택되며 흡수성을 갖습니다.
 
## <a name="what-do-the-probes-represent"></a>"프로브"는 무엇을 나타내나요?

프로브는 가능한 플레이어 위치를 샘플링한 것입니다. 각 프로브는 프로브 위치에서 시작된 장면의 별도 파형 시뮬레이션을 나타냅니다. 런타임 시 수신기 위치에 대한 음향 매개 변수는 근처의 프로브 위치에서 보간됩니다.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>클라우드에서 그렇게 많은 컴퓨팅이 수행되는 이유는 무엇인가요? 사용자에게 어떤 이득이 있나요?

프로젝트 Acoustics는 아주 복잡한 가상 환경에서도 정확하고 안정적인 음향 매개 변수를 제공하며 모든 아키텍처 측면을 고려합니다. 도면 체적의 수동 작업 없이 도면 의 매끄러운 오클루전 및 장애물및 동적 리버브 변형을 제공합니다. 유지되는 모든 작업에서는 런타임 동안 CPU가 부족합니다.

## <a name="what-exactly-happens-during-baking"></a>"준비" 중에 정확히 어떤 작업이 수행되나요?

베이크는 각 리스너 프로브를 중심으로 한 입방체 시뮬레이션 영역의 음향파 시뮬레이션으로 구성됩니다.

## <a name="is-my-source-content-secure"></a>내 소스 콘텐츠가 안전한가요?

프로젝트 음향은 소스 장면 형상을 클라우드에 업로드하지 않습니다. 대신 시뮬레이션은 프로브 위치 데이터와 결합되어 독점적인 형식으로 저장되는 장면의 복셀화에서 작동합니다.     

## <a name="next-steps"></a>다음 단계
* 프로젝트 [음향 유니티 샘플 콘텐츠](unity-quickstart.md) 또는 [언리얼 샘플 콘텐츠](unreal-quickstart.md) 사용

