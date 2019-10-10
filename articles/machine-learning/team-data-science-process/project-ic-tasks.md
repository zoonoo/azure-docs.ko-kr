---
title: Team Data Science Process에서 개별 기여자에 대한 작업
description: 데이터 과학 팀 프로젝트에서 개별 참가자에 대 한 작업에 대 한 자세한 연습입니다.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/24/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b64d9669c11f15de5e6bd616ff7a79f59b748363
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244303"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process에서 개별 기여자에 대한 작업

이 항목에서는 tdsp ( [팀 데이터 과학 프로세스](overview.md) )에서 프로젝트를 설정 하기 위해 *개별 참가자* 가 완료 하는 작업에 대해 간략히 설명 합니다. 목표는 TDSP를 표준화 하는 공동 작업 팀 환경에서 작업 하는 것입니다. TDSP는 공동 작업 및 팀 학습을 개선 하는 데 도움이 되도록 설계 되었습니다. TDSP를 표준화 하는 데이터 과학 팀에서 처리 하는 인력 역할 및 관련 작업에 대 한 개요는 [팀 데이터 과학 프로세스 역할 및 작업](roles-tasks.md)을 참조 하세요.

다음 다이어그램에서는 개별 참가자 (데이터 과학자)가 팀 환경을 설정 하는 작업을 완료 하는 작업을 보여 줍니다. TDSP에서 데이터 과학 프로젝트를 실행 하는 방법에 대 한 지침은 [데이터 과학 프로젝트 실행](project-execution.md)을 참조 하세요. 

![개별 참가자 작업](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** 는 프로젝트 팀이 프로젝트 템플릿 및 자산을 공유 하기 위해 유지 관리 하는 리포지토리입니다.
- **Teamutilities** 는 팀이 특별히 팀에서 관리 하는 유틸리티 리포지토리입니다. 
- **Grouputilities** 는 그룹이 전체 그룹에서 유용한 유틸리티를 공유 하기 위해 유지 관리 하는 리포지토리입니다. 

> [!NOTE] 
> 이 문서에서는 Microsoft에서 TDSP를 구현 하는 방법 이기 때문에 Azure Repos 및 Data Science Virtual Machine (DSVM)를 사용 하 여 TDSP 환경을 설정 합니다. 팀에서 다른 코드 호스팅 또는 개발 플랫폼을 사용 하는 경우 개별 참여자 작업은 동일 하지만이 작업을 완료 하는 방법은 다를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 [그룹 관리자](group-manager-tasks.md), [팀 리더](team-lead-tasks.md)및 [프로젝트 리더가](project-lead-tasks.md)다음 리소스 및 사용 권한을 설정 했다고 가정 합니다.

- 데이터 과학 단위에 대 한 Azure DevOps **조직**
- 프로젝트에서 설정 된 프로젝트 **리포지토리** 는 프로젝트 템플릿 및 자산을 공유 합니다.
- 그룹 관리자 및 팀 리더가 설정한 **Grouputilities** 및 **teamutilities** 리포지토리 (해당 하는 경우)
- 해당 하는 경우 팀 또는 프로젝트에 대 한 공유 자산을 위한 Azure **파일 저장소** 설정
- 프로젝트 리포지토리로 복제 하 여 다시 푸시할 수 있는 **권한** 

리포지토리를 복제 하 고 로컬 컴퓨터 또는 DSVM에서 콘텐츠를 수정 하거나 Azure file storage를 DSVM에 탑재 하려면 다음이 필요 합니다.

- Azure 구독.
- Git이 컴퓨터에 설치 되어 있어야 합니다. DSVM을 사용 하는 경우 Git이 사전 설치 됩니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용 하려는 경우 Azure에서 만들어지고 구성 된 Windows 또는 Linux DSVM을 사용 합니다. 자세한 내용 및 지침은 [Data Science Virtual Machine 설명서](/azure/machine-learning/data-science-virtual-machine/)를 참조 하세요.
- Windows DSVM의 경우 컴퓨터에 [GCM (Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 이 설치 되어 있어야 합니다. *README.md* 파일에서 **다운로드 및 설치** 섹션으로 스크롤하고 **최신 설치 관리자**를 선택 합니다. 설치 관리자 페이지에서 *.exe* 설치 관리자를 다운로드 하 여 실행 합니다. 
- Linux DSVM의 경우 DSVM에 설정 되 고 Azure DevOps에 추가 된 SSH 공개 키입니다. 자세한 내용 및 지침은 [platform and tools 부록](platforms-and-tools.md#appendix)에서 **SSH 공개 키 만들기** 섹션을 참조 하세요. 
- DSVM에 탑재 해야 하는 모든 Azure 파일 저장소에 대 한 Azure 파일 저장소 정보입니다. 

## <a name="clone-repositories"></a>리포지토리 복제

로컬로 리포지토리를 사용 하 고 변경 내용을 공유 팀 및 프로젝트 리포지토리에 푸시 하려면 먼저 로컬 컴퓨터에 리포지토리를 복사 하거나 *복제* 합니다. 

1. Azure DevOps에서 no__t의 팀 프로젝트 요약 페이지로 이동 합니다 *. https: \/ @-2 @ no__t-3server name >/\<organization 이름 >/\< 팀 이름 >* (예: **https: \//DataScienceUnit/myteam**)로 이동 합니다.
   
1. 왼쪽 탐색 영역에서 **리포지토리** 를 선택 하 고 페이지 맨 위에서 복제할 리포지토리를 선택 합니다.
   
1. 리포지토리 페이지에서 오른쪽 위에 있는 **복제** 를 선택 합니다.
   
1. **리포지토리 복제** 대화 상자에서 HTTP 연결에는 **HTTPS** 를 선택 하 고 Ssh 연결의 경우 **Ssh** 를 선택 하 고 **명령줄** 아래의 복제 URL을 클립보드에 복사 합니다.
   
   ![리포지토리 복제](./media/project-ic-tasks/clone.png)
   
1. 로컬 컴퓨터 또는 DSVM에서 다음 디렉터리를 만듭니다.
   
   - Windows의 경우: **C:\GitRepos**
   - Linux: **$home/gitrepos**
   
1. 만든 디렉터리로 변경 합니다.
   
1. Git Bash에서 복제 하려는 각 리포지토리에 대해-0 @no__t 명령을 실행 합니다. 
   
   예를 들어 다음 명령은 **Teamutilities** 리포지토리를 로컬 컴퓨터의 *myteam* 디렉터리에 복제 합니다. 
   
   **HTTPS 연결:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 연결:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. 로컬 프로젝트 디렉터리에서 복제 된 리포지토리에 대 한 폴더를 볼 수 있는지 확인 합니다.
   
   ![로컬 리포지토리 폴더 3 개](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>DSVM에 Azure file storage 탑재

팀 또는 프로젝트에서 Azure file storage에 공유 된 자산이 있는 경우 파일 저장소를 로컬 컴퓨터 또는 DSVM에 탑재 합니다. [로컬 컴퓨터 또는 DSVM에 Azure file Storage 탑재](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스에 정의 된 다른 역할 및 작업에 대 한 자세한 설명은 다음 링크를 참조 하세요.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)

