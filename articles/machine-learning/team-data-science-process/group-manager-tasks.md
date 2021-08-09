---
title: Team Data Science Process 그룹 관리자 작업
description: 그룹 관리자가 데이터 과학 팀 프로젝트에서 완료하는 작업에 대한 이 자세한 연습을 따르세요.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94740229"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team Data Science Process 그룹 관리자 작업

이 문서에서는 데이터 과학 조직을 위해 *그룹 관리자* 가 완료하는 작업에 대해 설명합니다. 그룹 관리자는 기업 내 전체 데이터 과학 단위를 관리합니다. 한 데이터 과학 단위에 여러 팀이 있을 수 있고, 각 팀은 고유한 비즈니스 수직 시장에서 많은 데이터 과학 프로젝트를 진행할 수 있습니다. 그룹 관리자의 목표는 TDSP([Team Data Science Process](overview.md))를 표준화하는 공동 작업 그룹 환경을 구축하는 것입니다. TDSP를 표준화하는 데이터 과학 팀에서 처리하는 모든 인력 역할 및 관련 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요.

다음 다이어그램은 6가지 기본 그룹 관리자 설정 작업을 보여 줍니다. 그룹 관리자는 자신의 작업을 대리인에게 위임할 수 있지만 역할과 연결된 작업은 변경되지 않습니다.

![그룹 관리자 작업](./media/group-manager-tasks/tdsp-group-manager.png)

1. 그룹에 대한 **Azure DevOps 조직** 을 설정합니다.
2. Azure DevOps 조직에서 기본 **GroupCommon 프로젝트** 를 만듭니다.
3. Azure Repos에서 **GroupProjectTemplate** 리포지토리를 만듭니다.
4. Azure Repos에서 **GroupUtilities** 리포지토리를 만듭니다.
5. Microsoft TDSP 팀의 **ProjectTemplate** 및 **Utilities** 리포지토리의 콘텐츠를 그룹 공통 리포지토리로 가져옵니다.
6. 팀 구성원이 그룹에 액세스할 수 있도록 **멤버 자격** 및 **권한** 을 설정합니다.

다음 자습서에서는 단계를 자세히 안내합니다. 

> [!NOTE] 
> 이 문서에서는 Azure DevOps를 사용하여 TDSP 그룹 환경을 설정합니다. 이것이 Microsoft에서 TDSP를 구현하는 방법이기 때문입니다. 그룹에서 다른 코드 호스팅 또는 개발 플랫폼을 사용하는 경우 그룹 관리자의 작업은 동일하지만 완료 방법이 다를 수 있습니다.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Azure DevOps에서 조직 및 프로젝트 만들기

