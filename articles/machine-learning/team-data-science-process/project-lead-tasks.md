---
title: Team Data Science Process에서 프로젝트 리더의 작업
description: Team Data Science Process 팀의 프로젝트 리더 작업에 대한 자세한 연습 과정
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000983"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Team Data Science Process의 프로젝트 및 리더 작업

이 문서에서는 *프로젝트 리더* 가 [TDSP(Team Data Science Process)](overview.md)에서 프로젝트 팀에 대한 리포지토리를 설정하기 위해 완료하는 작업에 대해 설명합니다. TDSP는 Microsoft에서 개발하여 클라우드 기반 예측 분석 솔루션을 효율적으로 실행하며 일련의 구조화된 작업을 제공하는 프레임워크입니다. TDSP는 협업과 팀 학습을 향상하는 데 도움이 되도록 디자인되었습니다. TDSP를 표준화하는 데이터 과학 팀의 인력 역할 및 관련 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요.

프로젝트 리더는 특정 데이터 과학 프로젝트에서 개별 데이터 과학자의 일상적인 작업을 관리합니다. 다음 다이어그램은 프로젝트 리더 작업에 대한 워크플로를 보여줍니다.

![프로젝트 리더 작업 워크플로](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

이 자습서에서는 1단계: 프로젝트 리포지토리 만들기 및 2단계: 팀 ProjectTemplate 리포지토리에서 프로젝트 리포지토리 시드를 다룹니다. 

3단계: 프로젝트에 대한 기능 작업 항목 만들기 및 4단계: 프로젝트 단계에 대한 스토리 추가는 [데이터 과학 프로젝트의 Agile 개발](agile-development.md)을 참조하세요.

5단계: 스토리지/분석 자산 만들기 및 사용자 지정 및 공유는 필요한 경우 [팀 데이터 및 분석 리소스 만들기](team-lead-tasks.md#create-team-data-and-analytics-resources)를 참조하세요.

6단계: 프로젝트 리포지토리의 보안 제어 설정은 [팀 구성원 추가 및 권한 구성](team-lead-tasks.md#add-team-members-and-configure-permissions)을 참조하세요.

> [!NOTE] 
> 이 문서에서는 Azure Repos를 사용하여 TDSP 프로젝트를 설정합니다. 이것이 Microsoft에서 TDSP를 구현하는 방법이기 때문입니다. 팀에서 다른 코드 호스팅을 사용하는 경우 프로젝트 리더 작업은 동일하지만 완료 방법이 다를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 [그룹 관리자](group-manager-tasks.md)와 [팀 리더](team-lead-tasks.md)가 다음과 같은 리소스 및 권한을 설정했다고 가정합니다.

- 데이터 단위에 대한 Azure DevOps **조직**
- 데이터 과학 팀에 대한 팀 **프로젝트**
- 팀 템플릿 및 유틸리티 **리포지토리**
- 프로젝트에 대한 리포지토리를 만들고 편집할 수 있는 조직 계정 **사용 권한**

리포지토리를 복제하고 로컬 컴퓨터 또는 DSVM(Data Science Virtual Machine)에서 콘텐츠를 수정하거나 Azure 파일 스토리지를 설정하고 DSVM에 탑재하려면 다음 검사 목록도 고려해야 합니다.

- Azure 구독
- 컴퓨터에 설치된 Git. DSVM을 사용하는 경우 Git이 미리 설치되어 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용하려는 경우 Azure에 만들어지고 구성된 Windows 또는 Linux DSVM. 자세한 내용과 지침은 [Data Science Virtual Machine 설명서](../data-science-virtual-machine/index.yml)를 참조하세요.
- Windows DSVM의 경우 머신에 설치된 [GCM(Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows). *README.md* 파일에서 **다운로드 및 설치** 섹션으로 스크롤한 다음, **최신 설치 관리자** 를 선택합니다. 설치 프로그램 페이지에서 *.exe* 설치 프로그램을 다운로드하고 실행합니다. 
- Linux DSVM의 경우 SSH 퍼블릭 키가 DSVM에 설정되고 Azure DevOps에서 추가됩니다. 자세한 내용과 지침은 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)의 **SSH 퍼블릭 키 만들기** 섹션을 참조하세요. 

## <a name="create-a-project-repository-in-your-team-project"></a>팀 프로젝트에서 프로젝트 리포지토리 만들기

팀의 **Myteam** 프로젝트에서 프로젝트 리포지토리를 만들려면 다음을 수행 합니다.

1. *https:\//\<server name>/\<organization name>/\<team name>* (예: **https:\//dev.azure.com/DataScienceUnit/MyTeam**)에서 팀의 프로젝트 **요약** 페이지로 이동한 다음 왼쪽 탐색 영역에서 **Repos** 를 선택합니다. 
   
1. 페이지 맨 위에서 리포지토리 이름을 선택한 다음 드롭다운에서 **새 리포지토리** 를 선택합니다.
   
   ![새 리포지토리 선택](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. **새 리포지토리 만들기** 대화 상자의 **유형** 아래에서 **Git** 이 선택되어 있는지 확인합니다. **리포지토리 이름** 아래에 *DSProject1* 을 입력한 다음, **만들기** 를 선택합니다.
   
   ![리포지토리 만들기](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. 프로젝트 설정 페이지에서 새 **DSProject1** 리포지토리를 볼 수 있는지 확인 합니다. 
   
   ![프로젝트 설정의 프로젝트 리포지토리](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>팀 템플릿을 프로젝트 리포지토리로 가져오기

프로젝트 리포지토리를 팀 템플릿 리포지토리의 콘텐츠로 채우려면 다음을 수행 합니다.

1. 팀의 프로젝트 **요약** 페이지의 왼쪽 탐색 영역에서 **Repos** 를 선택합니다. 
   
1. 페이지 맨 위에서 리포지토리 이름을 선택하고 드롭다운에서 **DSProject1** 를 선택합니다.
   
1. **DSProject1가 비어 있습니다** 페이지에서 **가져오기** 를 선택합니다. 
   
   ![가져오기 선택](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. **Git 리포지토리 가져오기** 대화 상자에서 **원본 유형** 으로 **Git** 을 선택하고 **URL 복제** 아래에 **teamtemplate** 리포지토리의 URL을 입력 합니다. 이 URL은 *https:\//\<server name>/\<organization name>/\<team name>/_git/\<team template repository name>* 입니다. 예: **https:\//dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. **가져오기** 를 선택합니다. 팀 템플릿 리포지토리의 콘텐츠를 프로젝트 리포지토리로 가져옵니다. 
   
   ![팀 템플릿 리포지토리 가져오기](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

프로젝트의 특정 요구 사항에 맞게 프로젝트 리포지토리의 콘텐츠를 사용자 지정해야 하는 경우 리포지토리 파일 및 폴더를 추가, 삭제 또는 수정할 수 있습니다. Azure Repos 직접 작업하거나 로컬 컴퓨터 또는 DSVM에 리포지토리를 복제하고, 변경하고, 업데이트를 커밋하고 공유 프로젝트 리포지토리에 푸시할 수 있습니다. [팀 리포지토리의 콘텐츠 사용자 지정](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

다음은 Team Data Science Process에서 정의한 기타 역할과 작업에 관한 자세한 설명의 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 개별 기여자 작업](project-ic-tasks.md)