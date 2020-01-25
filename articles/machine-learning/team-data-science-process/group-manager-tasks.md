---
title: Team Data Science Process 그룹 관리자 작업
description: 그룹 관리자가 데이터 과학 팀 프로젝트에서 완료 하는 작업의이 자세한 연습을 수행 합니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721356"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team Data Science Process 그룹 관리자 작업

이 문서에서는 데이터 과학 조직에 대해 *그룹 관리자* 가 완료 하는 태스크에 대해 설명 합니다. 그룹 관리자는 기업에서 전체 데이터 과학 단위를 관리 합니다. 데이터 과학 단위에는 여러 팀이 있을 수 있으며, 각 팀은 고유한 비즈니스 감축할에서 여러 데이터 과학 프로젝트에 대해 작동 합니다. 그룹 관리자의 목표는 tdsp ( [팀 데이터 과학 프로세스](overview.md) )를 표준화 하는 공동 작업 그룹 환경을 설정 하는 것입니다. TDSP를 표준화 하는 데이터 과학 팀에서 처리 하는 모든 인력 역할 및 관련 작업에 대 한 개요는 [팀 데이터 과학 프로세스 역할 및 작업](roles-tasks.md)을 참조 하세요.

다음 다이어그램에서는 6 가지 기본 그룹 관리자 설치 작업을 보여 줍니다. 그룹 관리자는 작업을 서로게이트가 위임할 수 있지만 역할과 관련 된 작업은 변경 되지 않습니다.

![그룹 관리자 태스크](./media/group-manager-tasks/tdsp-group-manager.png)

1. 그룹에 대 한 **Azure DevOps 조직을** 설정 합니다.
2. Azure DevOps 조직에서 기본 **Groupcommon 프로젝트** 를 만듭니다.
3. Azure Repos에서 **Groupprojecttemplate** 리포지토리를 만듭니다.
4. Azure Repos에서 **Grouputilities** 리포지토리를 만듭니다.
5. Microsoft TDSP 팀의 **Projecttemplate** 및 **유틸리티** 리포지토리의 콘텐츠를 그룹 공용 리포지토리로 가져옵니다.
6. 그룹에 액세스할 수 있도록 팀 멤버에 대 한 **멤버 자격** 및 **사용 권한을** 설정 합니다.

다음 자습서에서는 이러한 단계를 자세히 설명 합니다. 

> [!NOTE] 
> 이 문서에서는 Microsoft에서 TDSP를 구현 하는 방법 이기 때문에 Azure DevOps를 사용 하 여 TDSP 그룹 환경을 설정 합니다. 그룹이 다른 코드 호스팅 또는 개발 플랫폼을 사용 하는 경우 그룹 관리자의 작업은 동일 하지만이를 완료 하는 방법은 다를 수 있습니다.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Azure DevOps에서 조직 및 프로젝트 만들기

