---
title: Team Data Science Process Team에서 팀 리더의 작업
description: 팀 데이터 과학 프로세스 팀의 팀 리더를 위한 작업에 대한 자세한 연습
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864284"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>팀 데이터 과학 프로세스 팀의 팀 장팀을 위한 작업

이 문서에서는 *팀 이끌이가* 데이터 과학 팀을 위해 완료한 작업에 대해 설명합니다. 팀 리더의 목표는 팀 데이터 과학 프로세스(TDSP)를 표준화하는 협업 팀 환경을 구축하는 것입니다. [Team Data Science Process](overview.md) TDSP는 협업 및 팀 학습을 개선할 수 있도록 설계되었습니다. 

TDSP는 예측 분석 솔루션과 지능형 애플리케이션을 효율적으로 제공하기 위한 민첩하고 반복적인 데이터 과학 방법론입니다. 이 프로세스는 Microsoft와 업계의 모범 사례와 구조를 증류합니다.  목표는 데이터 과학 이니셔티브를 성공적으로 구현하고 분석 프로그램의 이점을 완전히 실현하는 것입니다. TDSP에서 표준화하는 데이터 과학 팀의 인사 역할 및 관련 작업에 대한 개요는 [팀 데이터 과학 프로세스 역할 및 작업을](roles-tasks.md)참조하십시오.

팀 리더는 기업의 데이터 과학 부서에서 여러 데이터 과학자로 구성된 팀을 관리합니다. 데이터 과학 단위의 크기와 구조에 따라 [그룹 관리자와](group-manager-tasks.md) 팀 리더가 같은 사람이거나 작업을 대리로 위임할 수 있습니다. 하지만 작업 자체는 변경되지 않습니다. 

다음 다이어그램에서는 팀 리더가 팀 환경을 설정하기 위해 완료한 작업에 대한 워크플로를 보여 주어집니다.

