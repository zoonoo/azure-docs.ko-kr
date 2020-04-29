---
title: Team Data Science Process에서 프로젝트 리더의 작업
description: 팀 데이터 과학 프로세스 팀에서 프로젝트 책임자를 위한 작업에 대 한 자세한 연습
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76714421"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>팀 데이터 과학 프로세스의 프로젝트 리드 작업

이 문서에서는 tdsp ( [팀 데이터 과학 프로세스](overview.md) )에서 프로젝트 팀의 리포지토리를 설정 하기 위해 *프로젝트 리더가* 완료 하는 작업을 설명 합니다. TDSP는 클라우드 기반, 예측 분석 솔루션을 효율적으로 실행 하기 위한 구조화 된 작업 시퀀스를 제공 하는 Microsoft에서 개발한 프레임 워크입니다. TDSP는 공동 작업 및 팀 학습을 개선 하는 데 도움이 되도록 설계 되었습니다. TDSP를 표준화 하는 데이터 과학 팀에 대 한 담당자 역할 및 관련 작업에 대 한 개요는 [팀 데이터 과학 프로세스 역할 및 작업](roles-tasks.md)을 참조 하세요.

프로젝트 리더는 TDSP의 특정 데이터 과학 프로젝트에서 개별 데이터 과학자의 일별 활동을 관리 합니다. 다음 다이어그램에서는 프로젝트 리더 태스크에 대 한 워크플로를 보여 줍니다.