1. [Visualstudio.microsoft.com](https://visualstudio.microsoft.com)로 이동 하 여 오른쪽 위에 있는 **로그인** 을 선택 하 고 Microsoft 계정에 로그인 합니다. 
   
   ![Microsoft 계정으로 로그인](./media/group-manager-tasks/signinvs.png)
   
   Microsoft 계정 없는 경우 **지금 등록**을 선택 하 고 Microsoft 계정 만든 다음이 계정을 사용 하 여 로그인 합니다. 조직에 Visual Studio 구독이 있는 경우 해당 구독의 자격 증명을 사용 하 여 로그인 합니다.
   
1. Azure DevOps 페이지의 오른쪽 위에서 로그인 한 후에 **새 조직 만들기**를 선택 합니다.
   
   ![새 조직 만들기](./media/group-manager-tasks/create-organization.png)
   
1. 서비스 약관, 개인 정보 취급 방침 및 규정 준수에 동의 하 라는 메시지가 표시 되 면 **계속**을 선택 합니다.
   
1. 등록 대화 상자에서 Azure DevOps 조직의 이름을 지정 하 고 호스트 지역 할당을 수락 하거나 드롭다운 및 다른 지역을 선택 합니다. 그런 다음, **계속**을 선택합니다. 

1. **시작할 프로젝트 만들기**에서 *groupcommon*을 입력 하 고 **프로젝트 만들기**를 선택 합니다. 
   
   ![프로젝트 만들기](./media/group-manager-tasks/create-project.png)

**Groupcommon** 프로젝트 **요약** 페이지가 열립니다. 페이지 URL은 *https:\//\<servername >/\<조직 이름 >/Sgcommon*입니다.

![프로젝트 요약 페이지](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>그룹 공통 리포지토리 설정

Azure Repos는 그룹에 대해 다음과 같은 유형의 리포지토리를 호스팅합니다.

- **그룹 공통 리포지토리**: 데이터 과학 단위 내의 여러 팀이 여러 데이터 과학 프로젝트에 대해 채택할 수 있는 범용 리포지토리입니다. 
- **팀 리포지토리**: 데이터 과학 단위 내의 특정 팀에 대 한 리포지토리입니다. 이러한 리포지토리는 팀의 요구에 따라 달라 지 며 해당 팀 내의 여러 프로젝트에 사용 될 수 있지만 데이터 과학 단위 내의 여러 팀에서 사용할 수 있을 정도로 일반적이 지 않습니다.
- **프로젝트 리포지토리**: 특정 프로젝트에 대 한 리포지토리입니다. 이러한 리포지토리는 팀 내의 여러 프로젝트 또는 데이터 과학 단위의 다른 팀에 대해 일반적으로 충분 하지 않을 수 있습니다.

프로젝트에서 그룹 공통 리포지토리를 설정 하려면 다음을 수행 합니다. 
- 기본 **Groupcommon** 리포지토리를 **groupprojecttemplate** 으로 이름 바꾸기
- 새 **Grouputilities** 리포지토리 만들기

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>기본 프로젝트 리포지토리를 GroupProjectTemplate으로 이름 바꾸기

기본 **Groupcommon** 프로젝트 리포지토리의 이름을 **groupprojecttemplate**으로 변경 하려면 다음을 수행 합니다.

1. **Groupcommon** 프로젝트 **요약** 페이지에서 **리포지토리**를 선택 합니다. 이 작업을 수행 하면 현재 비어 있는 GroupCommon 프로젝트의 기본 **groupcommon** 리포지토리로 이동 합니다.
   
1. 페이지 맨 위에서 **Groupcommon** 옆의 화살표를 드롭다운 하 고 **리포지토리 관리**를 선택 합니다.
   
   ![리포지토리 관리](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. **프로젝트 설정** 페이지에서 **groupcommon**옆의 ...를 선택 하 고 **리포지토리 이름 바꾸기**를 **선택 합니다.** 
   
   ![...를 선택 합니다. 그런 다음 리포지토리 이름 바꾸기를 선택 합니다.](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. **GroupCommon 리포지토리 이름 바꾸기** 팝업에서 *groupprojecttemplate*을 입력 한 다음 **이름 바꾸기**를 선택 합니다. 
   
   ![리포지토리 이름 바꾸기](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>GroupUtilities 리포지토리 만들기

**Grouputilities** 리포지토리를 만들려면 다음을 수행 합니다.

1. **Groupcommon** 프로젝트 **요약** 페이지에서 **리포지토리**를 선택 합니다. 
   
1. 페이지 맨 위에서 **Groupprojecttemplate** 옆의 화살표를 드롭다운 하 고 **새 리포지토리**를 선택 합니다.
   
   ![새 리포지토리 선택](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. **새 리포지토리 만들기** 대화 상자에서 **유형**으로 **Git** 를 선택 하 고, **저장소 이름**으로 *grouputilities* 를 입력 한 다음, **만들기**를 선택 합니다.
   
   ![GroupUtilities 리포지토리 만들기](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. **프로젝트 설정** 페이지의 왼쪽 탐색 영역에서 **리포지토리** 아래에 있는 **리포지토리** 를 선택 하 여 두 그룹 리포지토리: **groupprojecttemplate** 및 **grouputilities**를 확인 합니다.
   
   ![두 그룹 리포지토리](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Microsoft TDSP 팀 리포지토리 가져오기

자습서의이 부분에서는 Microsoft TDSP 팀에서 관리 하는 **Projecttemplate** 의 내용과 **유틸리티** 리포지토리를 **Groupprojecttemplate** 및 **grouputilities** 리포지토리로 가져옵니다. 

TDSP 팀 리포지토리를 가져오려면:

1. **Groupcommon** 프로젝트 홈 페이지의 왼쪽 탐색 영역에서 **리포지토리** 를 선택 합니다. 기본 **Groupprojecttemplate** 리포지토리가 열립니다. 
   
1. **Groupprojecttemplate이 비어 있음** 페이지에서 **가져오기**를 선택 합니다. 
   
   ![가져오기 선택](./media/group-manager-tasks/import-repo.png)
   
1. **Git 리포지토리 가져오기** 대화 상자에서 **원본 유형**으로 **Git** 을 선택 하 고 **복제 URL**에 대해 *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* 을 입력 합니다. 그런 다음 **가져오기**를 선택 합니다. Microsoft TDSP 팀 ProjectTemplate 리포지토리의 콘텐츠를 GroupProjectTemplate 리포지토리로 가져옵니다. 
   
   ![Microsoft TDSP 팀 리포지토리 가져오기](./media/group-manager-tasks/import-repo-2.png)
   
1. **리포지토리** 페이지의 맨 위에서 드롭다운 하 고 **grouputilities** 리포지토리를 선택 합니다.
   
1. 가져오기 프로세스를 반복 하 여 Microsoft TDSP 팀 **유틸리티** 리포지토리의 *https:\//github.com/Azure/Azure-TDSP-Utilities.git*의 콘텐츠를 **grouputilities** 리포지토리로 가져옵니다. 
   
이제 두 개의 그룹 리포지토리에는 Microsoft TDSP 팀의 해당 하는 리포지토리에서 모든 파일이 포함 *됩니다.* 

## <a name="customize-the-contents-of-the-group-repositories"></a>그룹 리포지토리의 콘텐츠 사용자 지정

그룹의 특정 요구 사항에 맞게 그룹 리포지토리의 콘텐츠를 사용자 지정 하려는 경우 지금 수행할 수 있습니다. 파일을 수정 하거나, 디렉터리 구조를 변경 하거나, 그룹이 개발한 파일을 추가 하거나 그룹에 유용 하 게 사용할 수 있습니다.

### <a name="make-changes-in-azure-repos"></a>Azure Repos에서 변경

리포지토리 콘텐츠를 사용자 지정 하려면:

1. **Groupcommon** 프로젝트 **요약** 페이지에서 **리포지토리**를 선택 합니다. 
   
1. 페이지 맨 위에서 사용자 지정 하려는 리포지토리를 선택 합니다.

1. 리포지토리 디렉터리 구조에서 변경 하려는 폴더 또는 파일로 이동 합니다. 
   
   - 새 폴더 또는 파일을 만들려면 **새로**만들기 옆의 화살표를 선택 합니다. 
     
     ![새 파일 만들기](./media/group-manager-tasks/new-file.png)
     
   - 파일을 업로드 하려면 **파일 업로드**를 선택 합니다. 
     
     ![파일 업로드](./media/group-manager-tasks/upload-files.png)
     
   - 기존 파일을 편집 하려면 파일로 이동한 다음 **편집**을 선택 합니다. 
     
     ![파일 편집](./media/group-manager-tasks/edit-file.png)
     
1. 파일을 추가 하거나 편집한 후 **커밋**을 선택 합니다.
   
   ![변경 내용 커밋](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>로컬 컴퓨터 또는 DSVM을 사용 하 여 변경

로컬 컴퓨터 또는 DSVM을 사용 하 여 변경 하 고 변경 내용을 그룹 리포지토리에 푸시 하려면 Git 및 Dsvm을 사용 하기 위한 필수 구성 요소가 있는지 확인 합니다.

- DSVM을 만들려면 Azure 구독을 선택 합니다.
- Git이 컴퓨터에 설치 되어 있어야 합니다. DSVM을 사용 하는 경우 Git이 사전 설치 됩니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용 하려는 경우 Azure에서 만들어지고 구성 된 Windows 또는 Linux DSVM을 사용 합니다. 자세한 내용 및 지침은 [Data Science Virtual Machine 설명서](/azure/machine-learning/data-science-virtual-machine/)를 참조 하세요.
- Windows DSVM의 경우 컴퓨터에 [GCM (Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 이 설치 되어 있어야 합니다. *README.md* 파일에서 **다운로드 및 설치** 섹션으로 스크롤하고 **최신 설치 관리자**를 선택 합니다. 설치 관리자 페이지에서 *.exe* 설치 관리자를 다운로드 하 여 실행 합니다. 
- Linux DSVM의 경우 DSVM에 설정 되 고 Azure DevOps에 추가 된 SSH 공개 키입니다. 자세한 내용 및 지침은 [platform and tools 부록](platforms-and-tools.md#appendix)에서 **SSH 공개 키 만들기** 섹션을 참조 하세요. 

먼저 로컬 컴퓨터에 리포지토리를 복사 하거나 *복제* 합니다. 
   
1. **Groupcommon** 프로젝트 **요약** 페이지에서 **리포지토리**를 선택 하 고 페이지 맨 위에서 복제할 리포지토리를 선택 합니다.
   
1. 리포지토리 페이지에서 오른쪽 위에 있는 **복제** 를 선택 합니다.
   
1. **리포지토리 복제** 대화 상자에서 HTTP 연결에는 **HTTPS** 를 선택 하 고 Ssh 연결의 경우 **Ssh** 를 선택 하 고 **명령줄** 아래의 복제 URL을 클립보드에 복사 합니다.
   
   ![리포지토리 복제](./media/group-manager-tasks/clone.png)
   
1. 로컬 컴퓨터에서 다음 디렉터리를 만듭니다.
   
   - Windows: **C:\GitRepos\GroupCommon**
   - Linux의 경우 홈 디렉터리의 **$/GitRepos/GroupCommon** 
   
1. 만든 디렉터리로 변경 합니다.
   
1. Git Bash에서 명령을 실행 `git clone <clone URL>.`
   
   예를 들어 다음 명령 중 하나는 로컬 컴퓨터의 *Grouputilities* 디렉터리에 **grouputilities** 리포지토리를 복제 하는 것입니다. 
   
   **HTTPS 연결:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH 연결:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

리포지토리의 로컬 복제본에서 원하는 변경 작업을 수행한 후에는 변경 내용을 공유 그룹 공용 리포지토리에 푸시할 수 있습니다. 

로컬 **Groupprojecttemplate** 또는 **grouputilities** 디렉터리에서 다음 Git Bash 명령을 실행 합니다.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Git 리포지토리에 처음 커밋하는 경우 `git commit` 명령을 실행 하기 전에 전역 매개 변수 *user.name* 및 *user. email* 을 구성 해야 할 수 있습니다. 다음 두 명령을 실행합니다.
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 여러 Git 리포지토리에 커밋하는 경우 모든 Git 리포지토리에 동일한 이름 및 전자 메일 주소를 사용 합니다. 여러 리포지토리에서 Git 작업을 추적 하기 위해 Power BI 대시보드를 빌드할 때 동일한 이름 및 전자 메일 주소를 사용 하는 것이 편리 합니다.

## <a name="add-group-members-and-configure-permissions"></a>그룹 구성원 추가 및 사용 권한 구성

그룹에 구성원을 추가 하려면:

1. Azure DevOps의 **Groupcommon** 프로젝트 홈 페이지에서 왼쪽 탐색 영역에 있는 **프로젝트 설정** 을 선택 합니다. 
   
1. **프로젝트 설정** 왼쪽 탐색 영역에서 **팀**을 선택 하 고 **팀** 페이지에서 **groupcommon 팀**을 선택 합니다. 
   
   ![팀 구성](./media/group-manager-tasks/teams.png)
   
1. **팀 프로필** 페이지에서 **추가**를 선택 합니다.
   
   ![GroupCommon 팀에 추가](./media/group-manager-tasks/add-to-team.png)
   
1. **사용자 및 그룹 추가** 대화 상자에서 그룹에 추가할 멤버를 검색 하 고 선택한 다음 **변경 내용 저장**을 선택 합니다. 
   
   ![사용자 및 그룹 추가](./media/group-manager-tasks/add-users.png)
   

멤버에 대 한 사용 권한을 구성 하려면:

1. **프로젝트 설정** 왼쪽 탐색 영역에서 **사용 권한**을 선택 합니다. 
   
1. **사용 권한** 페이지에서 멤버를 추가 하려는 그룹을 선택 합니다. 
   
1. 해당 그룹에 대 한 페이지에서 **멤버**를 선택 하 고 **추가**를 선택 합니다. 
   
1. **멤버 초대** 팝업에서 그룹에 추가할 멤버를 검색 하 고 선택한 다음, **저장**을 선택 합니다. 
   
   ![멤버에 사용 권한 부여](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>다음 단계

팀 데이터 과학 프로세스의 다른 역할 및 작업에 대 한 자세한 설명은 다음 링크를 참조 하세요.

- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀에 대 한 개별 참여자 작업 프로젝트](project-ic-tasks.md)