![팀 리더 작업 워크플로우](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Azure DevOps에서 그룹 조직에서 **팀 프로젝트를** 만듭니다. 
  
1. 기본 팀 리포지토리의 이름을 **TeamUtilities로 바꿉니다.**
  
1. 팀 프로젝트에서 새 **TeamTemplate** 리포지토리를 만듭니다. 
  
1. 그룹의 **그룹유틸리티 및** **그룹프로젝트템플릿** 리포지토리의 내용을 **TeamUtilities** 및 **TeamTemplate** 리포지토리로 가져옵니다. 
  
1. 팀 구성원을 추가하고 해당 권한을 구성하여 **보안 제어를** 설정합니다.
  
1. 필요한 경우 팀 데이터 및 분석 리소스를 만듭니다.
   - **TeamUtilities** 리포지토리에 팀별 유틸리티를 추가합니다. 
   - **Azure 파일 저장소를** 만들어 전체 팀에 유용할 수 있는 데이터 자산을 저장합니다. 
   - Azure 파일 저장소를 팀 리더의 **DSVM(데이터 과학 가상 컴퓨터)에** 탑재하고 데이터 자산을 추가합니다.

다음 자습서에서는 단계를 자세히 설명합니다.

> [!NOTE] 
> 이 문서에서는 Azure DevOps 및 DSVM을 사용하여 TDSP 팀 환경을 설정합니다. 팀에서 다른 코드 호스팅 또는 개발 플랫폼을 사용하는 경우 팀 장부 작업은 동일하지만 이를 완료하는 방법은 다를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서는 [그룹 관리자가](group-manager-tasks.md)다음 리소스 및 권한을 설정했다고 가정합니다.

- 데이터 단위에 대한 Azure DevOps **조직**
- Microsoft TDSP 팀의 프로젝트 템플릿 및 **유틸리티** 리포지토리의 내용으로 채워진 **ProjectTemplate** **GroupProjectTemplate** 및 **GroupUtilities** 리포지토리
- 조직에 대한 프로젝트 및 리포지토리를 만들 수 있는 조직 계정에 대한 권한

리포지토리를 복제하고 로컬 컴퓨터 또는 DSVM에서 콘텐츠를 수정하거나 Azure 파일 저장소를 설정하고 DSVM에 탑재하려면 다음이 필요합니다.

- Azure 구독
- 컴퓨터에 설치된 Git. DSVM을 사용하는 경우 Git이 사전 설치되어 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용하려는 경우 Azure에서 만들고 구성한 Windows 또는 Linux DSVM입니다. 자세한 정보 및 지침은 [데이터 과학 가상 시스템 설명서를](/azure/machine-learning/data-science-virtual-machine/)참조하십시오.
- Windows DSVM의 경우 [GCM(GCM)이](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 컴퓨터에 설치되어 있습니다. *README.md* 파일에서 다운로드 및 **설치** 섹션으로 스크롤하여 최신 **설치 관리자를**선택합니다. 설치 관리자 페이지에서 *.exe* 설치 관리자를 다운로드하여 실행합니다. 
- Linux DSVM의 경우 DSVM에 SSH 공개 키가 설정되고 Azure DevOps에 추가됩니다. 자세한 정보 및 지침은 [플랫폼 및 도구 부록의](platforms-and-tools.md#appendix) **SSH 공개 키 만들기** 섹션을 참조하십시오. 

## <a name="create-a-team-project-and-repositories"></a>팀 프로젝트 및 리포지토리 만들기

이 섹션에서는 그룹의 Azure DevOps 조직에서 다음 리소스를 만듭니다.

- Azure DevOps의 **MyTeam** 프로젝트
- **팀템플릿** 리포지토리
- **팀유틸리티** 리포지토리

이 자습서의 리포지토리 및 디렉터리에 지정된 이름은 대규모 데이터 과학 조직 내에서 자신의 팀에 대해 별도의 프로젝트를 설정하려는 것으로 가정합니다. 그러나 전체 그룹은 그룹 관리자 또는 조직 관리자가 만든 단일 프로젝트에서 작업하도록 선택할 수 있습니다. 그런 다음 모든 데이터 과학 팀이 이 단일 프로젝트에서 리포지토리를 만듭니다. 이 시나리오는 다음과 같은 경우 유효할 수 있습니다.
- 여러 데이터 과학 팀이 없는 소규모 데이터 과학 그룹입니다. 
- 그럼에도 불구하고 그룹 수준의 스프린트 계획과 같은 활동으로 팀 간 협업을 최적화하려는 여러 데이터 과학 팀이 있는 대규모 데이터 과학 그룹입니다. 

팀이 단일 그룹 프로젝트에서 팀별 리포지토리를 갖기로 선택한 경우 팀 리드는 * \<TeamName>템플릿* 및 * \<TeamName>유틸리티와*같은 이름으로 리포지토리를 만들어야 합니다. 예를 들어, *TeamA템플릿* 및 *TeamAUtilities*. 

어쨌든 팀 리드는 팀 구성원에게 설정하고 복제할 템플릿 및 유틸리티 리포지토리를 알려야 합니다. 프로젝트 리더는 [데이터 과학 팀이](project-lead-tasks.md) 별도의 프로젝트 또는 단일 프로젝트에서 프로젝트 리포지토리를 만들기 위해 프로젝트 리드 작업을 수행해야 합니다. 

### <a name="create-the-myteam-project"></a>MyTeam 프로젝트 만들기

팀을 위한 별도의 프로젝트를 만들려면 다음을 수행합니다.

1. 웹 브라우저에서 URL *https:\//\<서버 이름>/조직\<이름>* 그룹의 Azure DevOps 조직 홈 페이지로 이동한 다음 새 **프로젝트를**선택합니다. 
   
   ![새 프로젝트 선택](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. 프로젝트 **만들기** 대화 상자에서 *MyTeam*과 같은 팀 이름을 **프로젝트 이름**아래에 입력한 다음 **고급**을 선택합니다. 
   
1. **버전 제어에서** **Git을**선택하고 **작업 항목 프로세스에서** **Agile을**선택합니다. 그런 다음 **을 선택합니다.** 
   
   ![프로젝트 만들기](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
팀 프로젝트 **요약** 페이지가 열리고 페이지 URL *https:\//\<서버 이름>/조직\<이름\<>/팀 이름>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>MyTeam 기본 리포지토리의 이름을 팀유틸리티로 변경합니다.

1. **MyTeam** 프로젝트 **요약** 페이지에서 **어떤 서비스로 시작하시겠습니까?** **Repos** 
   
   ![리포스 선택](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. **MyTeam** 리포지토리 페이지에서 페이지 상단의 **MyTeam** 리포지토리를 선택한 다음 드롭다운에서 **리포지토리 관리를** 선택합니다. 
   
   ![리포지토리 관리 선택](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. 프로젝트 **설정** 페이지에서 **MyTeam** 리포지토리 옆의 **...를** 선택한 다음 **리포지토리 이름을 바꾸기를**선택합니다. 
   
   ![리포지토리 이름 바꾸기 선택](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. **MyTeam 리포지토리 팝업의 이름을 바꿀** 때 *TeamUtilities를*입력한 다음 **이름을 선택합니다.** 

### <a name="create-the-teamtemplate-repository"></a>팀템플릿 리포지토리 만들기

1. 프로젝트 **설정** 페이지에서 **새 리포지토리를 선택합니다.** 
   
   ![새 리포지토리 선택](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   또는 **MyTeam** 프로젝트 **요약** 페이지의 왼쪽 탐색에서 **리포지토리를** 선택하고 페이지 상단의 리포지토리를 선택한 다음 드롭다운에서 **새 리포지토리를** 선택합니다.
   
1. 새 **리포지토리** 만들기 대화 상자에서 **Git이** **Type**에서 선택되어 있는지 확인합니다. **리포지토리 이름**아래에 *TeamTemplate를* 입력한 다음 **을**선택합니다.
   
   ![리포지토리 만들기](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. 프로젝트 설정 페이지에서 두 리포지토리 **TeamUtilities** 및 **TeamTemplate를** 볼 수 있는지 확인합니다. 
   
   ![두 팀 리포지토리](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>그룹 공통 리포지토리의 내용 가져오기

팀 리포지토리를 그룹 관리자가 설정한 그룹 공통 리포지토리의 내용으로 채우려면 다음을 수행합니다.

1. **MyTeam** 프로젝트 홈 페이지에서 왼쪽 탐색에서 **리포지토리를** 선택합니다. **MyTeam** 템플릿을 찾을 수 없다는 메시지가 나오면 그렇지 않으면 링크를 선택하고 **기본 TeamTemplate 리포지토리로 이동합니다.** 
   
   기본 **TeamTemplate** 리포지토리가 열립니다. 
   
1. **팀템플릿이 비어 있는** 페이지에서 **가져오기를**선택합니다. 
   
   ![가져오기 선택](./media/team-lead-tasks/import-repo.png)
   
1. **Git 리포지토리 가져오기** 대화 상자에서 **Git을** **소스 유형으로**선택하고 **클론 URL**아래에 그룹 공통 템플릿 리포지토리의 URL을 입력합니다. URL은 *https입니다:\//\<서버\<이름>/ 조직\<이름>/_git/>. * 예: *https:\//dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. **가져오기**를 선택합니다. 그룹 템플릿 리포지토리의 내용은 팀 템플릿 리포지토리로 가져옵니다. 
   
   ![가져오기 그룹 공통 템플릿 리포지토리](./media/team-lead-tasks/import-repo-2.png)
   
1. 프로젝트의 **리포지토리** 페이지 맨 위에 있는 Dropdown을 선택하고 **TeamUtilities** 리포지토리를 선택합니다.
   
1. 가져오기 프로세스를 반복하여 그룹 유틸리티 리포지토리(예: *GroupUtilities)의*내용을 **TeamUtilities** 리포지토리로 가져옵니다. 
   
이제 두 팀 리포지토리각각에 해당 그룹 공통 리포지토리의 파일이 포함됩니다. 

### <a name="customize-the-contents-of-the-team-repositories"></a>팀 리포지토리의 내용 사용자 지정

팀의 특정 요구 사항에 맞게 팀 리포지토리의 내용을 사용자 지정하려는 경우 지금 수행할 수 있습니다. 파일을 수정하거나 디렉터리 구조를 변경하거나 파일 및 폴더를 추가할 수 있습니다.

Azure DevOps에서 직접 파일 또는 폴더를 수정, 업로드 또는 만들려면 다음을 수행하십시오.

1. **MyTeam** 프로젝트 **요약** 페이지에서 **리포지토리를**선택합니다. 
   
1. 페이지 상단에서 사용자 지정할 리포지토리를 선택합니다.

1. 리포지토리 디렉터리 구조에서 변경하려는 폴더 또는 파일로 이동합니다. 
   
   - 새 폴더 또는 파일을 만들려면 **새**. 
     
     ![새 파일 만들기](./media/team-lead-tasks/new-file.png)
     
   - 파일을 업로드하려면 **파일 업로드를**선택합니다. 
     
     ![파일 업로드](./media/team-lead-tasks/upload-files.png)
     
   - 기존 파일을 편집하려면 파일로 이동한 다음 **편집을**선택합니다. 
     
     ![파일 편집](./media/team-lead-tasks/edit-file.png)
     
1. 파일을 추가하거나 편집한 후 **커밋**을 선택합니다.
   
   ![변경 내용 커밋](./media/team-lead-tasks/commit.png)

로컬 컴퓨터 또는 DSVM의 리포지토리로 작업하려면 먼저 리포지토리를 로컬 컴퓨터에 복사하거나 *복제한* 다음 변경 내용을 커밋하고 공유 팀 리포지토리에 푸시합니다. 

리포지토리를 복제하려면 다음을 수행합니다.

1. **MyTeam** 프로젝트 **요약** 페이지에서 **리포지토리를**선택하고 페이지 상단에서 복제할 리포지토리를 선택합니다.
   
1. 리포지토리 페이지에서 오른쪽 상단에 있는 **복제를** 선택합니다.
   
1. 명령줄 아래에서 **클론 리포지토리** 대화 상자에서 HTTP 연결에 대한 **HTTPS** 또는 **SSH에 대한 SSH를** 선택하고 복제 URL을 클립보드에 복사합니다. **Command line**
   
   ![복사 복제 URL](./media/team-lead-tasks/clone.png)
   
1. 로컬 컴퓨터에서 다음 디렉터리를 만듭니다.
   
   - 윈도우용: **C:\GitRepos\마이팀**
   - 리눅스용, **$home/gitRepos/마이팀** 
   
1. 만든 디렉터리로 변경합니다.
   
1. Git Bash에서> `git clone <clone URL>` \<명령을 실행합니다. **Clone**
   
   예를 들어 다음 명령 중 하나를 사용하여 **TeamUtilities** 리포지토리를 로컬 컴퓨터의 *MyTeam* 디렉터리로 복제합니다. 
   
   **HTTPS 연결:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 연결:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

리포지토리의 로컬 복제본에서 원하는 변경 내용을 만든 후 변경 내용을 공유 팀 리포지토리에 커밋하고 푸시합니다. 

로컬 **GitRepos\MyTeam\TeamTemplate** 또는 **GitRepos\MyTeam\TeamUtilities** 디렉토리에서 다음 Git Bash 명령을 실행합니다.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Git 리포지토리에 처음 커밋하는 경우 `git commit` 명령을 실행하기 전에 user.name 및 *user.email에* *글로벌* 매개 변수를 구성해야 할 수 있습니다. 다음 두 명령을 실행합니다.
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 여러 Git 리포지토리에 커밋하는 경우 모든 Git 리포지토리에 동일한 이름과 전자 메일 주소를 사용합니다. Power BI 대시보드를 작성하여 여러 리포지토리에서 Git 활동을 추적할 때 동일한 이름과 전자 메일 주소를 사용하는 것이 편리합니다.

## <a name="add-team-members-and-configure-permissions"></a>팀 구성원 추가 및 사용 권한 구성

팀에 멤버를 추가하려면 다음 을 수행합니다.

1. Azure DevOps에서 **MyTeam** 프로젝트 홈 페이지에서 왼쪽 탐색에서 **프로젝트 설정을** 선택합니다. 
   
1. 프로젝트 **설정** 왼쪽 탐색에서 **팀**을 선택한 다음 **팀** 페이지에서 **MyTeam 팀을**선택합니다. 
   
   ![팀 구성](./media/team-lead-tasks/teams.png)
   
1. 팀 **프로필** 페이지에서 **추가를**선택합니다.
   
   ![마이팀 팀에 추가](./media/team-lead-tasks/add-to-team.png)
   
1. 사용자 **및 그룹 추가** 대화 상자에서 그룹에 추가할 구성원을 검색하고 선택한 다음 **변경 내용 저장을**선택합니다. 
   
   ![사용자 및 그룹 추가](./media/team-lead-tasks/add-users.png)
   

팀 멤버에 대한 사용 권한을 구성하려면 다음을 수행합니다.

1. 프로젝트 **설정** 왼쪽 탐색에서 **사용 권한을 선택합니다.** 
   
1. 사용 권한 페이지에서 구성원을 추가할 그룹을 **선택합니다.** 
   
1. 해당 그룹의 페이지에서 **구성원**을 선택한 다음 **에 추가를**선택합니다. 
   
1. 구성원 **초대** 팝업에서 그룹에 추가할 구성원을 검색하고 선택한 다음 **에 추가를**선택합니다. 
   
   ![구성원에게 권한 부여](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>팀 데이터 및 분석 리소스 만들기

이 단계는 선택 사항이지만 데이터 및 분석 리소스를 전체 팀과 공유하면 성능과 비용 상의 이점이 있습니다. 팀 멤버는 공유 리소스에서 프로젝트를 실행하고 예산을 절약하며 보다 효율적으로 공동 작업을 수행할 수 있습니다. Azure 파일 저장소를 만들고 DSVM에 탑재하여 팀 구성원과 공유할 수 있습니다. 

Azure HDInsight 스파크 클러스터와 같은 다른 리소스를 팀과 공유하는 자세한 내용은 [플랫폼 및 도구를](platforms-and-tools.md)참조하십시오. 이 항목에서는 데이터 과학 관점에서 사용자의 요구에 적합한 리소스를 선택하는 방법에 대한 지침과 제품 페이지 및 기타 관련있고 유용한 자습서에 대한 링크를 제공합니다.

>[!NOTE]
> 느리고 비용이 많이 드는 데이터 센터 간에 데이터를 전송하지 않으려면 Azure 리소스 그룹, 저장소 계정 및 DSVM이 모두 동일한 Azure 리전에서 호스팅되는지 확인합니다. 

### <a name="create-azure-file-storage"></a>Azure 파일 저장소 만들기

1. 다음 스크립트를 실행하여 전체 팀에 유용한 데이터 자산에 대한 Azure 파일 저장소를 만듭니다. 스크립트는 Azure 구독 정보를 묻는 메시지가 표시되므로 입력할 준비가 되어 있습니다. 

   - Windows 컴퓨터에서 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Linux 컴퓨터에서 Linux 셸에서 스크립트를 실행합니다.
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. 메시지가 표시되면 Microsoft Azure 계정에 로그인하고 사용하려는 구독을 선택합니다.
   
1. 사용할 저장소 계정을 선택하거나 선택한 구독 아래에 새 계정을 만듭니다. Azure 파일 저장소 이름에 소문자, 숫자 및 하이픈을 사용할 수 있습니다.
   
1. 저장소를 쉽게 설치하고 공유하려면 Y를 입력하거나 *입력을* 눌러 Azure 파일 저장소 정보를 현재 디렉터리에 있는 텍스트 파일에 저장합니다. 이 텍스트 파일을 **TeamTemplate** 리포지토리에 체크 인할 수 있으며, **이상적으로는 Docs\DataDctionaries에서**팀의 모든 프로젝트에 액세스할 수 있습니다. 또한 다음 섹션에서 Azure 파일 저장소를 Azure DSVM에 탑재하려면 파일 정보가 필요합니다. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>로컬 컴퓨터 또는 DSVM에 Azure 파일 저장소 마운트

1. Azure 파일 저장소를 로컬 컴퓨터 또는 DSVM에 탑재하려면 다음 스크립트를 사용합니다.
   
   - Windows 컴퓨터에서 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Linux 컴퓨터에서 Linux 셸에서 스크립트를 실행합니다.
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. 이전 단계에서 Azure 파일 저장소 정보 파일을 저장한 경우 *Y를* 입력하거나 입력을 눌러 계속합니다. 만든 파일의 전체 경로 및 이름을 입력합니다. 
   
   Azure 파일 저장소 정보 파일이 없는 경우 *n*을 입력하고 지침에 따라 구독, Azure 저장소 계정 및 Azure 파일 저장소 정보를 입력합니다.
   
1. 파일 공유를 탑재할 로컬 또는 TDSP 드라이브의 이름을 입력합니다. 화면에 기존 드라이브 이름 목록이 표시됩니다. 아직 존재하지 않는 드라이브 이름을 제공합니다.
   
1. 새 드라이브와 스토리지가 컴퓨터에 성공적으로 장착되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

다음은 팀 데이터 과학 프로세스에서 정의한 다른 역할 및 작업에 대한 자세한 설명에 대한 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀을 위한 개별 기여자 작업 프로젝트](project-ic-tasks.md)
