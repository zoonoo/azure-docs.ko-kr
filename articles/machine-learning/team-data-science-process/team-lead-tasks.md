---
title: Team Data Science Process Team에서 팀 리더의 작업
description: 팀 데이터 과학 프로세스 팀에 대 한 팀 리더 작업의 자세한 연습
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75864284"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>팀 데이터 과학 프로세스 팀에 대 한 팀 리더 작업

이 문서에서는 *팀 리더가* 데이터 과학 팀을 위해 완료 하는 작업을 설명 합니다. 팀 리더의 목표는 tdsp ( [팀 데이터 과학 프로세스](overview.md) )를 표준화 하는 공동 작업 팀 환경을 구축 하는 것입니다. TDSP는 공동 작업 및 팀 학습을 개선 하는 데 도움이 되도록 설계 되었습니다. 

TDSP는 예측 분석 솔루션 및 지능형 응용 프로그램을 효율적으로 제공 하는 민첩 하 고 반복적인 데이터 과학 방법론입니다. 이 프로세스는 Microsoft 및 업계의 모범 사례와 구조를 추출 합니다.  목표는 데이터 과학 이니셔티브의 성공적인 구현 이며 분석 프로그램의 이점을 완전히 실현 하는 것입니다. TDSP를 표준화 하는 데이터 과학 팀에 대 한 담당자 역할 및 관련 작업에 대 한 개요는 [팀 데이터 과학 프로세스 역할 및 작업](roles-tasks.md)을 참조 하세요.

팀 리더는 기업의 데이터 과학 단위에서 여러 데이터 과학자 구성 된 팀을 관리 합니다. 데이터 과학 단위의 크기와 구조에 따라 [그룹 관리자](group-manager-tasks.md) 와 팀 리더가 동일한 사람 이거나 작업을 서로게이트에 위임할 수 있습니다. 하지만 작업 자체는 변경되지 않습니다. 

다음 다이어그램에서는 팀 리더가 팀 환경을 설정 하기 위해 완료 한 작업에 대 한 워크플로를 보여 줍니다.

