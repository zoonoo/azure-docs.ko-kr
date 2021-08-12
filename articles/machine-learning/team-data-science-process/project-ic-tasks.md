---
title: Team Data Science Process에서 개별 기여자에 대한 작업
description: 개별 기여자가 데이터 과학 팀 프로젝트에서 수행하는 작업의 자세한 연습입니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001000"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process에서 개별 기여자에 대한 작업

이 항목에서는 ‘개별 기여자’가 TDSP([Team Data Science Process](overview.md))에서 프로젝트를 설정하기 위해 완료하는 작업을 간략하게 설명합니다. 목표는 TDSP를 표준화하는 협업 팀 환경에서 작업하는 것입니다. TDSP는 협업과 팀 학습을 향상하는 데 도움이 되도록 디자인되었습니다. TDSP에서 이 프로세스를 표준화하는 데이터 과학 팀이 처리하는 인력 역할과 관련 작업에 관한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요.

다음 다이어그램에서는 프로젝트 개별 기여자(데이터 과학자)가 팀 환경을 설정하기 위해 완료하는 작업을 보여 줍니다. TDSP에서 데이터 과학 프로젝트를 실행하는 방법에 관한 지침은 [데이터 과학 프로젝트 실행](./agile-development.md)을 참조하세요. 

![개별 기여자 작업](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** 는 프로젝트 팀이 프로젝트 템플릿과 자산을 공유하기 위해 유지 관리하는 리포지토리입니다.
- **TeamUtilities** 는 팀에서 팀을 위해 특별히 유지 관리하는 유틸리티 리포지토리입니다. 
- **GroupUtilities** 는 그룹 전체에서 유용한 유틸리티를 공유하기 위해 그룹에서 유지 관리하는 리포지토리입니다. 

> [!NOTE] 
> 이 문서에서는 Microsoft에서 TDSP를 구현하는 방법인 Azure Repos 및 DSVM(Data Science Virtual Machine)을 사용하여 TDSP 환경을 설정합니다. 팀에서 다른 코드 호스팅 또는 개발 플랫폼을 사용하는 경우 개별 기여자 작업은 동일하지만 완료 방법이 다를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 [그룹 관리자](group-manager-tasks.md), [팀 리더](team-lead-tasks.md), [프로젝트 리더](project-lead-tasks.md)가 다음 리소스와 권한을 설정했다고 가정합니다.

- 데이터 과학 단위에 대한 Azure DevOps **조직**
- 프로젝트 리더가 프로젝트 템플릿과 자산을 공유하기 위해 설정한 **프로젝트 리포지토리**
- 그룹 관리자와 팀 리더가 설정한 **GroupUtilities** 및 **TeamUtilities** 리포지토리(해당하는 경우)
- 팀이나 프로젝트를 위해 공유된 자산에 대해 설정된 Azure **File Storage**(해당하는 경우)
- 프로젝트 리포지토리에서 복제하고 해당 리포지토리에 다시 밀어 넣을 수 있는 **권한** 

리포지토리를 복제하고 로컬 머신이나 DSVM에서 콘텐츠를 수정하거나, Azure File Storage를 DSVM에 탑재하려면 다음 검사 목록을 고려해야 합니다.

- Azure 구독
- 머신에 설치된 Git. DSVM을 사용하는 경우 Git이 미리 설치됩니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용하려는 경우 Azure에 만들어지고 구성된 Windows 또는 Linux DSVM. 자세한 내용과 지침은 [Data Science Virtual Machine 설명서](../data-science-virtual-machine/index.yml)를 참조하세요.
- Windows DSVM의 경우 머신에 설치된 [GCM(Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows). *README.md* 파일에서 **다운로드 및 설치** 섹션으로 아래로 스크롤하고 **최신 설치 프로그램** 을 선택합니다. 설치 프로그램 페이지에서 *.exe* 설치 프로그램을 다운로드하고 실행합니다. 
- Linux DSVM의 경우 DSVM에서 설정되고 Azure DevOps에서 추가된 SSH 퍼블릭 키. 자세한 내용과 지침은 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)의 **SSH 퍼블릭 키 만들기** 섹션을 참조하세요. 
- DSVM에 탑재해야 하는 모든 Azure File Storage의 Azure File Storage 정보 

## <a name="clone-repositories"></a>리포지토리 복제

리포지토리를 로컬로 사용하고 변경 내용을 공유된 팀 및 프로젝트 리포지토리에 밀어 넣으려면 먼저 로컬 머신에 리포지토리를 복사하거나 ‘복제’합니다. 

1. Azure DevOps에서 *https:\//\<server name>/\<organization name>/\<team name>* 에 있는 팀의 프로젝트 요약 페이지로 이동합니다(예: **https:\//dev.azure.com/DataScienceUnit/MyTeam**).
   
1. 왼쪽 탐색 영역에서 **리포지토리** 를 선택하고 페이지 위쪽에서 복제할 리포지토리를 선택합니다.
   
1. 리포지토리 페이지에서 오른쪽 위에 있는 **복제** 를 선택합니다.
   
1. **리포지토리 복제** 대화 상자에서 HTTP 연결에 대해 **HTTPS** 를 선택하고, SSH 연결에 대해 **SSH** 를 선택하고, **명령줄** 아래에 있는 복제 URL을 클립보드에 복사합니다.
   
   ![리포지토리 복제](./media/project-ic-tasks/clone.png)
   
1. 로컬 머신이나 DSVM에서 다음 디렉터리를 만듭니다.
   
   - Windows: **C:\GitRepos**
   - Linux: **$home/GitRepos**
   
1. 만든 디렉터리로 변경합니다.
   
1. Git Bash에서 복제하려는 각 리포지토리에 대해 `git clone <clone URL>` 명령을 실행합니다. 
   
   예를 들어, 다음 명령은 **TeamUtilities** 리포지토리를 로컬 머신의 *MyTeam* 디렉터리에 복제합니다. 
   
   **HTTPS 연결:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 연결:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. 로컬 프로젝트 디렉터리에서 복제된 리포지토리의 폴더를 볼 수 있는지 확인합니다.
   
   ![로컬 리포지토리 폴더 3개](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>DSVM에 Azure File Storage 탑재

팀이나 프로젝트에서 Azure File Storage의 자산을 공유한 경우 File Storage를 로컬 머신이나 DSVM에 탑재합니다. [로컬 머신 또는 DSVM에 Azure File Storage 탑재](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

다음은 Team Data Science Process에서 정의한 기타 역할과 작업에 관한 자세한 설명의 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)