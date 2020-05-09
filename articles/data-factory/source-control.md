---
title: 원본 제어
description: Azure Data Factory에서 소스 제어를 구성 하는 방법을 알아봅니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/30/2020
ms.openlocfilehash: f327844be57d7f8e177f3bf72b1e3b56c5147e00
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629340"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory의 소스 제어
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

기본적으로는 데이터 팩터리 서비스에 대해 직접 UX (사용자 인터페이스 환경) 작성자를 Azure Data Factory 합니다. 이 환경에는 다음과 같은 제한 사항이 있습니다.

- Data Factory 서비스는 사용자의 변경 사항을 위한 JSON 엔터티를 저장하는 리포지토리를 포함하지 않습니다. 변경 내용을 저장 하는 유일한 방법은 **모두 게시** 단추를 통해 변경 하 고 모든 변경 내용은 data factory 서비스에 직접 게시 하는 것입니다.
- Data Factory 서비스는 공동 작업 및 버전 제어에 최적화 되어 있지 않습니다.

더 나은 제작 환경을 제공 하기 위해 Azure Data Factory Azure Repos 또는 GitHub를 사용 하 여 Git 리포지토리를 구성할 수 있습니다. Git은 변경 내용 추적과 공동 작업을 더 쉽게 수행할 수 있도록 하는 버전 제어 시스템입니다. 이 자습서에서는 모범 사례 및 문제 해결 가이드를 강조 표시 하 여 git 리포지토리를 구성 하 고 사용 하는 방법을 간략하게 설명 합니다.

> [!NOTE]
> Azure data factory git 통합은 Azure Government 클라우드에서 사용할 수 없습니다.

## <a name="advantages-of-git-integration"></a>Git 통합의 장점

다음은 git 통합에서 제작 환경에 제공 하는 몇 가지 이점 목록입니다.

-   **원본 제어:** 데이터 팩터리 작업이 매우 중요 해지면 팩터리와 Git를 통합 하 여 다음과 같은 다양 한 소스 제어 이점을 활용 하는 것이 좋습니다.
    -   변경 내용을 추적/감사할 수 있습니다.
    -   버그를 발생한 변경 내용을 되돌릴 수 있습니다.
-   **부분 저장:** 데이터 팩터리 서비스에 대해 제작할 때 변경 내용을 초안으로 저장할 수 없으며 모든 게시에서 데이터 팩터리 유효성 검사를 통과 해야 합니다. 파이프라인이 완료 되지 않았거나 컴퓨터 작동이 중단 되는 경우에만 변경 내용을 잃지 않으려는 경우, git 통합을 사용 하면 상태에 관계 없이 데이터 팩터리 리소스의 증분 변경 내용을 적용할 수 있습니다. Git 리포지토리를 구성 하면 변경 내용을 저장할 수 있으므로 사용자의 만족도에 대 한 변경 내용을 테스트 한 경우에만 게시할 수 있습니다.
-   **공동 작업 및 제어:** 동일한 팩터리에 참여 하는 팀 멤버가 여러 개 있는 경우 팀 멤버가 코드 검토 프로세스를 통해 서로 공동 작업 하도록 할 수 있습니다. 모든 참가자에 게 동일한 권한이 부여 되지 않도록 팩터리를 설정할 수도 있습니다. 일부 팀 멤버는 Git를 통해서만 변경을 수행할 수 있으며, 팀의 특정 사용자만 변경 내용을 팩터리에 게시할 수 있습니다.
-   **더 나은 CI/CD:**  [연속 배달 프로세스](continuous-integration-deployment.md)를 사용 하 여 여러 환경에 배포 하는 경우 git 통합을 사용 하면 특정 작업을 보다 쉽게 수행할 수 있습니다. 이러한 작업 중 일부는 다음을 포함합니다.
    -   ' Dev ' 팩터리에서 변경한 내용이 있는 즉시 자동으로 트리거되도록 릴리스 파이프라인을 구성 합니다.
    -   리소스 관리자 템플릿에서 매개 변수로 사용할 수 있는 팩터리의 속성을 사용자 지정 합니다. 필수 속성 세트만 매개 변수로 유지하고 다른 모든 항목은 하드 코딩하면 유용할 수 있습니다.
