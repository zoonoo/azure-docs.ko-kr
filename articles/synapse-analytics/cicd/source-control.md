---
title: Synapse Studio의 소스 제어
description: Azure Synapse Studio에서 원본 제어를 구성 하는 방법 알아보기
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: b7248a3a66e0eeca4d86568f80af82bf2d89701f
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96031237"
---
# <a name="source-control-in-azure-synapse-studio"></a>Azure Synapse Studio의 소스 제어

기본적으로 Azure Synapse Studio 작성자는 Synapse 서비스에 대해 직접 작성 합니다. 그러나이 환경에는 다음과 같은 제한 사항이 있습니다.

- Synapse studio는 변경 내용을 저장 하기 위한 임시 저장소를 포함 하지 않습니다. **게시** 를 통해 변경 내용을 저장 하 고 공유 하는 유일한 방법은 Synapse 서비스에 직접 게시 되는 모든 변경 내용입니다.

- Synapse Studio는 공동 작업 및 버전 제어에 최적화 되어 있지 않습니다.

원본 제어 기능을 제공 하기 위해 Synapse Studio를 사용 하 여 작업 영역을 Git 리포지토리, Azure DevOps 또는 GitHub와 연결할 수 있습니다. 이 문서에서는 git 리포지토리를 사용 하도록 설정한 상태에서 Synapse 작업 영역을 구성 하 고 작업 하는 방법을 간략하게 설명 합니다. 또한 몇 가지 모범 사례 및 문제 해결 가이드를 강조 표시 합니다.

> [!NOTE]
> Azure Synapse Studio git 통합은 Azure Government 클라우드에서 사용할 수 없습니다.

## <a name="configure-git-repository-in-synapse-studio"></a>Synapse Studio에서 Git 리포지토리 구성 

Synapse Studio를 시작한 후 작업 영역에서 git 리포지토리를 구성할 수 있습니다. Synapse Studio 작업 영역은 한 번에 하나의 git 리포지토리에만 연결 될 수 있습니다. 

### <a name="configuration-method-1-authoring-canvas"></a>구성 방법 1: 작성 캔버스

Synapse Studio authoring canvas에서 **Synapse Live** 드롭다운 메뉴를 선택한 다음 **코드 리포지토리 설정** 을 선택 합니다.

![제작에서 코드 리포지토리 설정 구성](media/configure-repo-1.png)

### <a name="configuration-method-2-management-hub"></a>구성 방법 2: 관리 허브

Synapse Studio의 관리 허브로 이동 합니다. **소스 제어** 섹션에서 **Git 구성** 을 선택 합니다. 연결 된 리포지토리가 없는 경우 **구성** 을 클릭 합니다.

![관리 허브에서 코드 리포지토리 설정 구성](media/configure-repo-2.png)

> [!NOTE]
> 작업 영역 참가자, 소유자 또는 상위 수준 역할로 부여 된 사용자는 Azure Synapse studio에서 git 리포지토리를 구성 하 고, 설정을 편집 하 고, 연결을 끊을 수 있습니다. 

작업 영역에서 Azure DevOps 또는 GitHub git 리포지토리를 연결할 수 있습니다.

## <a name="connect-with-azure-devops-git"></a>Azure DevOps Git를 사용 하 여 연결 

원본 제어, 공동 작업, 버전 관리 등에 대해 Synapse 작업 영역을 Azure DevOps 리포지토리에 연결할 수 있습니다. Azure DevOps 리포지토리가 없는 경우 [다음 지침](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) 에 따라 리포지토리 리소스를 먼저 만듭니다.

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps Git 리포지토리 설정

Git 리포지토리에 연결 하는 경우 먼저 리포지토리 유형을 Azure DevOps git로 선택한 후 드롭다운 목록에서 Azure AD 테 넌 트 하나를 선택 하 고 **계속** 을 클릭 합니다.

![코드 리포지토리 설정 구성](media/connect-with-azuredevops-repo-selected.png)

