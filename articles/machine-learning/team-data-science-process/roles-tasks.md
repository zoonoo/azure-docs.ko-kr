---
title: Team Data Science Process 역할 및 작업
description: 데이터 과학 그룹의 주요 구성 요소, 직원 역할 및 관련 태스크에 대 한 개요입니다.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/12/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bbc066f4b01a01a589849ad524b58a9b5d9dfae1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260645"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team Data Science Process 역할 및 작업

TDSP (팀 데이터 과학 프로세스)는 Microsoft에서 개발한 프레임 워크로, 예측 분석 솔루션 및 지능형 응용 프로그램을 효율적으로 구축 하는 구조적 방법론을 제공 합니다. 이 문서에서는이 프로세스를 표준화 하는 데이터 과학 팀에 대 한 핵심 인력 역할 및 관련 작업에 대해 간략하게 설명 합니다.

이 소개 문서에서는 전체 데이터 과학 그룹, 데이터 과학 팀 및 TDSP 프로젝트에 대 한 TDSP 환경을 설정 하는 방법에 대 한 자습서에 연결 합니다. 이 자습서에서는 Azure DevOps Projects, Azure Repos 리포지토리 및 Azure Boards agile 계획 도구를 사용 하 여 액세스를 제어 하 고 코드를 호스트 및 공유 하며 팀 작업을 관리 하는 방법에 대 한 자세한 지침을 제공 합니다.