-   **성능 향상:** Git 통합을 사용 하는 평균 팩터리는 데이터 팩터리 서비스에 대 한 작성 보다 10 배 더 빠르게 로드 됩니다. 이러한 성능 향상은 리소스가 Git을 통해 다운로드 되기 때문에 발생 합니다.

> [!NOTE]
> Git 리포지토리가 구성 되 면 Azure Data Factory UX에서 Data Factory 서비스를 사용 하 여 직접 제작할 수 없습니다. PowerShell 또는 SDK를 통해 서비스에 직접 변경 내용을 적용할 수 있습니다.

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos Git 통합을 통한 작성

Azure Repos Git 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 협업을 지원합니다. 사용자는 원본 제어, 협업, 버전 관리 등을 위해 Azure Repos Git 조직 리포지토리와 데이터 팩터리를 연결할 수 있습니다. 단일 Azure Repos Git 계정이 여러 리포지토리를 포함할 수는 있지만 각 Azure Repos Git 리포지토리는 데이터 팩터리 하나에만 연결할 수 있습니다. Azure Repos 조직 또는 리포지토리가 없는 경우 [이러한 지침](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)에 따라 리소스를 만듭니다.

> [!NOTE]
> Azure Repos Git 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. Data Factory 파이프라인은 Azure Repos Git 리포지토리에 저장된 스크립트 또는 데이터 파일을 자동으로 Azure Storage에 업로드하지 않습니다.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Azure Data Factory를 통한 Azure Repos Git 리포지토리 구성

데이터 팩터리를 사용해서 두 가지 방법으로 Azure Repos Git 리포지토리를 구성할 수 있습니다.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>구성 방법 1: 홈 페이지 Azure Data Factory

Azure Data Factory 홈 페이지에서 **코드 리포지토리 설정**을 선택 합니다.

