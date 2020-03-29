---
title: Team Data Science Process에서 프로젝트 리더의 작업
description: 팀 데이터 과학 프로세스 팀의 프로젝트 리더를 위한 작업에 대한 자세한 연습
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714421"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>팀 데이터 과학 프로세스의 프로젝트 리드 작업

이 문서에서는 TDSP(팀 [데이터 과학 프로세스)에서](overview.md) 프로젝트 팀에 대한 리포지토리를 설정하기 위해 *프로젝트 리더가* 완료한 작업에 대해 설명합니다. TDSP는 클라우드 기반 예측 분석 솔루션을 효율적으로 실행하기 위해 구조화된 일련의 활동을 제공하는 Microsoft에서 개발한 프레임워크입니다. TDSP는 협업 및 팀 학습을 개선할 수 있도록 설계되었습니다. TDSP에서 표준화하는 데이터 과학 팀의 인사 역할 및 관련 작업에 대한 개요는 [팀 데이터 과학 프로세스 역할 및 작업을](roles-tasks.md)참조하십시오.

프로젝트 리더는 TDSP의 특정 데이터 과학 프로젝트에서 개별 데이터 과학자의 일상 활동을 관리합니다. 다음 다이어그램은 프로젝트 잠재 고객 작업에 대한 워크플로를 보여 주며 다음과 같은 작업을 보여 주며 있습니다.

![프로젝트 잠재 고객 작업 워크플로우](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

이 자습서에서는 1단계: 프로젝트 리포지토리 만들기 및 2단계 프로젝트 저장소 팀 ProjectTemplate 리포지토리에서 시드 프로젝트 리포지토리를 다룹니다. 

3단계: 프로젝트에 대한 기능 작업 항목 만들기 및 4단계: 프로젝트 단계에 대한 스토리 추가, [데이터 과학 프로젝트의 민첩한 개발을](agile-development.md)참조하십시오.

5단계: 스토리지/분석 자산을 만들고 사용자 지정하고 필요한 경우 [팀 데이터 및 분석 리소스 만들기를](team-lead-tasks.md#create-team-data-and-analytics-resources)참조하세요.

6단계: 프로젝트 리포지토리의 보안 제어 설정, [팀 구성원 추가 및 사용 권한 구성](team-lead-tasks.md#add-team-members-and-configure-permissions)을 참조하십시오.

> [!NOTE] 
> 이 문서에서는 Azure Repos를 사용하여 TDSP 프로젝트를 설정합니다. 팀에서 다른 코드 호스팅 플랫폼을 사용하는 경우 프로젝트 잠재 고객 용 작업은 동일하지만 이를 완료하는 방법은 다를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서는 [그룹 관리자와](group-manager-tasks.md) [팀 장이](team-lead-tasks.md) 다음과 같은 리소스 및 권한을 설정했다고 가정합니다.

- 데이터 단위에 대한 Azure DevOps **조직**
- 데이터 과학 팀을 위한 팀 **프로젝트**
- 팀 템플릿 및 유틸리티 **리포지토리**
- 프로젝트에 대한 리포지토리를 만들고 편집할 수 있는 조직 계정에 대한 **권한**

리포지토리를 복제하고 로컬 컴퓨터 또는 DSVM(데이터 과학 가상 머신)의 콘텐츠를 수정하거나 Azure 파일 저장소를 설정하고 DSVM에 탑재하려면 다음 검사 목록도 고려해야 합니다.

- Azure 구독
- 컴퓨터에 설치된 Git. DSVM을 사용하는 경우 Git이 사전 설치되어 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용하려는 경우 Azure에서 만들고 구성한 Windows 또는 Linux DSVM입니다. 자세한 정보 및 지침은 [데이터 과학 가상 시스템 설명서를](/azure/machine-learning/data-science-virtual-machine/)참조하십시오.
- Windows DSVM의 경우 [GCM(GCM)이](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 컴퓨터에 설치되어 있습니다. *README.md* 파일에서 다운로드 및 **설치** 섹션으로 스크롤하여 최신 **설치 관리자를**선택합니다. 설치 관리자 페이지에서 *.exe* 설치 관리자를 다운로드하여 실행합니다. 
- Linux DSVM의 경우 DSVM에 SSH 공개 키가 설정되고 Azure DevOps에 추가됩니다. 자세한 정보 및 지침은 [플랫폼 및 도구 부록의](platforms-and-tools.md#appendix) **SSH 공개 키 만들기** 섹션을 참조하십시오. 

## <a name="create-a-project-repository-in-your-team-project"></a>팀 프로젝트에서 프로젝트 리포지토리 만들기

팀의 **MyTeam** 프로젝트에서 프로젝트 리포지토리를 만들려면 다음을 수행합니다.

1. **Summary** *\/\<\<https:/서버 이름>/조직 이름>/팀 이름>, https: /dev.azure.com/DataScienceUnit/MyTeam 및 왼쪽 탐색에서 Repos를 선택\< *합니다. **\/** **Repos** 
   
1. 페이지 상단에서 리포지토리 이름을 선택한 다음 드롭다운에서 **새 리포지토리를** 선택합니다.
   
   ![새 리포지토리 선택](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. 새 **리포지토리** 만들기 대화 상자에서 **Git이** **Type**에서 선택되어 있는지 확인합니다. **리포지토리 이름**아래에 *DSProject1을* 입력한 다음 **을**선택합니다.
   
   ![리포지토리 만들기](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. 프로젝트 설정 페이지에서 새 **DSProject1** 리포지토리를 볼 수 있는지 확인합니다. 
   
   ![프로젝트 설정의 프로젝트 리포지토리](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>팀 템플릿을 프로젝트 리포지토리로 가져오기

프로젝트 리포지토리를 팀 템플릿 리포지토리의 내용으로 채웁니다.

1. 팀의 프로젝트 **요약** 페이지에서 왼쪽 탐색에서 **리포지토리를** 선택합니다. 
   
1. 페이지 상단에서 리포지토리 이름을 선택하고 드롭다운에서 **DSProject1을** 선택합니다.
   
1. **DSProject1이 비어 있는** 페이지에서 **가져오기를**선택합니다. 
   
   ![가져오기 선택](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. **Git 리포지토리 가져오기** 대화 상자에서 **Git을** **소스 유형으로**선택하고 **Clone URL**아래에 **TeamTemplate** 리포지토리의 URL을 입력합니다. URL은 *\//\<https입니다: 서버\<이름>/\<조직 이름>/ 팀 이름>/_git/팀\<템플릿 리포지토리 이름>*. 예를 들어 **\/https: /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. **가져오기**를 선택합니다. 팀 템플릿 리포지토리의 내용을 프로젝트 리포지토리로 가져옵니다. 
   
   ![팀 템플릿 리포지토리 가져오기](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

프로젝트의 특정 요구 사항에 맞게 프로젝트 리포지토리의 내용을 사용자 지정해야 하는 경우 리포지토리 파일 및 폴더를 추가, 삭제 또는 수정할 수 있습니다. Azure 리포지토리에서 직접 작업하거나 리포지토리를 로컬 컴퓨터 또는 DSVM에 복제하고, 변경하고, 업데이트를 커밋하고 공유 프로젝트 리포지토리에 푸시할 수 있습니다. [팀 리포지토리의 내용을 사용자 지정할](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)때 의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

다음은 팀 데이터 과학 프로세스에서 정의한 다른 역할 및 작업에 대한 자세한 설명에 대한 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀의 개별 기여자 작업](project-ic-tasks.md)