![프로젝트 리드 작업 워크플로](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

이 자습서에서는 Step 1: Create project repository 및 Step 2: 초기값 프로젝트 리포지토리에서 팀 ProjectTemplate 리포지토리를 다룹니다. 

3 단계: 프로젝트에 대 한 기능 작업 항목 만들기 및 4 단계: 프로젝트 단계에 대 한 스토리 추가는 [데이터 과학 프로젝트의 Agile 개발](agile-development.md)을 참조 하세요.

5 단계: 저장소/분석 자산 및 공유 만들기 및 사용자 지정 (필요한 경우) [팀 데이터 및 분석 리소스 만들기](team-lead-tasks.md#create-team-data-and-analytics-resources)를 참조 하세요.

6 단계: 프로젝트 리포지토리의 보안 제어를 설정 하는 방법에 대해서는 [팀 멤버 추가 및 권한 구성](team-lead-tasks.md#add-team-members-and-configure-permissions)을 참조 하세요.

> [!NOTE] 
> 이 문서에서는 Microsoft에서 TDSP를 구현 하는 방법 이기 때문에 Azure Repos를 사용 하 여 TDSP 프로젝트를 설정 합니다. 팀에서 다른 코드 호스팅 플랫폼을 사용 하는 경우 프로젝트 리더 작업은 동일 하지만 완료 하는 방법은 다를 수 있습니다.

## <a name="prerequisites"></a>전제 조건

이 자습서에서는 [그룹 관리자](group-manager-tasks.md) 와 [팀 리더가](team-lead-tasks.md) 다음과 같은 리소스 및 권한을 설정 했다고 가정 합니다.

- 데이터 단위에 대 한 Azure DevOps **조직**
- 데이터 과학 팀에 대 한 팀 **프로젝트**
- 팀 템플릿 및 유틸리티 **리포지토리**
- 프로젝트에 대 한 리포지토리를 만들고 편집할 수 있는 조직 계정에 대 한 **사용 권한**

리포지토리를 복제 하 고 로컬 컴퓨터 또는 Data Science Virtual Machine (DSVM)에서 콘텐츠를 수정 하거나 Azure file storage를 설정 하 여 DSVM에 탑재 하려면 다음 검사 목록도 고려해 야 합니다.

- Azure 구독
- Git이 컴퓨터에 설치 되어 있어야 합니다. DSVM을 사용 하는 경우 Git이 사전 설치 됩니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용 하려는 경우 Azure에서 만들어지고 구성 된 Windows 또는 Linux DSVM을 사용 합니다. 자세한 내용 및 지침은 [Data Science Virtual Machine 설명서](/azure/machine-learning/data-science-virtual-machine/)를 참조 하세요.
- Windows DSVM의 경우 컴퓨터에 [GCM (Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 이 설치 되어 있어야 합니다. *README.md* 파일에서 **다운로드 및 설치** 섹션으로 스크롤하고 **최신 설치 관리자**를 선택 합니다. 설치 관리자 페이지에서 *.exe* 설치 관리자를 다운로드 하 여 실행 합니다. 
- Linux DSVM의 경우 DSVM에 설정 되 고 Azure DevOps에 추가 된 SSH 공개 키입니다. 자세한 내용 및 지침은 [platform and tools 부록](platforms-and-tools.md#appendix)에서 **SSH 공개 키 만들기** 섹션을 참조 하세요. 

## <a name="create-a-project-repository-in-your-team-project"></a>팀 프로젝트에서 프로젝트 리포지토리 만들기

팀의 **Myteam** 프로젝트에서 프로젝트 리포지토리를 만들려면 다음을 수행 합니다.

1. *Https:\//\<서버 이름\<>/조직 이름>/\<팀 이름>*(예: **https:\//dev.azure.com/DataScienceUnit/MyTeam**)에서 팀의 프로젝트 **요약** 페이지로 이동한 다음 왼쪽 탐색 모음에서 **리포지토리** 를 선택 합니다. 
   
1. 페이지 맨 위에서 리포지토리 이름을 선택 하 고 드롭다운에서 **새 리포지토리** 를 선택 합니다.
   
   ![새 리포지토리 선택](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. **새 리포지토리 만들기** 대화 상자에서 **Git** 이 **유형**아래에 선택 되어 있는지 확인 합니다. **리포지토리 이름**아래에 *DSProject1* 을 입력 하 고 **만들기**를 선택 합니다.
   
   ![리포지토리 만들기](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. 프로젝트 설정 페이지에서 새 **DSProject1** 리포지토리를 볼 수 있는지 확인 합니다. 
   
   ![프로젝트 설정의 프로젝트 리포지토리](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>팀 템플릿을 프로젝트 리포지토리로 가져오기

프로젝트 리포지토리를 팀 템플릿 리포지토리의 콘텐츠로 채우려면 다음을 수행 합니다.

1. 팀의 프로젝트 **요약** 페이지에서 왼쪽 탐색 영역에 있는 **리포지토리** 를 선택 합니다. 
   
1. 페이지 맨 위에 있는 리포지토리 이름을 선택 하 고 드롭다운에서 **DSProject1** 를 선택 합니다.
   
1. **DSProject1가 비어** 있습니다. 페이지에서 **가져오기**를 선택 합니다. 
   
   ![가져오기 선택](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. **Git 리포지토리 가져오기** 대화 상자에서 **원본 유형**으로 **Git** 을 선택 하 고 **복제 URL**아래에 **teamtemplate** 리포지토리의 url을 입력 합니다. URL은 *https\//\<: 서버 이름>/\<조직 이름>/\<팀 이름>/_git/\<팀 템플릿 리포지토리 이름>* 입니다. 예: **https:\//dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. **가져오기**를 선택합니다. 팀 템플릿 리포지토리의 콘텐츠를 프로젝트 리포지토리로 가져옵니다. 
   
   ![팀 템플릿 리포지토리 가져오기](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

프로젝트의 특정 요구 사항에 맞게 프로젝트 리포지토리의 콘텐츠를 사용자 지정 해야 하는 경우 리포지토리 파일 및 폴더를 추가, 삭제 또는 수정할 수 있습니다. Azure Repos에서 직접 작업 하거나, 로컬 컴퓨터 또는 DSVM에 리포지토리를 복제 하 고, 변경 내용을 적용 하 고, 업데이트를 커밋하고 공유 프로젝트 리포지토리에 푸시할 수 있습니다. [팀 리포지토리의 콘텐츠 사용자 지정](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스에 정의 된 다른 역할 및 작업에 대 한 자세한 설명은 다음 링크를 참조 하세요.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대 한 개별 참여자 작업](project-ic-tasks.md)