이 자습서에서는 Microsoft에서 TDSP를 구현 하는 방법 이므로 Azure DevOps를 사용 합니다. Azure DevOps는 역할 기반 보안, 작업 항목 관리 및 추적, 코드 호스팅, 공유 및 소스 제어를 통합 하 여 공동 작업을 용이 하 게 합니다. 또한이 자습서에서는 Azure [Data Science Virtual Machine](https://aka.ms/dsvm) (dsvm)를 미리 구성 하 고 Microsoft 소프트웨어 및 Azure 서비스와 통합 된 몇 가지 인기 있는 데이터 과학 도구를 포함 하는 분석 데스크톱으로 사용 합니다. 

자습서를 사용 하 여 다른 코드 호스팅, agile 계획, 개발 도구 및 환경을 사용 하 여 TDSP를 구현할 수 있지만 일부 기능은 사용 하지 못할 수 있습니다.

## <a name="structure-of-data-science-groups-and-teams"></a>데이터 과학 그룹 및 팀의 구조

엔터프라이즈의 데이터 과학 함수는 종종 다음과 같은 계층 구조로 구성 됩니다.

- 데이터 과학 그룹
  - 그룹 내 데이터 과학 팀/초

이러한 구조에는 그룹 잠재 고객 및 팀 리더가 있습니다. 일반적으로 데이터 과학 프로젝트는 데이터 과학 팀에 의해 수행 됩니다. 데이터 과학 팀에는 프로젝트 관리 및 거 버 넌 스 작업, 개별 데이터 과학자 및 엔지니어가 프로젝트의 데이터 과학 및 데이터 엔지니어링 부분을 수행 하기 위한 프로젝트 리더가 있습니다. 초기 프로젝트 설정 및 거 버 넌 스는 그룹, 팀 또는 프로젝트 책임자에 의해 수행 됩니다.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>4 가지 TDSP 역할에 대 한 정의 및 태스크
데이터 과학 단위가 그룹 내의 팀으로 구성 되어 있다고 가정 하 고, TDSP 직원에 게는 다음과 같은 4 개의 고유한 역할이 있습니다.

1. **그룹 관리자**: 기업에서 전체 데이터 과학 단위를 관리 합니다. 데이터 과학 단위에는 여러 팀이 있을 수 있고 각 팀은 고유 비즈니스 직종에서 여러 데이터 과학 프로젝트 작업을 수행합니다. 그룹 관리자는 대리인에게 작업을 위임할 수는 있지만 역할에 관련된 작업은 변경되지 않습니다.
   
2. **팀 리더**: 기업의 데이터 과학 단위에서 팀을 관리 합니다. 팀은 여러 명의 데이터 과학자로 구성됩니다. 소규모 데이터 과학 단위의 경우 그룹 관리자와 팀 리더가 동일한 사람일 수 있습니다.
   
3. **프로젝트 리드**: 특정 데이터 과학 프로젝트에서 개별 데이터 과학자의 일별 활동을 관리 합니다.
   
4. **프로젝트 개별 참가자**: 데이터 과학 프로젝트를 실행 하는 데이터 과학자, 비즈니스 분석가, 데이터 엔지니어, 설계자 및 기타.

> [!NOTE]
> 기업의 구조와 크기에 따라 단일 사용자가 둘 이상의 역할을 하거나 둘 이상의 사용자가 역할을 채울 수 있습니다.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>4 개의 역할로 완료할 작업

다음 다이어그램에서는 각 팀 데이터 과학 프로세스 역할의 최상위 태스크를 보여 줍니다. 이 스키마와 각 TDSP 역할에 대 한 다음과 같은 자세한 개요 작업을 통해 사용자의 책임에 따라 필요한 자습서를 선택할 수 있습니다.

![역할 및 작업 개요](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>그룹 관리자 작업

그룹 관리자나 지정 된 TDSP 시스템 관리자는 TDSP를 채택 하기 위해 다음 작업을 완료 합니다.

- 조직 내에 Azure DevOps **조직** 및 그룹 프로젝트를 만듭니다. 
- Azure DevOps 그룹 프로젝트에서 **프로젝트 템플릿 리포지토리** 를 만들고 Microsoft tdsp 팀에서 개발한 프로젝트 템플릿 리포지토리에서 시드 합니다. Microsoft TDSP 프로젝트 템플릿 리포지토리는 다음을 제공 합니다.
  - 데이터, 코드 및 문서에 대 한 디렉터리를 포함 하 여 **표준화 된 디렉터리 구조**
  - 효율적인 데이터 과학 프로세스를 안내 하는 **표준화 된 문서 템플릿** 집합입니다.
- **유틸리티 리포지토리**를 만들고 Microsoft tdsp 팀에서 개발한 유틸리티 리포지토리에서 시드 합니다. Microsoft의 TDSP 유틸리티 리포지토리는 데이터 과학자의 작업 효율을 높일 수 있는 유용한 유틸리티 집합을 제공 합니다. Microsoft 유틸리티 리포지토리에는 대화형 데이터 탐색, 분석, 보고, 기준 모델링 및 보고를 위한 유틸리티가 포함 되어 있습니다.
- 조직 계정에 대 한 **보안 제어 정책을** 설정 합니다.

자세한 지침은 [데이터 과학 팀에 대 한 그룹 관리자 작업](group-manager-tasks.md)을 참조 하세요.

## <a name="team-lead-tasks"></a>팀 리더 작업

팀 리더 또는 지정 된 프로젝트 관리자는 TDSP를 채택 하기 위해 다음 작업을 완료 합니다.

- 그룹의 Azure DevOps 조직에 팀 **프로젝트** 를 만듭니다.
- 프로젝트에서 **프로젝트 템플릿 리포지토리** 를 만들고 그룹 관리자 또는 대리자에 의해 설정 된 그룹 프로젝트 템플릿 리포지토리에서 시드 합니다.
- **팀 유틸리티 리포지토리**를 만들어 그룹 유틸리티 리포지토리에서 시드 하 고 리포지토리에 팀 특정 유틸리티를 추가 합니다.
- 필요에 따라 팀에 유용한 데이터 자산을 저장 하는 [Azure file storage](https://azure.microsoft.com/services/storage/files/) 를 만듭니다. 다른 팀 구성원도 팀의 분석 데스크톱에 이 공유 클라우드 파일 저장소를 탑재할 수 있습니다.
- 선택적으로 팀의 **Dsvm** 에 Azure file storage를 탑재 하 고 팀 데이터 자산을 추가 합니다.
- 팀 멤버를 추가 하 고 권한을 구성 하 여 **보안 제어** 를 설정 합니다.

자세한 지침은 [데이터 과학 팀에 대 한 팀 리더 작업](team-lead-tasks.md)을 참조 하세요.


## <a name="project-lead-tasks"></a>프로젝트 리더 작업

프로젝트 책임자는 TDSP를 채택 하기 위해 다음 작업을 완료 합니다.

- 팀 프로젝트에 **프로젝트 리포지토리** 를 만들고 프로젝트 템플릿 리포지토리에서 시드 합니다.
- 필요에 따라 프로젝트의 데이터 자산을 저장 하는 **Azure file storage** 를 만듭니다.
- 선택적으로 Azure 파일 저장소를 **Dsvm** 에 탑재 하 고 프로젝트 데이터 자산을 추가 합니다.
- 프로젝트 멤버를 추가 하 고 권한을 구성 하 여 **보안 제어** 를 설정 합니다.

자세한 지침은 [데이터 과학 팀에 대 한 프로젝트 리더 작업](project-lead-tasks.md)을 참조 하세요.

## <a name="project-individual-contributor-tasks"></a>프로젝트 개별 참가자 작업

프로젝트 개별 참가자 (일반적으로 데이터 과학자)는 TDSP를 사용 하 여 다음 작업을 수행 합니다.

- 프로젝트 리더가 설정한 **프로젝트 리포지토리** 를 복제 합니다.
- 필요에 따라 공유 팀 및 project **Azure file storage** 를 **Data Science Virtual Machine** (dsvm)에 탑재 합니다.
- 프로젝트를 실행 합니다.

프로젝트에 등록 하는 방법에 대 한 자세한 내용은 [데이터 과학 팀에 대 한 프로젝트 개별 참가자 작업](project-ic-tasks.md)을 참조 하세요.

## <a name="data-science-project-execution-workflow"></a>데이터 과학 프로젝트 실행 워크플로

관련 자습서를 수행 하 여 데이터 과학자, 프로젝트 책임자 및 팀 리더는 프로젝트의 모든 작업과 단계를 처음부터 끝까지 추적 하는 작업 항목을 만들 수 있습니다. Azure Repos를 사용 하 여 데이터 과학자 간에 공동 작업을 촉진 하 고, 프로젝트 실행 중에 생성 된 아티팩트가 모든 프로젝트 멤버에 의해 버전 제어 되 고 공유 되는지 확인 합니다. Azure DevOps를 사용 하면 Azure Repos 리포지토리 분기와 Azure Boards 작업 항목을 연결 하 고 작업 항목에 대해 수행 된 작업을 쉽게 추적할 수 있습니다.

다음 그림은 프로젝트 실행에 대 한 TDSP 워크플로를 간략히 설명 합니다.

![일반적인 데이터 과학 프로젝트 워크플로](./media/roles-tasks/overview-project-execute.png)

워크플로 단계는 세 가지 작업으로 그룹화 할 수 있습니다.

- 스 프린트 계획을 수행 하는 프로젝트 리드
- 데이터 과학자 분기에서 `git` 작업 항목을 처리 하는 아티팩트를 개발 합니다.
- 프로젝트 책임자 또는 다른 팀 멤버가 코드 검토를 수행 하 고 작업 분기를 마스터 분기에 병합 합니다.

프로젝트 실행 워크플로에 대 한 자세한 지침은 [데이터 과학 프로젝트의 Agile 개발](agile-development.md)을 참조 하세요.

## <a name="tdsp-project-template-repository"></a>TDSP 프로젝트 템플릿 리포지토리

Microsoft TDSP 팀의 [프로젝트 템플릿 리포지토리](https://github.com/Azure/Azure-TDSP-ProjectTemplate) 를 사용 하 여 효율적인 프로젝트 실행 및 공동 작업을 지원 합니다. 리포지토리는 고유한 TDSP 프로젝트에 사용할 수 있는 표준화 된 디렉터리 구조와 문서 템플릿을 제공 합니다.

## <a name="next-steps"></a>다음 단계

Team Data Science Process에서 정의한 역할 및 작업에 대한 자세한 설명을 살펴봅니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀에 대 한 개별 참여자 작업 프로젝트](project-ic-tasks.md)