![Azure Repos 코드 리포지토리 구성](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>구성 방법 2: UX 제작 캔버스
Azure Data Factory UX 제작 캔버스에서 **Data Factory** 드롭다운 메뉴를 선택한 다음 **코드 리포지토리 설정**을 선택 합니다.

![UX 제작을 위한 코드 리포지토리 설정 구성](media/author-visually/configure-repo-2.png)

두 방법 모두 리포지토리 설정 구성 창을 엽니다.

![코드 리포지토리 설정 구성](media/author-visually/repo-settings.png)

구성 창에는 다음 Azure Repos 코드 리포지토리 설정이 표시 됩니다.

| 설정 | Description | 값 |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다.<br/> | Azure DevOps Git 또는 GitHub |
| **Azure Active Directory** | Azure AD 테넌트 이름입니다. | `<your tenant name>` |
| **Azure Repos 조직** | Azure Repos 조직 이름입니다. Azure Repos 조직 이름은 `https://{organization name}.visualstudio.com`에서 확인할 수 있습니다. [Azure Repos 조직에 로그인](https://www.visualstudio.com/team-services/git/)하여 Visual Studio 프로필에 액세스하고 리포지토리 및 프로젝트를 확인할 수 있습니다. | `<your organization name>` |
| **이름** | Azure Repos 프로젝트 이름입니다. Azure Repos 프로젝트 이름은 `https://{organization name}.visualstudio.com/{project name}`에서 확인할 수 있습니다. | `<your Azure Repos project name>` |
| **Event.pushnotification.repositoryname** | Azure Repos 코드 리포지토리 이름입니다. Azure Repos 프로젝트는 프로젝트가 확장됨에 따라 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 프로젝트에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your Azure Repos code repository name>` |
| **협업 분기** | 게시에 사용되는 Azure Repos 협업 분기입니다. 기본적으로 `master`입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. | `<your collaboration branch name>` |
| **루트 폴더** | Azure Repos 협업 분기의 루트 폴더입니다. | `<your root folder name>` |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | UX **제작 캔버스**에서 기존 데이터 팩터리 리소스를 Azure Repos Git 리포지토리로 가져올 것인지를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **리소스를 가져올 분기** | 데이터 팩터리 리소스(파이프라인, 데이터 세트, 연결된 서비스 등)를 가져올 분기를 지정합니다. 다음 분기 중 하나로 리소스를 가져올 수 있습니다. a. 협업 b. 새로 만들기 c. 기존 리소스 사용 |  |

> [!NOTE]
> Microsoft Edge를 사용 하 고 있으며 Azure DevOps 계정 드롭다운에서 값이 표시 되지 않는 경우에는 https://*. visualstudio를 신뢰할 수 있는 사이트 목록에 추가 합니다.

### <a name="use-a-different-azure-active-directory-tenant"></a>다른 Azure Active Directory 테넌트 사용

Azure Repos Git 리포지토리는 다른 Azure Active Directory 테 넌 트에 있을 수 있습니다. 다른 Azure AD 테넌트를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다.

### <a name="use-your-personal-microsoft-account"></a>개인 Microsoft 계정 사용

Git 통합에 개인 Microsoft 계정을 사용하려면 개인 Azure 리포지토리를 조직의 Active Directory에 연결하면 됩니다.

1. 개인 Microsoft 계정을 조직의 Active Directory에 게스트로 추가합니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../active-directory/b2b/add-users-administrator.md)를 참조하세요.

2. 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다. 그런 다음, 조직의 Active Directory로 전환합니다.

3. Azure DevOps 섹션으로 이동하면 이제 개인 리포지토리가 보입니다. 리포지토리를 선택하고 Active Directory와 연결합니다.

이러한 구성 단계 후, Data Factory UI에서 Git 통합을 설정할 때 개인 리포지토리를 사용할 수 있습니다.

Azure Repos를 조직의 Active Directory에 연결하는 방법에 대한 자세한 내용은 [Azure DevOps 조직을 Azure Active Directory에 연결](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)을 참조하세요.

## <a name="author-with-github-integration"></a>GitHub 통합을 통한 작성

GitHub 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 협업을 지원합니다. 사용자는 원본 제어, 협업 및 버전 관리를 위해 GitHub 계정 리포지토리에 데이터 팩터리를 연결할 수 있습니다. 단일 GitHub 계정은 여러 리포지토리를 가질 수 있지만 GitHub 리포지토리는 단 하나의 데이터 팩터리와 연결할 수 있습니다. GitHub 계정 또는 리포지토리가 없는 경우 [다음 지침](https://github.com/join) 에 따라 리소스를 만듭니다.

Data Factory와 GitHub 통합은 공용 GitHub (즉, [https://github.com](https://github.com)) 및 GitHub Enterprise를 모두 지원 합니다. GitHub의 리포지토리에 대한 읽기 및 쓰기 권한이 있다면 Data Factory를 사용하여 공용 및 프라이빗 GitHub 리포지토리를 모두 사용할 수 있습니다.

GitHub 리포지토리를 구성 하려면 사용 중인 Azure 구독에 대 한 관리자 권한이 있어야 합니다.

9분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Azure Data Factory를 사용 하 여 GitHub 리포지토리 구성

데이터 팩터리를 사용해서 두 가지 방법으로 GitHub 리포지토리를 구성할 수 있습니다.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>구성 방법 1: 홈 페이지 Azure Data Factory

Azure Data Factory 홈 페이지에서 **코드 리포지토리 설정**을 선택 합니다.

![Azure Repos 코드 리포지토리 구성](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>구성 방법 2: UX 제작 캔버스

Azure Data Factory UX 제작 캔버스에서 **Data Factory** 드롭다운 메뉴를 선택한 다음 **코드 리포지토리 설정**을 선택 합니다.

![UX 제작을 위한 코드 리포지토리 설정 구성](media/author-visually/configure-repo-2.png)

두 방법 모두 리포지토리 설정 구성 창을 엽니다.

![GitHub 리포지토리 설정](media/author-visually/github-integration-image2.png)

구성 창에는 다음 GitHub 리포지토리 설정이 표시 됩니다.

| **설정** | **설명**  | **값**  |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다. | GitHub |
| **GitHub Enterprise 사용** | GitHub Enterprise를 선택하기 위한 확인란 | 선택 하지 않음 (기본값) |
| **GitHub Enterprise URL** | GitHub 엔터프라이즈 루트 URL (로컬 GitHub Enterprise 서버에 대 한 HTTPS 여야 함) 예: `https://github.mydomain.com` **GitHub Enterprise 사용** 을 선택한 경우에만 필요 | `<your GitHub enterprise url>` |                                                           
| **GitHub 계정** | 사용자 GitHub 계정 이름. 이 이름은 https:\//github.com/{account name}/{repository name}에서 찾을 수 있습니다. 이 페이지로 이동하면 GitHub 계정에 GitHub OAuth 자격 증명을 입력하라는 메시지가 표시됩니다. | `<your GitHub account name>` |
| **리포지토리 이름**  | GitHub 코드 리포지토리 이름입니다. GitHub 계정은 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 계정에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your repository name>` |
| **협업 분기** | 게시에 사용되는 GitHub 협업 분기입니다. 기본적으로 마스터입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. | `<your collaboration branch>` |
| **루트 폴더** | GitHub 협업 분기의 루트 폴더입니다. |`<your root folder name>` |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | UX 제작 캔버스에서 GitHub 리포지토리로 기존 data factory 리소스를 가져올지 여부를 지정 합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **리소스를 가져올 분기** | 데이터 팩터리 리소스(파이프라인, 데이터 세트, 연결된 서비스 등)를 가져올 분기를 지정합니다. 다음 분기 중 하나로 리소스를 가져올 수 있습니다. a. 협업 b. 새로 만들기 c. 기존 리소스 사용 |  |

### <a name="known-github-limitations"></a>알려진 GitHub 제한 사항

- GitHub 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. Data Factory 파이프라인은 GitHub 리포지토리에 저장된 스크립트 또는 데이터 파일을 자동으로 Azure Storage에 업로드하지 않습니다.

- 버전 2.14.0 이상의 GitHub Enterprise는 Microsoft Edge 브라우저에서 작동하지 않습니다.

- Data Factory 비주얼 제작 도구와 GitHub 통합은 일반적으로 사용할 수 있는 Data Factory 버전 에서만 작동 합니다.

- 단일 GitHub 분기에서 리소스 유형 (예: 파이프라인 및 데이터 집합) 당 최대 1000 개의 엔터티를 가져올 수 있습니다. 이 한도에 도달 하면 리소스를 별도의 팩터리에 분할 하는 것이 좋습니다. Azure DevOps Git에는 이러한 제한이 없습니다.

## <a name="version-control"></a>버전 제어

_원본 제어_라고도 하는 버전 제어 시스템을 통해 개발자는 코드에 대해 공동 작업을 수행하고, 코드 베이스의 변경 내용을 추적할 수 있습니다. 소스 제어는 개발자가 여러 명인 프로젝트에 반드시 필요한 도구입니다.

### <a name="creating-feature-branches"></a>기능 분기 만들기

데이터 팩터리와 연결된 각 Azure Repos Git 리포지토리에는 협업 분기가 생성됩니다. (`master`는 기본 협업 분기입니다). 사용자는 분기 드롭다운에서 **+ 새 분기** 를 클릭 하 여 기능 분기를 만들 수도 있습니다. 새 분기 창이 표시 되 면 기능 분기의 이름을 입력 합니다.

![새 분기 만들기](media/author-visually/new-branch.png)

기능 분기의 변경 내용을 공동 작업 분기에 병합할 준비가 되 면 분기 드롭다운을 클릭 하 고 **끌어오기 요청 만들기**를 선택 합니다. 이 작업을 통해 끌어오기 요청을 수행하고, 코드 검토를 수행하고, 협업 분기에 변경 내용을 병합할 수 있는 Azure Repos Git로 이동합니다. (기본값은 `master`입니다.) 협업 분기에서 Data Factory 서비스에 게시할 수만 있습니다. 

![새 끌어오기 요청 만들기](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>게시 설정 구성

기본적으로 data factory는 게시 된 팩터리의 리소스 관리자 템플릿을 생성 하 고 라는 `adf_public`분기에 저장 합니다. 사용자 지정 게시 분기를 구성 하려면 공동 작업 `publish_config.json` 분기의 루트 폴더에 파일을 추가 합니다. 게시할 때 ADF는이 파일을 읽고 필드 `publishBranch`를 찾은 다음 모든 리소스 관리자 템플릿을 지정 된 위치에 저장 합니다. 분기가 존재 하지 않는 경우 data factory가 자동으로 만듭니다. 이 파일의 모양은 다음과 같습니다.

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory은 한 번에 하나의 게시 분기만 포함할 수 있습니다. 새 게시 분기를 지정할 때 Data Factory는 이전 게시 분기를 삭제하지 않습니다. 이전 게시 분기를 제거 하려면 수동으로 삭제 합니다.

> [!NOTE]
> Data Factory는 팩터리를 로드할 때만 `publish_config.json` 파일을 읽습니다. 포털에 팩터리가 이미 로드되어 있으면 브라우저를 새로 고쳐서 변경 내용을 적용합니다.

### <a name="publish-code-changes"></a>코드 변경 내용 게시

공동 작업 분기에 변경 내용을 병합 한 후 (`master` 기본값) **게시** 를 클릭 하 여 마스터 분기의 코드 변경 내용을 Data Factory 서비스에 수동으로 게시 합니다.

![Data Factory 서비스에 변경 내용 게시](media/author-visually/publish-changes.png)

왼쪽 창이 열리고 게시 분기 및 보류 중인 변경 내용이 올바른지 확인 합니다. 변경 내용을 확인 한 후 **확인** 을 클릭 하 여 게시를 확인 합니다.

![올바른 게시 분기 확인](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> 마스터 분기는 Data Factory 서비스에 배포된 내용을 반영하지 않습니다. 따라서 마스터 분기를 *반드시* Data Factory 서비스에 수동으로 게시해야 합니다.

## <a name="best-practices-for-git-integration"></a>Git 통합에 대한 모범 사례

### <a name="permissions"></a>사용 권한

일반적으로 모든 팀 멤버에 게 팩터리를 업데이트할 수 있는 권한이 필요 하지 않습니다. 다음 사용 권한 설정을 사용 하는 것이 좋습니다.

*   모든 팀 멤버는 데이터 팩터리에 대한 읽기 권한이 있어야 합니다.
*   선택 된 사용자 집합만 팩터리에 게시할 수 있도록 허용 해야 합니다. 이렇게 하려면 팩터리가 속한 리소스 그룹에 대 한 **Data Factory 참가자** 역할이 있어야 합니다. 사용 권한에 대 한 자세한 내용은 [Azure Data Factory의 역할 및 사용 권한](concepts-roles-permissions.md)을 참조 하세요.
   
공동 작업 분기에 대 한 직접 체크 인을 허용 하지 않는 것이 좋습니다. 이 제한 사항은 모든 체크 인이 [기능 분기 만들기](source-control.md#creating-feature-branches)에 설명 된 끌어오기 요청 검토 프로세스를 거치는 동안 버그를 방지 하는 데 도움이 됩니다.

### <a name="using-passwords-from-azure-key-vault"></a>Azure Key Vault의 암호 사용

Data Factory 연결 된 서비스에 대 한 연결 문자열 또는 암호 또는 관리 되는 id 인증을 저장 하려면 Azure Key Vault를 사용 하는 것이 좋습니다. 보안상의 이유로 데이터 팩터리는 Git에 비밀을 저장 하지 않습니다. 암호와 같은 암호를 포함 하는 연결 된 서비스에 대 한 모든 변경 내용은 Azure Data Factory 서비스에 즉시 게시 됩니다.

또한 Key Vault 또는 MSI 인증을 사용 하면 리소스 관리자 템플릿 배포 중에 이러한 암호를 제공할 필요가 없으므로 연속 통합 및 배포를 더 쉽게 수행할 수 있습니다.

## <a name="troubleshooting-git-integration"></a>Git 통합 문제 해결

### <a name="stale-publish-branch"></a>부실 게시 분기

게시 분기가 master 분기와 동기화 되지 않은 상태에서 최근 게시에도 불구 하 고 오래 된 리소스를 포함 하는 경우 다음 단계를 수행 합니다.

1. 현재 Git 리포지토리 제거
1. 동일한 설정으로 Git를 다시 구성 하지만 **기존 Data Factory 리소스를 리포지토리로 가져오기** 가 선택 되어 있는지 확인 하 고 **새 분기** 를 선택 합니다.
1. 변경 내용을 공동 작업 분기에 병합 하는 끌어오기 요청 만들기 

다음은 오래 된 게시 분기를 발생 시킬 수 있는 상황에 대 한 몇 가지 예입니다.
- 사용자에 게 분기가 여러 개 있습니다. 하나의 기능 분기에서는 AKV 연결 되지 않은 연결 된 서비스를 삭제 했습니다. (AKV 연결 된 서비스는 Git에 있는지 여부에 관계 없이 즉시 게시 되며) 기능 분기가 공동 작업 분기에 병합 되지 않습니다.
- 사용자가 SDK 또는 PowerShell을 사용 하 여 데이터 팩터리를 수정 했습니다.
- 사용자가 모든 리소스를 새 분기로 이동 하 고 처음으로 게시 하려고 했습니다. 리소스를 가져올 때 연결 된 서비스를 수동으로 만들어야 합니다.
- 사용자가 AKV 연결 되지 않은 서비스 또는 Integration Runtime JSON을 수동으로 업로드 합니다. 데이터 집합, 연결 된 서비스 또는 파이프라인과 같은 다른 리소스에서 해당 리소스를 참조 합니다. UX를 통해 만든 AKV 연결 되지 않은 서비스는 자격 증명을 암호화 해야 하기 때문에 즉시 게시 됩니다. 연결 된 서비스를 참조 하는 데이터 집합을 업로드 하 고 게시 하려고 하면 UX는 git 환경에 존재 하므로이를 허용 합니다. 데이터 팩터리 서비스에 존재 하지 않기 때문에 게시 시간에 거부 됩니다.

## <a name="switch-to-a-different-git-repository"></a>다른 Git 리포지토리로 전환

다른 Git 리포지토리로 전환 하려면 Data Factory 개요 페이지의 오른쪽 위 모퉁이에 있는 **Git 리포지토리 설정** 아이콘을 클릭 합니다. 아이콘이 표시 되지 않는 경우 로컬 브라우저 캐시의 선택을 취소 합니다. 아이콘을 선택하여 현재 리포지토리와 연결을 제거합니다.

![Git 아이콘](media/author-visually/remove-repo.png)

리포지토리 설정 창이 표시 되 면 **Git 제거**를 선택 합니다. 데이터 팩터리 이름을 입력 하 고 **확인** 을 클릭 하 여 데이터 팩터리에 연결 된 Git 리포지토리를 제거 합니다.

![현재 Git 리포지토리와 연결 제거](media/author-visually/remove-repo2.png)

현재 리포지토리와의 연결을 제거한 후에는 다른 리포지토리를 사용 하도록 Git 설정을 구성한 다음 기존 Data Factory 리소스를 새 리포지토리로 가져올 수 있습니다.

> [!IMPORTANT]
> 데이터 팩터리에서 Git 구성을 제거 해도 리포지토리에서 아무것도 삭제 되지 않습니다. 팩터리는 게시 된 모든 리소스를 포함 합니다. 계속 해 서 서비스에 대해 팩터리를 직접 편집할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
* 연속 통합 및 배포를 구현 하려면 [Azure Data Factory의 CI/CD (지속적인 통합 및 배달)](continuous-integration-deployment.md)를 참조 하세요.