![팀 리더 작업 워크플로](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Azure DevOps의 그룹 조직에서 **팀 프로젝트** 를 만듭니다. 
  
1. 기본 팀 리포지토리의 이름을 **Teamutilities**로 바꿉니다.
  
1. 팀 프로젝트에 새 **Teamtemplate** 리포지토리를 만듭니다. 
  
1. 그룹의 **Grouputilities** 및 **groupprojecttemplate** 리포지토리의 콘텐츠를 **teamutilities** 및 **teamutilities** 리포지토리로 가져옵니다. 
  
1. 팀 멤버를 추가 하 고 권한을 구성 하 여 **보안 제어** 를 설정 합니다.
  
1. 필요한 경우 팀 데이터 및 분석 리소스를 만듭니다.
   - 팀 특정 유틸리티를 **teamutilities** 리포지토리에 추가 합니다. 
   - 전체 팀에 게 유용한 데이터 자산을 저장 하는 **Azure file storage** 를 만듭니다. 
   - Azure file storage를 팀 리드의 **Data Science Virtual Machine** (dsvm)에 탑재 하 고 여기에 데이터 자산을 추가 합니다.

다음 자습서에서는 이러한 단계를 자세히 설명 합니다.

> [!NOTE] 
> 이 문서에서는 Microsoft에서 TDSP를 구현 하는 방법 이기 때문에 Azure DevOps 및 DSVM을 사용 하 여 TDSP 팀 환경을 설정 합니다. 팀에서 다른 코드 호스팅 또는 개발 플랫폼을 사용 하는 경우 팀 리더 작업은 동일 하지만이를 완료 하는 방법은 다를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 [그룹 관리자](group-manager-tasks.md)에 의해 다음과 같은 리소스 및 사용 권한이 설정 되어 있다고 가정 합니다.

- 데이터 단위에 대 한 Azure DevOps **조직**
- **Groupprojecttemplate** 및 **Grouputilities** 리포지토리 (Microsoft Tdsp 팀의 **projecttemplate** 및 **유틸리티** 리포지토리의 내용으로 채워짐)
- 팀에 대 한 프로젝트 및 리포지토리를 만들 수 있는 조직 계정에 대 한 사용 권한

리포지토리를 복제 하 고 로컬 컴퓨터 또는 DSVM에서 해당 콘텐츠를 수정 하거나 Azure file storage를 설정 하 여 DSVM에 탑재 하려면 다음이 필요 합니다.

- Azure 구독
- Git이 컴퓨터에 설치 되어 있어야 합니다. DSVM을 사용 하는 경우 Git이 사전 설치 됩니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용 하려는 경우 Azure에서 만들어지고 구성 된 Windows 또는 Linux DSVM을 사용 합니다. 자세한 내용 및 지침은 [Data Science Virtual Machine 설명서](/azure/machine-learning/data-science-virtual-machine/)를 참조 하세요.
- Windows DSVM의 경우 컴퓨터에 [GCM (Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 이 설치 되어 있어야 합니다. *README.md* 파일에서 **다운로드 및 설치** 섹션으로 스크롤하고 **최신 설치 관리자**를 선택 합니다. 설치 관리자 페이지에서 *.exe* 설치 관리자를 다운로드 하 여 실행 합니다. 
- Linux DSVM의 경우 DSVM에 설정 되 고 Azure DevOps에 추가 된 SSH 공개 키입니다. 자세한 내용 및 지침은 [platform and tools 부록](platforms-and-tools.md#appendix)에서 **SSH 공개 키 만들기** 섹션을 참조 하세요. 

## <a name="create-a-team-project-and-repositories"></a>팀 프로젝트 및 리포지토리 만들기

이 섹션에서는 그룹의 Azure DevOps 조직에 다음 리소스를 만듭니다.

- Azure DevOps의 **Myteam** 프로젝트
- **Teamtemplate** 리포지토리
- **Teamutilities** 리포지토리

이 자습서의 리포지토리 및 디렉터리에 대해 지정 된 이름은 대규모 데이터 과학 조직 내에서 자신의 팀에 대 한 별도의 프로젝트를 설정 하려는 경우를 가정 합니다. 그러나 전체 그룹은 그룹 관리자 또는 조직 관리자가 만든 단일 프로젝트에서 작동 하도록 선택할 수 있습니다. 그런 다음, 모든 데이터 과학 팀은이 단일 프로젝트로 리포지토리를 만듭니다. 이 시나리오는 다음에 대해 유효 합니다.
- 여러 데이터 과학 팀이 없는 작은 데이터 과학 그룹 
- 여러 데이터 과학 팀을 포함 하는 대규모 데이터 과학 그룹으로, 그러나 그룹 수준 스 프린트 계획과 같은 작업으로 팀 간 공동 작업을 최적화 하려고 합니다. 

팀이 단일 그룹 프로젝트에서 팀 특정 리포지토리를 사용 하도록 선택 하는 경우 팀은 * \<TeamName> 템플릿* 및 * \<TeamName> 유틸리티*와 같은 이름의 리포지토리를 만들어야 합니다. 예: *TeamATemplate* 및 *TeamAUtilities*. 

어떤 경우 든 팀 리더는 팀 멤버가 설정 하 고 복제할 템플릿 및 유틸리티 리포지토리를 알고 있어야 합니다. 프로젝트 책임자는 개별 프로젝트 또는 단일 프로젝트에서 프로젝트 리포지토리를 만들기 위해 [데이터 과학 팀에 대 한 프로젝트 리더 작업](project-lead-tasks.md) 을 따라야 합니다. 

### <a name="create-the-myteam-project"></a>MyTeam 프로젝트 만들기

팀에 대 한 별도의 프로젝트를 만들려면 다음을 수행 합니다.

1. 웹 브라우저에서 URL *https: \/ / \<server name> / \<organization name> *에서 그룹의 Azure devops 조직 홈 페이지로 이동 하 고 **새 프로젝트**를 선택 합니다. 
   
   ![새 프로젝트 선택](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. **프로젝트 만들기** 대화 상자에서 팀 이름 (예: *Myteam*)을 **프로젝트 이름**아래에 입력 한 다음 **고급**을 선택 합니다. 
   
1. **버전 제어**에서 **Git**을 선택 하 고 **작업 항목 프로세스**에서 **Agile**을 선택 합니다. 그런 다음 **만들기**를 선택합니다. 
   
   ![프로젝트 만들기](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
팀 프로젝트 **요약** 페이지가 열리고 페이지 URL *https: \/ / \<server name> / \<organization name> / \<team name> *가 포함 됩니다.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>MyTeam 기본 리포지토리를 TeamUtilities로 이름 바꾸기

1. **Myteam** 프로젝트 **요약** 페이지에서 **어떤 서비스를 시작**하 시겠습니까?에서 **리포지토리**을 선택 합니다. 
   
   ![리포지토리 선택](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. **Myteam** 리포지토리 페이지에서 페이지 맨 위에 있는 **myteam** 리포지토리를 선택 하 고 드롭다운에서 **리포지토리 관리** 를 선택 합니다. 
   
   ![리포지토리 관리 선택](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. **프로젝트 설정** 페이지에서 **myteam** **리포지토리 옆의 ...를** 선택 하 고 **리포지토리 이름 바꾸기**를 선택 합니다. 
   
   ![리포지토리 이름 바꾸기 선택](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. **MyTeam 리포지토리 이름 바꾸기** 팝업에서 *teamutilities*를 입력 한 다음 **이름 바꾸기**를 선택 합니다. 

### <a name="create-the-teamtemplate-repository"></a>TeamTemplate 리포지토리 만들기

1. **프로젝트 설정** 페이지에서 **새 리포지토리를 선택 합니다.** 
   
   ![새 리포지토리 선택](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   또는 **Myteam** 프로젝트 **요약** 페이지의 왼쪽 탐색 영역에서 **리포지토리** 를 선택 하 고 페이지 맨 위에 있는 리포지토리를 선택한 다음 드롭다운에서 **새 리포지토리** 를 선택 합니다.
   
1. **새 리포지토리 만들기** 대화 상자에서 **Git** 이 **유형**아래에 선택 되어 있는지 확인 합니다. **리포지토리 이름**아래에서 *teamtemplate* 을 입력 한 다음 **만들기**를 선택 합니다.
   
   ![리포지토리 만들기](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. 프로젝트 설정 페이지에서 두 리포지토리 **Teamutilities** 및 **teamutilities** 을 볼 수 있는지 확인 합니다. 
   
   ![두 팀 리포지토리](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>그룹 공통 리포지토리의 콘텐츠 가져오기

그룹 관리자가 설정한 그룹 공통 리포지토리의 내용으로 팀 리포지토리를 채우려면 다음을 수행 합니다.

1. **Myteam** 프로젝트 홈 페이지의 왼쪽 탐색 영역에서 **리포지토리** 를 선택 합니다. **Myteam** 템플릿이 없다는 메시지가 표시 되 면 링크를 선택 하 고 **, 그렇지 않으면 기본 teamtemplate 리포지토리로 이동 합니다.** 
   
   기본 **Teamtemplate** 리포지토리가 열립니다. 
   
1. **Teamtemplate이 비어** 있습니다. 페이지에서 **가져오기**를 선택 합니다. 
   
   ![가져오기 선택](./media/team-lead-tasks/import-repo.png)
   
1. **Git 리포지토리 가져오기** 대화 상자에서 **원본 유형**으로 **Git** 을 선택 하 고 **복제 URL**아래에 그룹 공통 템플릿 리포지토리의 url을 입력 합니다. URL은 *https: \/ / \<server name> / \<organization name> /_git/ \<repository name> *입니다. 예: *https: \/ /dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. **가져오기**를 선택합니다. 그룹 템플릿 리포지토리의 콘텐츠를 팀 템플릿 리포지토리로 가져옵니다. 
   
   ![그룹 공통 템플릿 리포지토리 가져오기](./media/team-lead-tasks/import-repo-2.png)
   
1. 프로젝트의 **리포지토리** 페이지 위쪽에서 **teamutilities** 리포지토리를 드롭다운 하 고 선택 합니다.
   
1. 가져오기 프로세스를 반복 하 여 그룹 공통 유틸리티 리포지토리 (예: *Grouputilities*)의 콘텐츠를 **teamutilities** 리포지토리로 가져옵니다. 
   
이제 두 팀 리포지토리에는 해당 그룹 공통 리포지토리의 파일이 포함 됩니다. 

### <a name="customize-the-contents-of-the-team-repositories"></a>팀 리포지토리의 콘텐츠 사용자 지정

팀의 특정 요구 사항에 맞게 팀 리포지토리의 콘텐츠를 사용자 지정 하려는 경우 지금 수행할 수 있습니다. 파일을 수정 하거나, 디렉터리 구조를 변경 하거나, 파일 및 폴더를 추가할 수 있습니다.

Azure DevOps에서 직접 파일 또는 폴더를 수정, 업로드 또는 만들려면 다음을 수행 합니다.

1. **Myteam** 프로젝트 **요약** 페이지에서 **리포지토리**를 선택 합니다. 
   
1. 페이지 맨 위에서 사용자 지정 하려는 리포지토리를 선택 합니다.

1. 리포지토리 디렉터리 구조에서 변경 하려는 폴더 또는 파일로 이동 합니다. 
   
   - 새 폴더 또는 파일을 만들려면 **새로**만들기 옆의 화살표를 선택 합니다. 
     
     ![새 파일 만들기](./media/team-lead-tasks/new-file.png)
     
   - 파일을 업로드 하려면 **파일 업로드**를 선택 합니다. 
     
     ![파일 업로드](./media/team-lead-tasks/upload-files.png)
     
   - 기존 파일을 편집 하려면 파일로 이동한 다음 **편집**을 선택 합니다. 
     
     ![파일 편집](./media/team-lead-tasks/edit-file.png)
     
1. 파일을 추가 하거나 편집한 후 **커밋**을 선택 합니다.
   
   ![변경 내용 커밋](./media/team-lead-tasks/commit.png)

로컬 컴퓨터 또는 DSVM에서 리포지토리로 작업 하려면 먼저 로컬 컴퓨터에 리포지토리를 복사 하거나 *복제* 한 다음 공유 팀 리포지토리에 변경 내용을 커밋하고 푸시합니다. 

리포지토리를 복제 하려면:

1. **Myteam** 프로젝트 **요약** 페이지에서 **리포지토리**를 선택 하 고 페이지 맨 위에서 복제할 리포지토리를 선택 합니다.
   
1. 리포지토리 페이지에서 오른쪽 위에 있는 **복제** 를 선택 합니다.
   
1. **리포지토리 복제** 대화 상자의 **명령줄**에서 HTTP 연결에는 **HTTPS** 를, ssh 연결에는 **ssh** 를 선택 하 고 복제 URL을 클립보드에 복사 합니다.
   
   ![복제 URL 복사](./media/team-lead-tasks/clone.png)
   
1. 로컬 컴퓨터에서 다음 디렉터리를 만듭니다.
   
   - Windows: **C:\GitRepos\MyTeam**
   - Linux의 경우 **/gitrepos/myteam를 $home** 합니다. 
   
1. 만든 디렉터리로 변경 합니다.
   
1. Git Bash에서 명령을 실행 합니다 `git clone <clone URL>` \<clone URL> . 여기서은 **복제** 대화 상자에서 복사한 URL입니다.
   
   예를 들어 다음 명령 중 하나를 사용 하 여 **Teamutilities** 리포지토리를 로컬 컴퓨터의 *myteam* 디렉터리에 복제 합니다. 
   
   **HTTPS 연결:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 연결:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

리포지토리의 로컬 복제본에서 원하는 사항을 변경한 후 변경 내용을 공유 팀 리포지토리에 커밋하고 푸시합니다. 

로컬 **GitRepos\MyTeam\TeamTemplate** 또는 **GitRepos\MyTeam\TeamUtilities** 디렉터리에서 다음 Git Bash 명령을 실행 합니다.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Git 리포지토리에 처음 커밋하는 경우 명령을 실행 하기 전에 전역 매개 변수 *user.name* 및 *user. email* 을 구성 해야 할 수 있습니다. `git commit` 다음 두 명령을 실행합니다.
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 여러 Git 리포지토리에 커밋하는 경우 모든 Git 리포지토리에 동일한 이름 및 전자 메일 주소를 사용 합니다. 여러 리포지토리에서 Git 작업을 추적 하기 위해 Power BI 대시보드를 빌드할 때 동일한 이름 및 전자 메일 주소를 사용 하는 것이 편리 합니다.

## <a name="add-team-members-and-configure-permissions"></a>팀 멤버 추가 및 사용 권한 구성

팀에 멤버를 추가 하려면 다음을 수행 합니다.

1. Azure DevOps의 **Myteam** 프로젝트 홈 페이지에서 왼쪽 탐색 영역에 있는 **프로젝트 설정** 을 선택 합니다. 
   
1. **프로젝트 설정** 왼쪽 탐색 영역에서 **팀**을 선택 하 고 **팀** 페이지에서 **myteam 팀**을 선택 합니다. 
   
   ![팀 구성](./media/team-lead-tasks/teams.png)
   
1. **팀 프로필** 페이지에서 **추가**를 선택 합니다.
   
   ![MyTeam 팀에 추가](./media/team-lead-tasks/add-to-team.png)
   
1. **사용자 및 그룹 추가** 대화 상자에서 그룹에 추가할 멤버를 검색 하 고 선택한 다음 **변경 내용 저장**을 선택 합니다. 
   
   ![사용자 및 그룹 추가](./media/team-lead-tasks/add-users.png)
   

팀 멤버에 대 한 사용 권한을 구성 하려면:

1. **프로젝트 설정** 왼쪽 탐색 영역에서 **사용 권한**을 선택 합니다. 
   
1. **사용 권한** 페이지에서 멤버를 추가 하려는 그룹을 선택 합니다. 
   
1. 해당 그룹에 대 한 페이지에서 **멤버**를 선택 하 고 **추가**를 선택 합니다. 
   
1. **멤버 초대** 팝업에서 그룹에 추가할 멤버를 검색 하 고 선택한 다음, **저장**을 선택 합니다. 
   
   ![멤버에 사용 권한 부여](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>팀 데이터 및 분석 리소스 만들기

이 단계는 선택 사항 이지만 전체 팀과 데이터 및 분석 리소스를 공유 하면 성능과 비용 이점이 있습니다. 팀 멤버는 공유 리소스에서 해당 프로젝트를 실행 하 고, 예산을 절약 하 고, 더 효율적으로 공동 작업할 수 있습니다. Azure file storage를 만들고 DSVM에 탑재 하 여 팀 멤버와 공유할 수 있습니다. 

Azure HDInsight Spark 클러스터와 같은 다른 리소스를 팀과 공유 하는 방법에 대 한 자세한 내용은 [플랫폼 및 도구](platforms-and-tools.md)를 참조 하세요. 이 항목에서는 요구 사항에 적합 한 리소스 선택에 대 한 데이터 과학 관점의 지침과 제품 페이지 및 기타 관련 된 자습서에 대 한 링크를 제공 합니다.

>[!NOTE]
> 데이터 센터 간에 데이터를 전송 하는 것이 느리고 비용이 많이 드는 것을 방지 하려면 Azure 리소스 그룹, 저장소 계정 및 DSVM이 모두 동일한 Azure 지역에 호스트 되는지 확인 합니다. 

### <a name="create-azure-file-storage"></a>Azure 파일 저장소 만들기

1. 다음 스크립트를 실행 하 여 전체 팀에 게 유용한 데이터 자산에 대 한 Azure file storage를 만듭니다. 스크립트에서 Azure 구독 정보를 입력 하 라는 메시지가 표시 되 면를 입력 합니다. 

   - Windows 컴퓨터의 PowerShell 명령 프롬프트에서 스크립트를 실행 합니다.
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Linux 컴퓨터에서 Linux 셸에서 스크립트를 실행 합니다.
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. 메시지가 표시 되 면 Microsoft Azure 계정에 로그인 하 고 사용 하려는 구독을 선택 합니다.
   
1. 사용할 저장소 계정을 선택 하거나 선택한 구독에서 새 계정을 만듭니다. Azure 파일 저장소 이름에 소문자, 숫자 및 하이픈을 사용할 수 있습니다.
   
1. 저장소를 쉽게 탑재 하 고 공유 하려면 Enter 키를 누르거나 *Y* 를 입력 하 여 Azure 파일 저장소 정보를 현재 디렉터리의 텍스트 파일에 저장 합니다. **Docs\DataDictionaries**아래에 있는 **teamtemplate** 리포지토리에이 텍스트 파일을 체크 인할 수 있으므로 팀의 모든 프로젝트에서 액세스할 수 있습니다. 또한 다음 섹션에서 azure DSVM에 Azure file storage를 탑재 하는 파일 정보가 필요 합니다. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>로컬 컴퓨터 또는 DSVM에 Azure file storage 탑재

1. Azure 파일 저장소를 로컬 컴퓨터 또는 DSVM에 탑재 하려면 다음 스크립트를 사용 합니다.
   
   - Windows 컴퓨터의 PowerShell 명령 프롬프트에서 스크립트를 실행 합니다.
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Linux 컴퓨터에서 Linux 셸에서 스크립트를 실행 합니다.
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. 이전 단계에서 Azure file storage 정보 파일을 저장 한 경우 Enter 키를 누르거나 *Y* 를 입력 하 여 계속 합니다. 만든 파일의 전체 경로와 이름을 입력 합니다. 
   
   Azure 파일 저장소 정보 파일이 없으면 *n*을 입력 하 고 지침에 따라 구독, azure storage 계정 및 azure 파일 저장소 정보를 입력 합니다.
   
1. 파일 공유를 탑재할 로컬 또는 TDSP 드라이브의 이름을 입력 합니다. 화면에 기존 드라이브 이름 목록이 표시 됩니다. 아직 존재 하지 않는 드라이브 이름을 제공 하십시오.
   
1. 컴퓨터에 새 드라이브 및 저장소가 성공적으로 탑재 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스에 정의 된 다른 역할 및 작업에 대 한 자세한 설명은 다음 링크를 참조 하세요.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀에 대 한 개별 참여자 작업 프로젝트](project-ic-tasks.md)
