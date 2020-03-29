---
title: Team Data Science Process 그룹 관리자 작업
description: 그룹 관리자가 데이터 과학 팀 프로젝트에서 완료한 작업에 대한 자세한 단계를 따릅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721356"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team Data Science Process 그룹 관리자 작업

이 문서에서는 *그룹 관리자가* 데이터 과학 조직에 대해 완료한 작업에 대해 설명합니다. 그룹 관리자는 기업의 전체 데이터 과학 단위를 관리합니다. 데이터 과학 부서에는 여러 팀이 있을 수 있으며, 각 팀은 서로 다른 비즈니스 분야에서 많은 데이터 과학 프로젝트를 진행하고 있습니다. 그룹 관리자의 목표는 팀 데이터 과학 프로세스(TDSP)를 표준화하는 공동 그룹 환경을 구축하는 것입니다. [Team Data Science Process](overview.md) TDSP에서 표준화하는 데이터 과학 팀에서 처리하는 모든 인사 역할 및 관련 작업에 대한 개요는 [팀 데이터 과학 프로세스 역할 및 작업을](roles-tasks.md)참조하십시오.

다음 다이어그램은 6개의 주요 그룹 관리자 설정 작업을 보여 주며, 이 에 대한 다이어그램은 다음과 같은 것입니다. 그룹 관리자는 작업을 서로게이트에 위임할 수 있지만 역할과 연결된 작업은 변경되지 않습니다.

![그룹 관리자 작업](./media/group-manager-tasks/tdsp-group-manager.png)

1. 그룹에 대한 **Azure DevOps 조직을** 설정합니다.
2. Azure DevOps 조직에서 기본 **GroupCommon 프로젝트를** 만듭니다.
3. Azure 리포지토리에서 **그룹 프로젝트 템플릿** 리포지토리를 만듭니다.
4. Azure 리포지토리에서 **그룹유틸리티** 리포지토리를 만듭니다.
5. Microsoft TDSP 팀의 **ProjectTemplate** 및 **유틸리티** 리포지토리의 내용을 그룹 공통 리포지토리로 가져옵니다.
6. 팀 구성원이 그룹에 액세스할 수 있도록 **멤버 자격** 및 **권한을 설정합니다.**

다음 자습서에서는 단계를 자세히 설명합니다. 

> [!NOTE] 
> 이 문서에서는 Azure DevOps를 사용하여 TDSP 그룹 환경을 설정합니다. 그룹이 다른 코드 호스팅 또는 개발 플랫폼을 사용하는 경우 그룹 관리자의 작업은 동일하지만 이를 완료하는 방법은 다를 수 있습니다.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Azure DevOps에서 조직 및 프로젝트 만들기