1. [visualstudio.microsoft.com](https://visualstudio.microsoft.com)으로 이동하여 오른쪽 상단에서 **로그인** 을 선택하고 Microsoft 계정에 로그인합니다. 
   
   ![Microsoft 계정으로 로그인](./media/group-manager-tasks/signinvs.png)
   
   Microsoft 계정이 없으면 **지금 가입** 을 선택하고 Microsoft 계정을 만든 다음 이 계정을 사용하여 로그인합니다. 조직에 Visual Studio Subscription이 있는 경우 해당 구독에 대한 자격 증명을 사용하여 로그인합니다.
   
1. 로그인한 후 Azure DevOps 페이지의 오른쪽 상단에서 **새 조직 만들기** 를 선택합니다.
   
   ![새 조직 만들기](./media/group-manager-tasks/create-organization.png)
   
1. 서비스 약관, 개인정보처리방침 및 사용 규정에 동의하라는 메시지가 표시되면 **계속** 을 선택합니다.
   
1. 등록 대화 상자에서 Azure DevOps 조직의 이름을 지정하고 호스트 지역 할당을 수락하거나 드롭다운하여 다른 지역을 선택합니다. 그런 다음, **계속** 을 선택합니다. 

1. **시작할 프로젝트 만들기** 에서 *GroupCommon* 을 입력한 다음 **프로젝트 만들기** 를 선택합니다. 
   
   ![프로젝트 만들기](./media/group-manager-tasks/create-project.png)

**GroupCommon** 프로젝트 **요약** 페이지가 열립니다. 페이지 URL은 *https:\//\<servername>/\<organization-name>/GroupCommon* 입니다.

![프로젝트 요약 페이지](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>그룹 공통 리포지토리 설정

Azure Repos는 그룹에 대해 다음 유형의 리포지토리를 호스팅합니다.

- **그룹 공통 리포지토리**: 데이터 과학 단위 내의 여러 팀이 많은 데이터 과학 프로젝트에 채택할 수 있는 범용 리포지토리입니다. 
- **팀 리포지토리**: 데이터 과학 단위 내 특정 팀을 위한 리포지토리입니다. 이러한 리포지토리는 팀의 요구 사항에 따라 다르며 해당 팀 내의 여러 프로젝트에 사용될 수 있지만 데이터 과학 단위 내의 여러 팀에서 사용할 만큼 일반적이지 않습니다.
- **프로젝트 리포지토리**: 특정 프로젝트에 대한 리포지토리입니다. 이러한 리포지토리는 팀 내의 여러 프로젝트 또는 데이터 과학 단위의 다른 팀에 대해 충분히 일반적이지 않을 수 있습니다.

프로젝트에서 그룹 공통 리포지토리를 설정하려면 다음을 수행합니다. 
- 기본 **GroupCommon** 리포지토리의 이름을 **GroupProjectTemplate** 으로 바꿉니다.
- 새 **GroupUtilities** 리포지토리 만들기

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>기본 프로젝트 리포지토리의 이름을 GroupProjectTemplate으로 바꾸기

기본 **GroupCommon** 프로젝트 리포지토리의 이름을 **GroupProjectTemplate** 으로 바꾸려면:

1. **GroupCommon** 프로젝트 **요약** 페이지에서 **Repos** 를 선택합니다. 이 작업을 수행하면 현재 비어 있는 GroupCommon 프로젝트의 기본 **GroupCommon** 리포지토리로 이동합니다.
   
1. 페이지 상단에서 **GroupCommon** 옆에 있는 화살표를 드롭다운하고 **리포지토리 관리** 를 선택합니다.
   
   ![리포지토리 관리](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. **프로젝트 설정** 페이지에서 **GroupCommon** 옆에 있는 **...** 를 선택한 다음 **리포지토리 이름 바꾸기** 를 선택합니다. 
   
   ![...를 선택한 다음 리포지토리 이름 바꾸기를 선택합니다.](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. **GroupCommon 리포지토리 이름 바꾸기** 팝업에서 *GroupProjectTemplate* 을 입력한 다음 **이름 바꾸기** 를 선택합니다. 
   
   ![리포지토리 이름 바꾸기](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>GroupUtilities 리포지토리를 만듭니다.

**GroupUtilities** 리포지토리를 만들려면:

1. **GroupCommon** 프로젝트 **요약** 페이지에서 **Repos** 를 선택합니다. 
   
1. 페이지 상단에서 **GroupProjectTemplate** 옆에 있는 화살표를 드롭다운하고 **새 리포지토리** 를 선택합니다.
   
   ![새 리포지토리 선택](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. **새 리포지토리 만들기** 대화 상자에서 **유형** 으로 **Git** 을 선택하고 **리포지토리 이름** 으로 *GroupUtilities* 를 입력한 다음 **만들기** 를 선택합니다.
   
   ![GroupUtilities 리포지토리 만들기](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. **프로젝트 설정** 페이지에서 왼쪽 탐색 메뉴의 **리포지토리** 아래에 있는 **리포지토리** 를 선택하여 **GroupProjectTemplate** 및 **GroupUtilities** 라는 2개의 그룹 리포지토리를 확인합니다.
   
   ![2개의 그룹 리포지토리](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Microsoft TDSP 팀 리포지토리 가져오기

자습서의 이 부분에서는 Microsoft TDSP 팀에서 관리하는 **Projecttemplate** 및 **Utilities** 리포지토리의 콘텐츠를 **Groupprojecttemplate** 및 **GroupUtilities** 리포지토리로 가져옵니다. 

TDSP 팀 리포지토리를 가져오려면:

1. **GroupCommon** 프로젝트 홈페이지의 왼쪽 탐색 메뉴에서 **Repos** 를 선택합니다. 기본 **GroupProjectTemplate** 리포지토리가 열립니다. 
   
1. **GroupProjectTemplate이 비어 있음** 페이지에서 **가져오기** 를 선택합니다. 
   
   ![가져오기 선택](./media/group-manager-tasks/import-repo.png)
   
1. **Git 리포지토리 가져오기** 대화 상자에서 **원본 유형** 으로 **Git** 을 선택하고 **복제 URL** 에 대해 *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* 를 입력합니다. 그런 다음, **가져오기** 를 선택합니다. Microsoft TDSP 팀 ProjectTemplate 리포지토리의 콘텐츠를 GroupProjectTemplate 리포지토리로 가져옵니다. 
   
   ![Microsoft TDSP 팀 리포지토리 가져오기](./media/group-manager-tasks/import-repo-2.png)
   
1. **리포지토리** 페이지 상단에서 드롭다운하여 **GroupUtilities** 리포지토리를 선택합니다.
   
이제 두 그룹 리포지토리 각각에 Microsoft TDSP 팀의 해당 리포지토리에서 *.git* 디렉터리의 파일을 제외한 모든 파일이 포함됩니다. 

## <a name="customize-the-contents-of-the-group-repositories"></a>그룹 리포지토리의 콘텐츠 사용자 지정

그룹 리포지토리의 콘텐츠를 그룹의 특정 요구 사항에 맞게 사용자 지정하려는 경우 지금 수행할 수 있습니다. 파일을 수정하거나 디렉터리 구조를 변경하거나 그룹에서 개발했거나 그룹에 도움이 되는 파일을 추가할 수 있습니다.

### <a name="make-changes-in-azure-repos"></a>Azure Repos에서 변경

리포지토리 콘텐츠를 사용자 지정하려면:

1. **GroupCommon** 프로젝트 **요약** 페이지에서 **Repos** 를 선택합니다. 
   
1. 페이지 상단에서 사용자 지정하려는 리포지토리를 선택합니다.

1. repo 디렉터리 구조에서 변경할 폴더나 파일로 이동합니다. 
   
   - 새 폴더 또는 파일을 만들려면 **새로 만들기** 옆에 있는 화살표를 선택합니다. 
     
     ![새 파일 만들기](./media/group-manager-tasks/new-file.png)
     
   - 파일을 업로드하려면 **파일 업로드** 를 선택합니다. 
     
     ![파일 업로드](./media/group-manager-tasks/upload-files.png)
     
   - 기존 파일을 편집하려면 파일로 이동한 다음 **편집** 을 선택합니다. 
     
     ![파일 편집](./media/group-manager-tasks/edit-file.png)
     
1. 파일을 추가하거나 편집한 후 **커밋** 을 선택합니다.
   
   ![변경 내용 커밋](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>로컬 컴퓨터 또는 DSVM을 사용하여 변경

로컬 컴퓨터 또는 DSVM을 사용하여 변경하고 그룹 리포지토리에 변경 사항을 푸시하려면 Git 및 DSVM 작업을 위해 다음 필수 조건이 있는지 확인합니다.

- DSVM을 만들려는 경우 Azure 구독.
- 컴퓨터에 설치된 Git. DSVM을 사용하는 경우 Git의 사전 설치. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용하려는 경우 Azure에 만들어지고 구성된 Windows 또는 Linux DSVM. 자세한 내용과 지침은 [Data Science Virtual Machine 설명서](../data-science-virtual-machine/index.yml)를 참조하세요.
- Windows DSVM의 경우 컴퓨터에 [GCM(Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)이 설치되어 있습니다. *README.md* 파일에서 **다운로드 및 설치** 섹션으로 스크롤한 다음 **최신 설치 관리자** 를 선택합니다. 설치 프로그램 페이지에서 *.exe* 설치 프로그램을 다운로드하고 실행합니다. 
- Linux DSVM의 경우 SSH 공개 키가 DSVM에 설정되고 Azure DevOps에서 추가됩니다. 자세한 내용과 지침은 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)의 **SSH 공개 키 만들기** 섹션을 참조하세요. 

먼저 리포지토리를 로컬 컴퓨터에 복사하거나 *복제* 합니다. 
   
1. **GroupCommon** 프로젝트 **요약** 페이지에서 **Repos** 를 선택하고 페이지 상단에서 복제할 리포지토리를 선택합니다.
   
1. 리포지토리 페이지의 오른쪽 상단에서 **복제** 를 선택합니다.
   
1. **복제 리포지토리** 대화 상자에서, HTTP 연결은 **HTTPS** 를 선택하고, SSH 연결은 **SSH** 를 선택하고, **명령줄** 아래에 있는 복제 URL을 클립보드에 복사합니다.
   
   ![리포지토리 복제](./media/group-manager-tasks/clone.png)
   
1. 로컬 컴퓨터에서 다음 디렉터리를 만듭니다.
   
   - Windows의 경우 **C:\GitRepos\GroupCommon**
   - Linux의 경우 홈 디렉터리에 **$/GitRepos/GroupCommon** 
   
1. 만든 디렉터리로 변경합니다.
   
1. Git Bash에서 `git clone <clone URL>.` 명령을 실행합니다.
   
   예를 들어 다음 명령 중 하나는 **GroupUtilities** 리포지토리를 로컬 컴퓨터의 *GroupCommon* 디렉터리에 복제합니다. 
   
   **HTTPS 연결:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH 연결:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

리포지토리의 로컬 복제에서 원하는 대로 변경한 후 공유 그룹 공통 리포지토리에 변경 사항을 푸시할 수 있습니다. 

로컬 **GroupProjectTemplate** 또는 **GroupUtilities** 디렉터리에서 다음 Git Bash 명령을 실행합니다.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Git 리포지토리에 처음 커밋하는 경우 `git commit` 명령을 실행하기 전에 전역 매개 변수 *user.name* 및 *user.email* 을 구성해야 할 수 있습니다. 다음 두 명령을 실행합니다.
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 여러 Git 리포지토리에 커밋하는 경우 모든 리포지토리에 동일한 이름과 이메일 주소를 사용합니다. 여러 리포지토리에서 Git 활동을 추적하기 위해 Power BI 대시보드를 빌드할 때 동일한 이름과 이메일 주소를 사용하는 것이 편리합니다.

## <a name="add-group-members-and-configure-permissions"></a>그룹 구성원 추가 및 권한 구성

그룹에 구성원을 추가하려면:

1. Azure DevOps의 **GroupCommon** 프로젝트 홈 페이지의 왼쪽 탐색 메뉴에서 **프로젝트 설정** 을 선택합니다. 
   
1. **프로젝트 설정** 왼쪽 탐색에서 **팀** 을 선택한 다음 **팀** 페이지에서 **GroupCommon 팀** 을 선택합니다. 
   
   ![팀 구성](./media/group-manager-tasks/teams.png)
   
1. **팀 프로필** 페이지에서 **추가** 를 선택합니다.
   
   ![GroupCommon 팀에 추가](./media/group-manager-tasks/add-to-team.png)
   
1. **사용자 및 그룹 추가** 대화 상자에서 그룹에 추가할 구성원을 검색하여 선택한 다음 **변경 내용 저장** 을 선택합니다. 
   
   ![사용자 및 그룹 추가](./media/group-manager-tasks/add-users.png)
   

구성원에 대한 권한을 구성하려면:

1. **프로젝트 설정** 왼쪽 탐색에서 **권한** 을 선택합니다. 
   
1. **권한** 페이지에서 구성원을 추가할 그룹을 선택합니다. 
   
1. 해당 그룹의 페이지에서 **구성원** 을 선택한 다음 **추가** 를 선택합니다. 
   
1. **구성원 초대** 팝업에서 그룹에 추가할 구성원을 검색하여 선택한 다음 **저장** 을 선택합니다. 
   
   ![구성원에게 권한 부여](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>다음 단계

다음은 팀 데이터 과학 프로세스의 다른 역할 및 작업에 대한 자세한 설명에 대한 링크입니다.

- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀을 위한 프로젝트 개별 기여자 작업](project-ic-tasks.md)
