---
title: Team Data Science Process 역할 및 작업
description: 주요 구성 요소, 담당자 역할 및 데이터 과학 그룹과 관련 작업의 개요입니다.
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
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000966"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team Data Science Process 역할 및 작업

TDSP(Team Data Science Process)는 Microsoft에서 개발한 프레임워크로, 예측 분석 솔루션 및 인텔리전트 애플리케이션을 효율적으로 빌드하는 구조적 방법론을 제공합니다. 이 문서에서는 이 프로세스를 표준화하는 데이터 과학팀의 주요 담당자 역할 및 관련 작업을 설명합니다.

이 소개 문서는 TDSP 환경을 설정하는 방법에 대한 자습서로 연결됩니다. 이 자습서에서는 Azure DevOps Projects, Azure Repos 리포지토리 및 Azure Boards를 사용하는 방법에 대한 자세한 지침을 제공합니다.  학습 목표는 개념에서 시작하여 모델링을 거쳐 배포를 수행하는 것입니다.

이 자습서에서는 Microsoft에서 TDSP를 구현하는 방법인 Azure DevOps를 사용합니다. Azure DevOps는 역할 기반 보안, 작업 항목 관리 및 추적, 코드 호스팅, 공유 및 소스 제어를 통합하여 협업을 지원합니다. 또한 이 자습서에서는 Azure [DSVM(Data Science Virtual Machine)](https://aka.ms/dsvm)을 분석 데스크톱으로 사용합니다. 여기에는 많이 사용되는 여러 데이터 과학 도구가 사전 구성되어 있으며 Microsoft 소프트웨어 및 Azure 서비스와 통합되어 있습니다. 

자습서를 사용하여 다른 코드 호스팅, Agile 계획, 개발 도구 및 환경을 사용해 TDSP를 구현할 수 있지만 일부 기능은 사용하지 못할 수 있습니다.

## <a name="structure-of-data-science-groups-and-teams"></a>데이터 과학 그룹 및 팀 구조

기업에서 데이터 과학 기능은 종종 다음 계층 구조로 구성됩니다.

- 데이터 과학 그룹
  - 그룹 내 데이터 과학팀

이러한 구조에는 그룹 리드와 팀 리드가 있습니다. 일반적으로 데이터 과학 프로젝트는 데이터 과학팀에서 수행합니다. 데이터 과학팀에는 프로젝트 리더가 프로젝트 관리 및 거버넌스 작업을 맡고, 개별 데이터 과학자 및 엔지니어가 프로젝트의 데이터 과학 및 데이터 엔지니어링 파트를 수행합니다. 초기 프로젝트 설정 및 거버넌스는 그룹, 팀 또는 프로젝트 리더가 수행합니다.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>4가지 TDSP 역할에 대한 정의 및 작업
데이터 과학 단위가 그룹 내의 팀으로 구성되어 있다는 가정하에, TDSP 담당자에게는 다음과 같은 4개의 고유한 역할이 있습니다.

1. **그룹 관리자**: 기업 내의 전체 데이터 과학 단위를 관리합니다. 데이터 과학 단위에는 여러 팀이 있을 수 있고 각 팀은 고유 비즈니스 직종에서 여러 데이터 과학 프로젝트 작업을 수행합니다. 그룹 관리자는 대리인에게 작업을 위임할 수는 있지만 역할에 관련된 작업은 변경되지 않습니다.
   
2. **팀 리더**: 기업의 데이터 과학 단위 내 팀을 관리합니다. 팀은 여러 명의 데이터 과학자로 구성됩니다. 소규모 데이터 과학 단위의 경우 그룹 관리자와 팀 리더가 동일한 사람일 수 있습니다.
   
3. **프로젝트 리더**: 특정 데이터 과학 프로젝트에서 개별 데이터 과학자의 일상적인 작업을 관리합니다.
   
4. **프로젝트 개별 기여자**: 데이터 과학 프로젝트를 수행하는 데이터 과학자, 비즈니스 분석가, 데이터 엔지니어, 설계자 및 기타 직원입니다.

> [!NOTE]
> 기업의 구조 및 크기에 따라 한 사람이 하나 이상의 역할을 수행하거나 여러 사람이 같은 역할을 수행할 수 있습니다.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>4개의 역할이 수행해야 하는 작업

다음 다이어그램에서는 각 Team Data Science Process 역할이 수행하는 최상위 작업을 보여 줍니다. 이 스키마와 그다음에 나오는 각 TDSP 역할에 대한 보다 자세한 작업 개요를 통해 각자 책임에 따라 필요한 자습서를 선택할 수 있습니다.

![역할 및 작업 개요](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>그룹 관리자 작업

그룹 관리자 또는 지정된 TDSP 시스템 관리자가 TDSP를 채택하려면 다음 작업을 완료합니다.

- 조직 내에 Azure DevOps **조직** 및 그룹 프로젝트를 만듭니다. 
- Azure DevOps 그룹 프로젝트에 **프로젝트 템플릿 리포지토리** 를 만들고 Microsoft TDSP 팀에서 개발한 프로젝트 템플릿 리포지토리에서 시드합니다. Microsoft TDSP 프로젝트 템플릿 리포지토리에서는 다음을 제공합니다.
  - 데이터, 코드 및 문서에 대한 디렉터리를 포함하여 **표준 디렉터리 구조** 를 제공합니다.
  - **표준 문서 템플릿** 세트를 제공하여 효율적인 데이터 과학 프로세스를 안내합니다.
- **유틸리티 리포지토리** 를 만들고 Microsoft TDSP 팀에서 개발한 유틸리티 리포지토리에서 시드합니다. Microsoft의 TDSP 유틸리티 리포지토리는 데이터 과학자의 작업 효율을 높일 수 있는 유용한 유틸리티 세트를 제공합니다. Microsoft 유틸리티 리포지토리에는 대화형 데이터 탐색, 분석, 보고, 기준 모델링 및 보고를 위한 유틸리티가 포함되어 있습니다.
- 조직 계정에 대한 **보안 제어 정책** 을 설정합니다.

자세한 지침은 [데이터 과학팀에 대한 그룹 관리자 작업](group-manager-tasks.md)을 참고하세요.

## <a name="team-lead-tasks"></a>팀 리더 작업

팀 리더 또는 지정된 프로젝트 관리자는 TDSP를 채택하려면 다음 작업을 완료합니다.

- 그룹의 Azure DevOps 조직에 팀 **프로젝트** 를 만듭니다.
- 프로젝트에서 **프로젝트 템플릿 리포지토리** 를 만들고 그룹 관리자 또는 대리자가 설정한 그룹 프로젝트 템플릿 리포지토리에서 시드합니다.
- **팀 유틸리티 리포지토리** 를 만들어 그룹 유틸리티 리포지토리에서 시드하고 리포지토리에 팀별 유틸리티를 추가합니다.
- 선택 사항으로 팀에 유용한 데이터 자산을 저장하는 [Azure 파일 스토리지](https://azure.microsoft.com/services/storage/files/)를 만듭니다. 다른 팀 구성원도 팀의 분석 데스크톱에 이 공유 클라우드 파일 저장소를 탑재할 수 있습니다.
- 선택 사항으로 팀의 **DSVM** 에 Azure 파일 스토리지를 탑재하고 여기에 팀 데이터 자산을 추가합니다.
- 팀 멤버를 추가하고 권한을 구성하여 **보안 제어** 를 설정합니다.

자세한 지침은 [데이터 과학팀에 대한 팀 리더 작업](team-lead-tasks.md)을 참고하세요.


## <a name="project-lead-tasks"></a>프로젝트 리더 작업

프로젝트 리더는 TDSP를 채택하려면 다음 작업을 완료합니다.

- 팀 프로젝트에 **프로젝트 리포지토리** 를 만들고 팀 프로젝트 템플릿 리포지토리에서 시드합니다.
- 선택 사항으로 프로젝트의 데이터 자산을 저장하는 **Azure 파일 스토리지** 를 만듭니다.
- 선택 사항으로 Azure 파일 스토리지를 **DSVM** 에 탑재하고 프로젝트 데이터 자산을 추가합니다.
- 프로젝트 멤버를 추가하고 권한을 구성하여 **보안 제어** 를 설정합니다.

자세한 지침은 [데이터 과학팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)을 참고하세요.

## <a name="project-individual-contributor-tasks"></a>프로젝트 개별 참가자 작업

프로젝트 개별 기여자(일반적으로 데이터 과학자)는 TDSP를 사용하여 다음 작업을 수행합니다.

- 프로젝트 리더가 설정한 **프로젝트 리포지토리** 를 복제합니다.
- 선택 사항으로 팀 및 프로젝트의 공유 **Azure 파일 스토리지** 를 해당 **DSVM**(Data Science Virtual Machine)에 탑재합니다.
- 프로젝트를 실행합니다.

프로젝트에 온보딩하는 자세한 지침은 [데이터 과학팀에 대한 프로젝트 개별 기여자 작업](project-ic-tasks.md)을 참고하세요.

## <a name="data-science-project-execution-workflow"></a>데이터 과학 프로젝트 실행 워크플로

관련 자습서를 수행하여 데이터 과학자, 프로젝트 리더 및 팀 리더는 프로젝트의 전체 과정에 필요한 모든 작업 및 단계를 추적하는 작업 항목을 만들 수 있습니다. Azure Repos를 사용하여 데이터 과학자 간에 협업을 촉진하고, 프로젝트 실행 중에 생성된 아티팩트가 모든 프로젝트 멤버에 의해 버전 제어되고 공유되는지 확인합니다. Azure DevOps를 사용하면 Azure Repos 리포지토리 분기와 Azure Boards 작업 항목을 연결하고 작업 항목에 대해 수행된 작업을 쉽게 추적할 수 있습니다.

다음 그림은 TDSP를 사용하여 프로젝트를 실행하는 워크플로를 간략하게 설명합니다.

![일반적인 데이터 과학 프로젝트 워크플로](./media/roles-tasks/overview-project-execute.png)

워크플로 단계는 세 가지 작업으로 그룹화할 수 있습니다.

- 프로젝트 리더가 스프린트 계획을 수행합니다.
- 데이터 과학자가 `git` 분기에서 아티팩트를 개발하여 작업 항목을 처리합니다.
- 프로젝트 리더 또는 다른 팀 멤버는 코드 검토를 수행하고 작업 분기를 기본 분기에 병합합니다.

프로젝트 실행 워크플로에 대한 자세한 지침은 [데이터 과학 프로젝트의 Agile 개발](agile-development.md)을 참고하세요.

## <a name="tdsp-project-template-repository"></a>TDSP 프로젝트 템플릿 리포지토리

Microsoft TDSP 팀의 [프로젝트 템플릿 리포지토리](https://github.com/Azure/Azure-TDSP-ProjectTemplate)를 사용하여 효율적인 프로젝트 실행 및 협업을 지원합니다. 리포지토리는 사용자 고유의 TDSP 프로젝트에 사용할 수 있는 표준 디렉터리 구조 및 문서 템플릿을 제공합니다.

## <a name="next-steps"></a>다음 단계

Team Data Science Process에서 정의한 역할 및 작업에 대한 자세한 설명을 살펴봅니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학팀에 대한 프로젝트 개별 기여자 작업](project-ic-tasks.md)
