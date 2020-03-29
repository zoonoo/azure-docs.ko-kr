---
title: Team Data Science Process 역할 및 작업
description: 데이터 과학 그룹의 주요 구성 요소, 인사 역할 및 관련 작업의 개요입니다.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720013"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team Data Science Process 역할 및 작업

팀 데이터 과학 프로세스(TDSP)는 예측 분석 솔루션과 지능형 응용 프로그램을 효율적으로 빌드하기 위한 구조화 된 방법을 제공하는 Microsoft에서 개발한 프레임워크입니다. 이 문서에서는 이 프로세스를 표준화하는 데이터 과학 팀의 주요 인사 역할 및 관련 작업에 대해 간략하게 설명합니다.

이 소개 문서에서는 TDSP 환경을 설정하는 방법에 대한 자습서를 링크합니다. 자습서에서는 Azure DevOps 프로젝트, Azure 리포지토리 및 Azure 보드 사용에 대한 자세한 지침을 제공합니다.  동기 부여 목표는 개념에서 모델링으로, 그리고 배포로 이동하는 것입니다.

이 자습서에서는 Microsoft에서 TDSP를 구현하는 방법이므로 Azure DevOps를 사용합니다. Azure DevOps는 역할 기반 보안, 작업 항목 관리 및 추적, 코드 호스팅, 공유 및 소스 제어를 통합하여 공동 작업을 용이하게 합니다. 또한 이 자습서에서는 Azure 데이터 과학 가상 머신(DSVM)을 분석 데스크톱으로 사용하며, 이 데스크톱에는 Microsoft 소프트웨어 및 Azure 서비스와 미리 구성되고 통합된 몇 가지 인기 있는 데이터 과학 도구가 있습니다. [Data Science Virtual Machine](https://aka.ms/dsvm) 

자습서를 사용하여 다른 코드 호스팅, 민첩한 계획 및 개발 도구 및 환경을 사용하여 TDSP를 구현할 수 있지만 일부 기능을 사용하지 못할 수도 있습니다.

## <a name="structure-of-data-science-groups-and-teams"></a>데이터 과학 그룹 및 팀 구조

기업의 데이터 과학 기능은 다음과 같은 계층 구조로 구성되는 경우가 많습니다.

- 데이터 과학 그룹
  - 그룹 내 데이터 과학 팀/s

이러한 구조에는 그룹 리드와 팀 리드가 있습니다. 일반적으로 데이터 과학 프로젝트는 데이터 과학 팀에 의해 수행됩니다. 데이터 과학 팀은 프로젝트 관리 및 거버넌스 작업에 대한 프로젝트 리드를 가지고 있으며, 개별 데이터 과학자와 엔지니어는 프로젝트의 데이터 과학 및 데이터 엔지니어링 부분을 수행합니다. 초기 프로젝트 설정 및 거버넌스는 그룹, 팀 또는 프로젝트 리드에 의해 수행됩니다.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>네 가지 TDSP 역할에 대한 정의 및 작업
데이터 과학 단위가 그룹 내의 팀으로 구성된다는 가정하에 TDSP 담당자에게는 다음과 같은 네 가지 역할이 있습니다.

1. **그룹 관리자**: 기업의 전체 데이터 과학 단위를 관리합니다. 데이터 과학 단위에는 여러 팀이 있을 수 있고 각 팀은 고유 비즈니스 직종에서 여러 데이터 과학 프로젝트 작업을 수행합니다. 그룹 관리자는 대리인에게 작업을 위임할 수는 있지만 역할에 관련된 작업은 변경되지 않습니다.
   
2. **팀 리더**: 기업의 데이터 과학 부서에서 팀을 관리합니다. 팀은 여러 명의 데이터 과학자로 구성됩니다. 소규모 데이터 과학 단위의 경우 그룹 관리자와 팀 리더가 동일한 사람일 수 있습니다.
   
3. **프로젝트 리더**: 특정 데이터 과학 프로젝트에서 개별 데이터 과학자의 일상 활동을 관리합니다.
   
4. **프로젝트 개별 기여자**: 데이터 과학자, 비즈니스 분석가, 데이터 엔지니어, 설계자 및 데이터 과학 프로젝트를 실행하는 다른 사람.

> [!NOTE]
> 기업의 구조와 규모에 따라 한 사람이 두 개 이상의 역할을 할 수 있거나 두 명 이상이 역할을 채울 수 있습니다.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>네 가지 역할로 완료해야 할 작업

다음 다이어그램은 각 팀 데이터 과학 프로세스 역할에 대한 최상위 작업을 보여 주며 있습니다. 이 스키마와 각 TDSP 역할에 대한 작업의 자세한 개요를 통해 책임에 따라 필요한 자습서를 선택하는 데 도움이 될 수 있습니다.

![역할 및 작업 개요](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>그룹 관리자 작업

그룹 관리자 또는 지정된 TDSP 시스템 관리자는 TDSP를 채택하기 위해 다음 작업을 완료합니다.

- Azure DevOps **조직** 및 조직 내의 그룹 프로젝트를 만듭니다. 
- Azure DevOps 그룹 프로젝트에서 **프로젝트 템플릿 리포지토리를** 만들고 Microsoft TDSP 팀에서 개발한 프로젝트 템플릿 리포지토리에서 시드합니다. Microsoft TDSP 프로젝트 템플릿 리포지토리는 다음을 제공합니다.
  - 데이터, 코드 및 문서에 대한 디렉터리를 포함하여 **표준화된 디렉터리 구조입니다.**
  - 효율적인 데이터 과학 프로세스를 안내하는 **표준화된 문서 템플릿** 세트입니다.
- Microsoft TDSP 팀에서 개발한 **유틸리티 리포지토리에서 유틸리티 리포지토리를**만들고 시드합니다. Microsoft의 TDSP 유틸리티 리포지토리는 데이터 과학자의 작업을 보다 효율적으로 만드는 데 유용한 유틸리티 집합을 제공합니다. Microsoft 유틸리티 리포지토리에는 대화형 데이터 탐색, 분석, 보고 및 기준 모델링 및 보고를 위한 유틸리티가 포함됩니다.
- 조직 계정에 대한 **보안 제어 정책을** 설정합니다.

자세한 지침은 [데이터 과학 팀의 그룹 관리자 작업을](group-manager-tasks.md)참조하십시오.

## <a name="team-lead-tasks"></a>팀 리더 작업

팀 리더 또는 지정된 프로젝트 관리자는 TDSP를 채택하기 위해 다음 작업을 완료합니다.

- 그룹의 Azure DevOps 조직에서 팀 **프로젝트를** 만듭니다.
- 프로젝트에서 **프로젝트 템플릿 리포지토리를** 만들고 그룹 관리자 또는 대리자가 설정한 그룹 프로젝트 템플릿 리포지토리에서 이를 시드합니다.
- 팀 **유틸리티 리포지토리를**만들고 그룹 유틸리티 리포지토리에서 시드하고 팀별 유틸리티를 리포지토리에 추가합니다.
- 선택적으로 [Azure 파일 저장소를](https://azure.microsoft.com/services/storage/files/) 만들어 팀의 유용한 데이터 자산을 저장합니다. 다른 팀 구성원도 팀의 분석 데스크톱에 이 공유 클라우드 파일 저장소를 탑재할 수 있습니다.
- 선택적으로 팀의 **DSVM에** Azure 파일 저장소를 탑재하고 팀 데이터 자산을 추가합니다.
- 팀 구성원을 추가하고 해당 권한을 구성하여 **보안 제어를** 설정합니다.

자세한 지침은 [데이터 과학 팀의 팀 리더 작업을](team-lead-tasks.md)참조하십시오.


## <a name="project-lead-tasks"></a>프로젝트 리더 작업

프로젝트 리더는 TDSP를 채택하기 위해 다음 작업을 완료합니다.

- 팀 프로젝트에서 **프로젝트 리포지토리를** 만들고 프로젝트 템플릿 리포지토리에서 시드합니다.
- 선택적으로 프로젝트의 데이터 자산을 저장 하는 **Azure 파일 저장소를** 만듭니다.
- 선택적으로 Azure 파일 저장소를 **DSVM에** 탑재하고 프로젝트 데이터 자산을 추가합니다.
- 프로젝트 멤버를 추가하고 해당 권한을 구성하여 **보안 제어를** 설정합니다.

자세한 지침은 [데이터 과학 팀의 프로젝트 잠재 고객 작업을](project-lead-tasks.md)참조하십시오.

## <a name="project-individual-contributor-tasks"></a>프로젝트 개별 참가자 작업

일반적으로 데이터 과학자인 프로젝트 개별 기여자는 TDSP를 사용하여 다음 작업을 수행합니다.

- 프로젝트 리더가 설정한 **프로젝트 리포지토리를** 복제합니다.
- 선택적으로 공유 팀을 탑재하고 **DSVM(데이터 과학 가상 머신)에** **Azure 파일 저장소를** 프로젝사킵니다.
- 프로젝트를 실행합니다.

프로젝트에 온보딩에 대한 자세한 지침은 [데이터 과학 팀의 프로젝트 개별 기여자 작업을](project-ic-tasks.md)참조하십시오.

## <a name="data-science-project-execution-workflow"></a>데이터 과학 프로젝트 실행 워크플로우

관련 자습서를 따르면 데이터 과학자, 프로젝트 리드 및 팀 리드는 프로젝트의 모든 작업과 단계를 처음부터 끝까지 추적하는 작업 항목을 만들 수 있습니다. Azure Repos를 사용하면 데이터 과학자 간의 공동 작업을 촉진하고 프로젝트 실행 중에 생성된 아티팩트가 모든 프로젝트 구성원이 버전을 제어하고 공유하도록 합니다. Azure DevOps를 사용하면 Azure 보드 작업 항목을 Azure 리포지토리 분기와 연결하고 작업 항목에 대해 수행된 작업을 쉽게 추적할 수 있습니다.

다음 그림에서는 프로젝트 실행을 위한 TDSP 워크플로우를 간략하게 설명합니다.

![일반적인 데이터 과학 프로젝트 워크플로우](./media/roles-tasks/overview-project-execute.png)

워크플로 단계는 다음 세 가지 활동으로 그룹화할 수 있습니다.

- 프로젝트 리드가 스프린트 계획 수행
- 데이터 과학자는 작업 `git` 항목을 해결하기 위해 분기에 아티팩트를 개발합니다.
- 프로젝트 리더 또는 다른 팀 구성원이 코드 검토를 수행하고 작업 분기를 마스터 분기에 병합합니다.

프로젝트 실행 워크플로우에 대한 자세한 지침은 [데이터 과학 프로젝트의 애자일 개발을](agile-development.md)참조하십시오.

## <a name="tdsp-project-template-repository"></a>TDSP 프로젝트 템플릿 리포지토리

Microsoft TDSP 팀의 [프로젝트 템플릿 리포지토리를](https://github.com/Azure/Azure-TDSP-ProjectTemplate) 사용하여 효율적인 프로젝트 실행 및 공동 작업을 지원합니다. 리포지토리는 자체 TDSP 프로젝트에 사용할 수 있는 표준화된 디렉터리 구조 및 문서 템플릿을 제공합니다.

## <a name="next-steps"></a>다음 단계

Team Data Science Process에서 정의한 역할 및 작업에 대한 자세한 설명을 살펴봅니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀을 위한 개별 기여자 작업 프로젝트](project-ic-tasks.md)
