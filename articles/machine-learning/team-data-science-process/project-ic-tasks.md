---
title: Team Data Science Process에서 개별 기여자에 대한 작업
description: 데이터 과학 팀 프로젝트의 개별 기여자를 위한 작업에 대한 자세한 연습입니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721254"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process에서 개별 기여자에 대한 작업

이 항목에서는 *개별 기여자가* [TDSP(팀 데이터 과학 프로세스)에서](overview.md) 프로젝트를 설정하기 위해 완료하는 작업에 대해 간략하게 설명합니다. 목표는 TDSP에서 표준화하는 협업 팀 환경에서 작업하는 것입니다. TDSP는 협업 및 팀 학습을 개선할 수 있도록 설계되었습니다. TDSP에서 표준화하는 데이터 과학 팀에서 처리하는 인사 역할 및 관련 작업에 대한 개요는 [팀 데이터 과학 프로세스 역할 및 작업을](roles-tasks.md)참조하십시오.

다음 다이어그램에서는 개별 기여자(데이터 과학자)가 팀 환경을 설정하기 위해 완료된 작업을 보여 주었습니다. TDSP에서 데이터 과학 프로젝트를 실행하는 방법에 대한 지침은 [데이터 과학 프로젝트 실행을](project-execution.md)참조하십시오. 

![개별 기여자 작업](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository는** 프로젝트 팀이 프로젝트 템플릿 및 자산을 공유하기 위해 유지 관리하는 리포지토리입니다.
- **TeamUtilities는** 팀이 팀을 위해 특별히 유지 관리하는 유틸리티 리포지토리입니다. 
- **GroupUtilities는** 그룹이 전체 그룹에서 유용한 유틸리티를 공유하기 위해 유지 관리하는 리포지토리입니다. 

> [!NOTE] 
> 이 문서에서는 Azure 리포지토리와 DSVM(데이터 과학 가상 머신)을 사용하여 TDSP 환경을 설정합니다. 팀에서 다른 코드 호스팅 또는 개발 플랫폼을 사용하는 경우 개별 기여자 작업은 동일하지만 이를 완료하는 방법은 다를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 [그룹 관리자,](group-manager-tasks.md) [팀 리더](team-lead-tasks.md)및 [프로젝트 책임자가](project-lead-tasks.md)다음과 같은 리소스 및 권한을 설정했다고 가정합니다.

- 데이터 과학 단위에 대한 Azure DevOps **조직**
- 프로젝트 리드가 프로젝트 템플릿 및 자산을 공유하도록 설정한 프로젝트 **리포지토리**
- **그룹 유틸리티** 및 **팀유틸리티** 리포지토리(해당되는 경우 그룹 관리자 및 팀 리더가 설정).
- 해당되는 경우 팀 또는 프로젝트에 대한 공유 자산에 대해 설정된 Azure **파일 저장소**
- 프로젝트 리포지토리에서 복제하여 다시 푸시할 수 있는 **권한** 

리포지토리를 복제하고 로컬 컴퓨터 또는 DSVM의 콘텐츠를 수정하거나 Azure 파일 저장소를 DSVM에 탑재하려면 다음 검사 목록을 고려해야 합니다.

- Azure 구독
- 컴퓨터에 설치된 Git. DSVM을 사용하는 경우 Git이 사전 설치되어 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용하려는 경우 Azure에서 만들고 구성한 Windows 또는 Linux DSVM입니다. 자세한 정보 및 지침은 [데이터 과학 가상 시스템 설명서를](/azure/machine-learning/data-science-virtual-machine/)참조하십시오.
- Windows DSVM의 경우 [GCM(GCM)이](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 컴퓨터에 설치되어 있습니다. *README.md* 파일에서 다운로드 및 **설치** 섹션으로 스크롤하여 최신 **설치 관리자를**선택합니다. 설치 관리자 페이지에서 *.exe* 설치 관리자를 다운로드하여 실행합니다. 
- Linux DSVM의 경우 DSVM에 SSH 공개 키가 설정되고 Azure DevOps에 추가됩니다. 자세한 정보 및 지침은 [플랫폼 및 도구 부록의](platforms-and-tools.md#appendix) **SSH 공개 키 만들기** 섹션을 참조하십시오. 
- DSVM에 탑재해야 하는 모든 Azure 파일 저장소에 대한 Azure 파일 저장소 정보입니다. 

## <a name="clone-repositories"></a>복제 리포지토리

리포지토리를 로컬로 사용하여 작업하고 변경 내용을 공유 팀 및 프로젝트 리포지토리로 푸시하려면 먼저 리포지토리를 로컬 컴퓨터에 복사하거나 *복제합니다.* 

1. Azure DevOps에서 팀의 프로젝트 요약 *\//\<페이지(https>/조직\<이름>/팀\<이름>( *예: **https:\//dev.azure.com/DataScienceUnit/MyTeam)** 으로 이동합니다.
   
1. 왼쪽 탐색에서 **리포지토리를** 선택하고 페이지 상단에서 복제할 리포지토리를 선택합니다.
   
1. 리포지토리 페이지에서 오른쪽 상단에 있는 **복제를** 선택합니다.
   
1. 복제 **리포지토리** 대화 상자에서 HTTP 연결에 대한 **HTTPS를** 선택하거나 SSH 연결의 **경우 SSH를** 선택하고 **명령줄** 아래에 있는 복제 URL을 클립보드에 복사합니다.
   
   ![리포지토리 복제](./media/project-ic-tasks/clone.png)
   
1. 로컬 컴퓨터 또는 DSVM에서 다음 디렉터리를 만듭니다.
   
   - 윈도우용: **C:\GitRepos**
   - 리눅스용: **$home/gitRepos**
   
1. 만든 디렉터리로 변경합니다.
   
1. Git Bash에서 복제하려는 `git clone <clone URL>` 각 리포지토리에 대한 명령을 실행합니다. 
   
   예를 들어 다음 명령은 **TeamUtilities** 리포지토리를 로컬 컴퓨터의 *MyTeam* 디렉터리로 복제합니다. 
   
   **HTTPS 연결:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 연결:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. 로컬 프로젝트 디렉터리에서 복제된 리포지토리의 폴더를 볼 수 있는지 확인합니다.
   
   ![세 개의 로컬 리포지토리 폴더](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>DSVM에 Azure 파일 저장소 마운트

팀 또는 프로젝트가 Azure 파일 저장소에 자산을 공유한 경우 파일 저장소를 로컬 컴퓨터 또는 DSVM에 마운트합니다. 로컬 컴퓨터 [또는 DSVM의 Azure 파일 저장소 마운트의 지침을 따릅니다.](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)

## <a name="next-steps"></a>다음 단계

다음은 팀 데이터 과학 프로세스에서 정의한 다른 역할 및 작업에 대한 자세한 설명에 대한 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)