구성 창에는 다음과 같은 Azure DevOps git 설정이 표시 됩니다.

| 설정 | Description | 값 |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다.<br/> | Azure DevOps Git 또는 GitHub |
| **Azure Active Directory** | Azure AD 테넌트 이름입니다. | `<your tenant name>` |
| **Azure DevOps 계정** | Azure Repos 조직 이름입니다. Azure Repos 조직 이름은 `https://{organization name}.visualstudio.com`에서 확인할 수 있습니다. [Azure Repos 조직에 로그인](https://www.visualstudio.com/team-services/git/)하여 Visual Studio 프로필에 액세스하고 리포지토리 및 프로젝트를 확인할 수 있습니다. | `<your organization name>` |
| **ProjectName** | Azure Repos 프로젝트 이름입니다. Azure Repos 프로젝트 이름은 `https://{organization name}.visualstudio.com/{project name}`에서 확인할 수 있습니다. | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos 코드 리포지토리 이름입니다. Azure Repos 프로젝트는 프로젝트가 확장됨에 따라 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 프로젝트에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your Azure Repos code repository name>` |
| **협업 분기** | 게시에 사용되는 Azure Repos 협업 분기입니다. 기본적으로 `master`입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. 기존 분기를 선택 하거나 새로 만들 수 있습니다. | `<your collaboration branch name>` |
| **루트 폴더** | Azure Repos 협업 분기의 루트 폴더입니다. | `<your root folder name>` |
| **리포지토리로 기존 리소스 가져오기** | Synapse Studio에서 Azure Repos Git 리포지토리로 기존 리소스를 가져올지 여부를 지정 합니다. 확인란을 선택 하 여 작업 영역 리소스 (풀 제외)를 JSON 형식의 연결 된 Git 리포지토리로 가져옵니다. 이 작업은 각 리소스를 개별적으로 내보냅니다. 이 확인란을 선택 하지 않으면 기존 리소스를 가져오지 않습니다. | 선택 됨 (기본값) |
| **이 분기로 리소스 가져오기** | 리소스 (sql 스크립트, 노트북, spark 작업 정의, 데이터 집합, 데이터 흐름 등)를 가져올 분기를 선택 합니다. 

또한에서 리포지토리 링크를 사용 하 여 연결 하려는 git 리포지토리를 빠르게 가리킬 수 있습니다. 

### <a name="use-a-different-azure-active-directory-tenant"></a>다른 Azure Active Directory 테넌트 사용

Azure Repos Git 리포지토리는 다른 Azure Active Directory 테넌트에 있을 수 없습니다. 다른 Azure AD 테넌트를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다. 자세한 내용은 [구독 관리자 변경](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator#assign-a-subscription-administrator) 을 참조 하세요.

> [!IMPORTANT]
> 다른 Azure Active Directory에 연결 하려면 로그인 한 사용자가 Active Directory의 일부 여야 합니다. 

### <a name="use-your-personal-microsoft-account"></a>개인 Microsoft 계정 사용

Git 통합에 개인 Microsoft 계정을 사용하려면 개인 Azure 리포지토리를 조직의 Active Directory에 연결하면 됩니다.

1. 개인 Microsoft 계정을 조직의 Active Directory에 게스트로 추가합니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](https://docs.microsoft.com/azure/active-directory/external-identities/add-users-administrator)를 참조하세요.

2. 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다. 그런 다음, 조직의 Active Directory로 전환합니다.

3. Azure DevOps 섹션으로 이동하면 이제 개인 리포지토리가 보입니다. 리포지토리를 선택하고 Active Directory와 연결합니다.

이러한 구성 단계 후에는 Synapse Studio에서 Git 통합을 설정할 때 개인 리포지토리를 사용할 수 있습니다.

Azure Repos를 조직의 Active Directory에 연결하는 방법에 대한 자세한 내용은 [Azure DevOps 조직을 Azure Active Directory에 연결](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)을 참조하세요.

## <a name="connect-with-github"></a>GitHub를 사용 하 여 연결 

 원본 제어, 공동 작업, 버전 관리를 위해 작업 영역을 GitHub 리포지토리와 연결할 수 있습니다. GitHub 계정 또는 리포지토리가 없는 경우 [이러한 지침](https://github.com/join)에 따라 리소스를 만듭니다.

Synapse Studio와 GitHub 통합은 공용 GitHub (즉, [https://github.com](https://github.com) ) 및 GitHub Enterprise를 모두 지원 합니다. GitHub의 리포지토리에 대 한 읽기 및 쓰기 권한이 있는 한 공용 및 개인 GitHub 리포지토리를 모두 사용할 수 있습니다.

### <a name="github-settings"></a>GitHub 설정

Git 리포지토리에 연결 하는 경우 먼저 리포지토리 유형을 GitHub로 선택 하 고 github Enterprise Server를 사용 하는 경우 github 계정 또는 GitHub Enterprise Server URL을 제공 하 고 **계속** 을 클릭 합니다.

![GitHub 리포지토리 설정](media/connect-with-github-repo-1.png)

구성 창에 다음 GitHub 리포지토리 설정이 표시됩니다.

| **설정** | **설명**  | **값**  |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다. | GitHub |
| **GitHub Enterprise 사용** | GitHub Enterprise를 선택하기 위한 확인란 | 선택하지 않음(기본값) |
| **GitHub Enterprise URL** | GitHub Enterprise 루트 URL(로컬 GitHub Enterprise 서버의 HTTPS여야 함) 예: `https://github.mydomain.com` **GitHub Enterprise 사용** 을 선택한 경우에만 필요 | `<your GitHub enterprise url>` |                                                           
| **GitHub 계정** | 사용자 GitHub 계정 이름. 이 이름은 https:\//github.com/{이름}/{리포지토리 이름}에서 찾을 수 있습니다. 이 페이지로 이동하면 GitHub 계정에 GitHub OAuth 자격 증명을 입력하라는 메시지가 표시됩니다. | `<your GitHub account name>` |
| **리포지토리 이름**  | GitHub 코드 리포지토리 이름입니다. GitHub 계정은 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 계정에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your repository name>` |
| **협업 분기** | 게시에 사용되는 GitHub 협업 분기입니다. 기본값은 마스터입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. | `<your collaboration branch>` |
| **루트 폴더** | GitHub 협업 분기의 루트 폴더입니다. |`<your root folder name>` |
| **리포지토리로 기존 리소스 가져오기** | Synapse Studio에서 Git 리포지토리로 기존 리소스를 가져올지 여부를 지정 합니다. 확인란을 선택 하 여 작업 영역 리소스 (풀 제외)를 JSON 형식의 연결 된 Git 리포지토리로 가져옵니다. 이 작업은 각 리소스를 개별적으로 내보냅니다. 이 확인란을 선택 하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **이 분기로 리소스 가져오기** | 리소스 (sql 스크립트, 노트북, spark 작업 정의, 데이터 집합, 데이터 흐름 등)를 가져올 분기를 선택 합니다. 

### <a name="github-organizations"></a>GitHub 조직

GitHub 조직에 연결 하려면 조직이 Synapse Studio에 권한을 부여 해야 합니다. 조직에 대 한 관리자 권한이 있는 사용자는 아래 단계를 수행 해야 합니다.

#### <a name="connecting-to-github-for-the-first-time"></a>처음으로 GitHub에 연결

처음으로 Synapse Studio에서 GitHub에 연결 하는 경우 다음 단계에 따라 GitHub 조직에 연결 합니다.

1. Git 구성 창의 *GitHub 계정* 필드에 조직 이름을 입력 합니다. GitHub에 로그인 하 라는 메시지가 표시 됩니다. 

1. 사용자 자격 증명을 사용 하 여 로그인 합니다.

1. *Azure Synapse* 라는 응용 프로그램으로 Synapse에 권한을 부여 하 라는 메시지가 표시 됩니다. 이 화면에 Synapse에서 조직에 액세스할 수 있는 권한을 부여 하는 옵션이 표시 됩니다. 사용 권한을 부여 하는 옵션이 표시 되지 않으면 관리자에 게 GitHub를 통해 사용 권한을 수동으로 부여 하도록 요청 합니다.

이러한 단계를 수행 하면 작업 영역에서 조직 내 공용 및 개인 리포지토리에 연결할 수 있습니다. 연결할 수 없는 경우 브라우저 캐시를 지우고 다시 시도 하세요.

## <a name="version-control"></a>버전 제어

개발자는 버전 제어 시스템 ( _소스 제어_ 라고도 함)을 사용 하 여 코드를 공동 작업 하 고 변경 내용을 추적할 수 있습니다. 소스 제어는 다중 개발자 프로젝트에 필수적인 도구입니다.

### <a name="creating-feature-branches"></a>기능 분기 만들기

Synapse Studio와 연결 된 각 Git 리포지토리에는 공동 작업 분기가 있습니다. ( `main` 또는 `master` 가 기본 공동 작업 분기) 분기 드롭다운에서 **+ 새 분기** 를 클릭하여 기능 분기를 만들 수도 있습니다. 새 분기 창이 나타나면 기능 분기의 이름을 입력합니다.

![새 분기 만들기](media/create-new-branch.png)

기능 분기의 변경 내용을 협업 분기에 병합할 준비가 되면, 분기 드롭다운을 클릭하고 **끌어오기 요청 만들기** 를 선택합니다. 이 작업을 수행 하면 끌어오기 요청을 발생 시키고, 코드 검토를 수행 하 고, 공동 작업 분기에 변경 내용을 병합할 수 있는 Git 공급자로 이동 합니다. 공동 작업 분기에서 Synapse 서비스에만 게시할 수 있습니다. 

![새 끌어오기 요청 만들기](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>게시 설정 구성

기본적으로 Synapse Studio는 작업 영역 템플릿을 생성 하 고 라는 분기에 저장 `workspace_publish` 합니다. 사용자 지정 게시 분기를 구성하려면 협업 분기의 루트 폴더에 `publish_config.json` 파일을 추가합니다. 게시 하는 경우 Synapse Studio는이 파일을 읽고 필드를 찾은 `publishBranch` 다음 지정 된 위치에 작업 영역 템플릿 파일을 저장 합니다. 분기가 없으면 Synapse Studio에서 자동으로 만듭니다. 이 파일의 모양은 다음과 같습니다.

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio에는 한 번에 하나의 게시 분기만 있을 수 있습니다. 새 게시 분기를 지정 하면 이전 게시 분기가 삭제 되지 않습니다. 이전 게시 분기를 제거하려는 경우에는 수동으로 해당 분기를 삭제합니다.

### <a name="publish-code-changes"></a>코드 변경 내용 게시

공동 작업 분기에 변경 내용을 병합 한 후 **게시** 를 클릭 하 여 공동 작업 분기의 코드 변경 내용을 Synapse 서비스에 수동으로 게시 합니다.

![변경 내용 게시](media/gitmode-publish.png)

사이드 창이 열리면 게시 분기 및 보류 중인 변경 사항이 올바른지 확인합니다. 변경 내용을 확인한 후 **확인** 을 클릭하여 게시를 확인합니다.

![올바른 게시 분기 확인](media/publish-change.png)

> [!IMPORTANT]
> 공동 작업 분기는 서비스에 배포 된 항목을 나타내지는 않습니다. 공동 작업 분기의 변경 내용은 수동으로 서비스를 게시 *해야* 합니다.

## <a name="switch-to-a-different-git-repository"></a>다른 Git 리포지토리로 전환

다른 Git 리포지토리로 전환 하려면 **소스 제어** 에서 관리 허브의 git 구성 페이지로 이동 합니다. **연결 끊기** 를 선택 합니다. 

![GIT 아이콘](media/remove-repository.png)

작업 영역 이름을 입력 하 고 **연결 끊기** 를 클릭 하 여 작업 영역과 연결 된 Git 리포지토리를 제거 합니다.

현재 리포지토리와의 연결을 제거한 후에는 다른 리포지토리를 사용 하도록 Git 설정을 구성한 다음 기존 리소스를 새 리포지토리로 가져올 수 있습니다.

> [!IMPORTANT]
> 작업 영역에서 Git 구성을 제거 해도 리포지토리에서 아무것도 삭제 되지 않습니다. Synapse 작업 영역에는 게시 된 모든 리소스가 포함 됩니다. 계속 해 서 서비스에 대해 작업 영역을 직접 편집할 수 있습니다.

## <a name="best-practices-for-git-integration"></a>Git 통합에 대한 모범 사례

-   **사용 권한**. 작업 영역에 연결 된 git 리포지토리가 있으면 작업 영역에서 임의의 역할을 사용 하 여 git 리포지토리에 액세스할 수 있는 모든 사용자가 sql 스크립트, 노트북, spark 작업 정의, 데이터 집합, 데이터 흐름, 파이프라인 등의 아티팩트를 git 모드에서 업데이트할 수 있습니다. 일반적으로 모든 팀 멤버에 게 작업 영역을 업데이트할 수 있는 권한이 필요 하지 않습니다. Synapse workspace 아티팩트 작성자에 게는 git 리포지토리 권한만 부여 합니다. 
-   **공동 작업**. 협업 분기에 대한 직접 체크 인은 허용하지 않는 것이 좋습니다. 이렇게 제한하면 [기능 분기 만들기](source-control.md#creating-feature-branches)의 설명대로 모든 체크 인이 끌어오기 요청 검토 프로세스를 거치기 때문에 버그를 방지하는 데 유용할 수 있습니다
-   **Synapse 라이브 모드** 입니다. Git 모드로 게시 한 후에는 모든 변경 내용이 Synapse live 모드로 반영 됩니다. Synapse live 모드에서는 게시를 사용할 수 없습니다. 적절 한 사용 권한이 부여 된 경우 라이브 모드에서 아티팩트를 실행 하 고 볼 수 있습니다. 
-   **Studio에서 아티팩트를 편집** 합니다. Synapse studio는 작업 영역 소스 제어를 사용 하도록 설정 하 고 변경 내용을 git에 자동으로 동기화 할 수 있는 유일한 장소입니다. SDK, PowerShell을 통한 모든 변경 내용은 git와 동기화 되지 않습니다. Git를 사용 하는 경우 항상 Studio에서 아티팩트를 편집 하는 것이 좋습니다.

## <a name="troubleshooting-git-integration"></a>Git 통합 문제 해결

### <a name="access-to-git-mode"></a>Git 모드에 대 한 액세스 

작업 영역과 연결 된 GitHub git 리포지토리에 대 한 사용 권한이 부여 되었지만 Git 모드에 액세스할 수 없는 경우: 

1. 캐시를 지우고 페이지를 새로 고칩니다. 

1. GitHub 계정에 로그인 합니다.

### <a name="stale-publish-branch"></a>부실한 게시 분기

게시 분기가 공동 작업 분기와 동기화 되지 않은 상태에서 최근 게시에도 불구 하 고 오래 된 리소스를 포함 하는 경우 다음 단계를 수행 합니다.

1. 현재 Git 리포지토리 제거

1. 동일한 설정으로 Git를 다시 구성 하지만 **기존 리소스를 리포지토리로 가져오기** 가 선택 되어 있는지 확인 하 고 동일한 분기를 선택 합니다.  

1. 변경 내용을 협업 분기에 병합하는 끌어오기 요청을 만듭니다. 


## <a name="next-steps"></a>다음 단계

* 연속 통합 및 배포를 구현 하려면 [CI/CD (지속적인 통합 및 배달)](continuous-integration-deployment.md)를 참조 하세요.