1. [visualstudio.microsoft.com](https://visualstudio.microsoft.com)이동하여 오른쪽 상단에서 **로그인을** 선택하고 Microsoft 계정에 로그인합니다. 
   
   ![Microsoft 계정으로 로그인](./media/group-manager-tasks/signinvs.png)
   
   Microsoft 계정이 없는 경우 **지금 등록을**선택하고 Microsoft 계정을 만들고 이 계정을 사용하여 로그인합니다. 조직에 Visual Studio 구독이 있는 경우 해당 구독에 대한 자격 증명으로 로그인합니다.
   
1. 로그인한 후 Azure DevOps 페이지의 오른쪽 상단에서 **새 조직 만들기를**선택합니다.
   
   ![새 조직 만들기](./media/group-manager-tasks/create-organization.png)
   
1. 서비스 약관, 개인 정보 보호 정책에 동의하라는 메시지가 표시되면 **계속을**선택합니다.
   
1. 등록 대화 상자에서 Azure DevOps 조직의 이름을 지정하고 호스트 지역 할당을 수락하거나 드롭다운하여 다른 지역을 선택합니다. 그런 다음 **계속을**선택합니다. 

1. **시작할 프로젝트 만들기에서** *GroupCommon*을 입력한 다음 **프로젝트 만들기를**선택합니다. 
   
   ![프로젝트 만들기](./media/group-manager-tasks/create-project.png)

**GroupCommon** 프로젝트 **요약** 페이지가 열립니다. 페이지 URL은 *https입니다:\//\<서버\<이름>/ 조직 이름>/GroupCommon*.

![프로젝트 요약 페이지](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>그룹 공통 리포지토리 설정

Azure Repos는 그룹에 대해 다음과 같은 유형의 리포지토리를 호스팅합니다.

- **공통 리포지토리 그룹**: 데이터 과학 단위 내의 여러 팀이 많은 데이터 과학 프로젝트에 채택할 수 있는 범용 리포지토리입니다. 
- **팀 리포지토리**: 데이터 과학 단위 내의 특정 팀을 위한 리포지토리입니다. 이러한 리포지토리는 팀의 요구에 따라 다르며 해당 팀 내의 여러 프로젝트에 사용할 수 있지만 데이터 과학 단위 내의 여러 팀에서 사용할 수 있을 만큼 일반적이지는 않습니다.
- **프로젝트 리포지토리**: 특정 프로젝트에 대한 리포지토리. 이러한 리포지토리는 팀 내의 여러 프로젝트나 데이터 과학 단위의 다른 팀에 대해 충분히 일반적이지 않을 수 있습니다.

프로젝트에서 그룹 공통 리포지토리를 설정하려면 다음을 수행하십시오. 
- 기본 **그룹공통** 리포지토리의 이름을 **그룹프로젝트템플릿으로** 변경
- 새 **그룹유틸리티** 리포지토리 만들기

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>기본 프로젝트 리포지토리의 이름을 GroupProjectTemplate로 변경

기본 **GroupCommon** 프로젝트 리포지토리의 이름을 **GroupProjectTemplate로**변경하려면 다음을 수행합니다.

1. **GroupCommon** 프로젝트 **요약** 페이지에서 **리포지토리를**선택합니다. 이 작업은 **현재** 비어 있는 GroupCommon 프로젝트의 기본 GroupCommon 리포지토리로 이동합니다.
   
1. 페이지 상단에서 **GroupCommon** 옆의 화살표를 드롭다운하고 **리포지토리 관리를**선택합니다.
   
   ![리포지토리 관리](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. 프로젝트 **설정** 페이지에서 GroupCommon 다음에 있는 **...Common을**선택한 다음 **리포지토리 이름을 바꾸기를**선택합니다. **...** 
   
   ![선택... 그런 다음 리포지토리 이름 바꾸기를 선택합니다.](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. 그룹 **공통 리포지토리 팝업의 이름을 바꿀** 때 *GroupProjectTemplate*를 입력한 다음 **이름을 바꾸기를**선택합니다. 
   
   ![리포지토리 이름 바꾸기](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>GroupUtilities 리포지토리를 만듭니다.

**GroupUtilities** 리포지토리를 만들려면 다음을 수행하십시오.

1. **GroupCommon** 프로젝트 **요약** 페이지에서 **리포지토리를**선택합니다. 
   
1. 페이지 상단에서 **GroupProjectTemplate** 옆의 화살표를 드롭다운하고 **새 리포지토리를**선택합니다.
   
   ![새 리포지토리 선택](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. 새 **리포지토리** 대화 상자 만들기에서 **Git을** **유형으로**선택하고 *GroupUtilities를* **리포지토리 이름으로**입력한 다음 **에서 만들기를**선택합니다.
   
   ![그룹 유틸리티 리포지토리 만들기](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. 프로젝트 **설정** 페이지에서 왼쪽 탐색의 **리포지토리** 아래의 **리포지토리를** 선택하여 두 그룹 리포지토리: **GroupProjectTemplate** 및 **GroupUtilities**를 볼 수 있습니다.
   
   ![두 그룹 리포지토리](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Microsoft TDSP 팀 리포지토리 가져오기

자습서의 이 부분에서는 Microsoft TDSP 팀에서 관리하는 **ProjectTemplate** 및 **유틸리티** 리포지토리의 내용을 **GroupProjectTemplate** 및 **GroupUtilities** 리포지토리로 가져옵니다. 

TDSP 팀 리포지토리를 가져오려면 다음을 수행합니다.

1. **GroupCommon** 프로젝트 홈 페이지에서 왼쪽 탐색에서 **리포지토리를** 선택합니다. 기본 **그룹프로젝트템플릿** 리포지토리가 열립니다. 
   
1. **그룹프로젝트템플릿이 비어 있는** 페이지에서 **가져오기를**선택합니다. 
   
   ![가져오기 선택](./media/group-manager-tasks/import-repo.png)
   
1. **Git 리포지토리 가져오기** 대화 상자에서 **Git을** **소스 유형으로**선택하고 복제 **URL에**대해 *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* 를 입력합니다. 그런 다음 **가져오기를**선택합니다. Microsoft TDSP 팀 ProjectTemplate 리포지토리의 내용은 GroupProjectTemplate 리포지토리로 가져옵니다. 
   
   ![가져오기 Microsoft TDSP 팀 리포지토리](./media/group-manager-tasks/import-repo-2.png)
   
1. **리포지토리** 페이지 상단에서 드롭다운하고 **GroupUtilities** 리포지토리를 선택합니다.
   
1. 가져오기 프로세스를 반복하여 Microsoft TDSP 팀 **유틸리티** 리포지토리의 내용을 *가져오십시오( https:\//github.com/Azure/Azure-TDSP-Utilities.git)* **그룹유틸리티 리포지토리로** 가져옵니다. 
   
이제 두 그룹 리포지토리각각에 *.git* 디렉터리의 파일을 제외한 모든 파일이 Microsoft TDSP 팀의 해당 리포지토리에 포함되어 있습니다. 

## <a name="customize-the-contents-of-the-group-repositories"></a>그룹 리포지토리의 내용 사용자 지정

그룹의 특정 요구 사항을 충족하도록 그룹 리포지토리의 내용을 사용자 지정하려는 경우 지금 수행할 수 있습니다. 파일을 수정하거나 디렉터리 구조를 변경하거나 그룹이 개발했거나 그룹에 도움이 되는 파일을 추가할 수 있습니다.

### <a name="make-changes-in-azure-repos"></a>Azure 리포지토리에서 변경

리포지토리 내용을 사용자 지정하려면 다음을 수행합니다.

1. **GroupCommon** 프로젝트 **요약** 페이지에서 **리포지토리를**선택합니다. 
   
1. 페이지 상단에서 사용자 지정할 리포지토리를 선택합니다.

1. 리포지토리 디렉터리 구조에서 변경하려는 폴더 또는 파일로 이동합니다. 
   
   - 새 폴더 또는 파일을 만들려면 **새**. 
     
     ![새 파일 만들기](./media/group-manager-tasks/new-file.png)
     
   - 파일을 업로드하려면 **파일 업로드를**선택합니다. 
     
     ![파일 업로드](./media/group-manager-tasks/upload-files.png)
     
   - 기존 파일을 편집하려면 파일로 이동한 다음 **편집을**선택합니다. 
     
     ![파일 편집](./media/group-manager-tasks/edit-file.png)
     
1. 파일을 추가하거나 편집한 후 **커밋**을 선택합니다.
   
   ![변경 내용 커밋](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>로컬 컴퓨터 또는 DSVM을 사용하여 변경

로컬 컴퓨터 또는 DSVM을 사용하여 변경하고 변경 내용을 그룹 리포지토리로 푸시하려면 Git 및 DSVM 작업을 위한 필수 구성 조건이 있는지 확인합니다.

- DSVM을 만들려는 경우 Azure 구독입니다.
- 컴퓨터에 설치된 Git. DSVM을 사용하는 경우 Git이 사전 설치되어 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- DSVM을 사용하려는 경우 Azure에서 만들고 구성한 Windows 또는 Linux DSVM입니다. 자세한 정보 및 지침은 [데이터 과학 가상 시스템 설명서를](/azure/machine-learning/data-science-virtual-machine/)참조하십시오.
- Windows DSVM의 경우 [GCM(GCM)이](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 컴퓨터에 설치되어 있습니다. *README.md* 파일에서 다운로드 및 **설치** 섹션으로 스크롤하여 최신 **설치 관리자를**선택합니다. 설치 관리자 페이지에서 *.exe* 설치 관리자를 다운로드하여 실행합니다. 
- Linux DSVM의 경우 DSVM에 SSH 공개 키가 설정되고 Azure DevOps에 추가됩니다. 자세한 정보 및 지침은 [플랫폼 및 도구 부록의](platforms-and-tools.md#appendix) **SSH 공개 키 만들기** 섹션을 참조하십시오. 

먼저 리포지토리를 로컬 컴퓨터에 복사하거나 *복제합니다.* 
   
1. **GroupCommon** 프로젝트 **요약** 페이지에서 **리포지토리를**선택하고 페이지 상단에서 복제할 리포지토리를 선택합니다.
   
1. 리포지토리 페이지에서 오른쪽 상단에 있는 **복제를** 선택합니다.
   
1. 복제 **리포지토리** 대화 상자에서 HTTP 연결에 대한 **HTTPS를** 선택하거나 SSH 연결의 **경우 SSH를** 선택하고 **명령줄** 아래에 있는 복제 URL을 클립보드에 복사합니다.
   
   ![리포지토리 복제](./media/group-manager-tasks/clone.png)
   
1. 로컬 컴퓨터에서 다음 디렉터리를 만듭니다.
   
   - 윈도우용: **C:\GitRepos\그룹공통**
   - 리눅스의 경우, 홈 디렉토리에 **$/GitRepos/GroupCommon** 
   
1. 만든 디렉터리로 변경합니다.
   
1. Git Bash에서 명령을 실행합니다.`git clone <clone URL>.`
   
   예를 들어 다음 명령 중 하나는 **GroupUtilities** 리포지토리를 로컬 컴퓨터의 *GroupCommon* 디렉터리로 복제합니다. 
   
   **HTTPS 연결:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH 연결:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

리포지토리의 로컬 복제에서 원하는 변경 내용을 변경한 후 공유 그룹 공통 리포지토리로 변경내용을 푸시할 수 있습니다. 

로컬 **GroupProjectTemplate** 또는 **GroupUtilities** 디렉터리에서 다음 Git Bash 명령을 실행합니다.

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

## <a name="add-group-members-and-configure-permissions"></a>그룹 구성원 추가 및 사용 권한 구성

그룹에 구성원을 추가하려면 다음을 수행합니다.

1. Azure DevOps에서 **GroupCommon** 프로젝트 홈 페이지에서 왼쪽 탐색에서 **프로젝트 설정을** 선택합니다. 
   
1. 프로젝트 **설정** 왼쪽 탐색에서 **팀**을 선택한 다음 **팀** 페이지에서 그룹 **공통 팀을**선택합니다. 
   
   ![팀 구성](./media/group-manager-tasks/teams.png)
   
1. 팀 **프로필** 페이지에서 **추가를**선택합니다.
   
   ![그룹공통 팀에 추가](./media/group-manager-tasks/add-to-team.png)
   
1. 사용자 **및 그룹 추가** 대화 상자에서 그룹에 추가할 구성원을 검색하고 선택한 다음 **변경 내용 저장을**선택합니다. 
   
   ![사용자 및 그룹 추가](./media/group-manager-tasks/add-users.png)
   

구성원에 대한 사용 권한을 구성하려면 다음을 수행합니다.

1. 프로젝트 **설정** 왼쪽 탐색에서 **사용 권한을 선택합니다.** 
   
1. 사용 권한 페이지에서 구성원을 추가할 그룹을 **선택합니다.** 
   
1. 해당 그룹의 페이지에서 **구성원**을 선택한 다음 **에 추가를**선택합니다. 
   
1. 구성원 **초대** 팝업에서 그룹에 추가할 구성원을 검색하고 선택한 다음 **에 추가를**선택합니다. 
   
   ![구성원에게 권한 부여](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>다음 단계

다음은 팀 데이터 과학 프로세스의 다른 역할 및 작업에 대한 자세한 설명에 대한 링크입니다.

- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀을 위한 개별 기여자 작업 프로젝트](project-ic-tasks.md)
