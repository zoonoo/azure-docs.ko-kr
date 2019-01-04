---
title: Azure Data Factory의 시각적 작성 | Microsoft Docs
description: Azure Data Factory에서 시각적 작성을 사용하는 방법에 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/7/2018
ms.author: shlo
ms.openlocfilehash: 7a0d72ac67b329cb6d25be955205a2bbcef38e81
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281696"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory에서 시각적 작성
Azure Data Factory UX(사용자 인터페이스 환경)을 사용하면 코드를 작성할 필요 없이 데이터 팩터리에 대한 리소스를 시각적으로 작성하고 배포할 수 있습니다. 파이프라인 캔버스에 작업을 끌어다 놓고, 테스트 실행을 수행하고, 반복적으로 디버그하고, 파이프라인 실행을 배포 및 모니터링할 수 있습니다. 시각적 작성을 수행하기 위해 UX를 사용하는 두 가지 방법이 있습니다.

- Data Factory 서비스로 직접 작성합니다.
- 공동 작업, 원본 제어 또는 버전 관리용으로 Azure Repos Git 통합 기능을 사용하여 작성합니다.

## <a name="author-directly-with-the-data-factory-service"></a>Data Factory 서비스로 직접 작성
Data Factory 서비스를 통한 시각적 작성은 Git 통합 기능을 사용한 시각적 작성과는 두 가지 면에서 다릅니다.

- Data Factory 서비스는 사용자의 변경 사항을 위한 JSON 엔터티를 저장하는 리포지토리를 포함하지 않습니다.
- Data Factory 서비스는 공동 작업 또는 버전 제어에 최적화되지 않습니다.

![Data Factory 서비스 구성 ](media/author-visually/configure-data-factory.png)

UX **제작 캔버스**를 사용하여 Data Factory 서비스를 통해 직접 작성하는 경우 **모두 게시** 모드만 사용할 수 있습니다. 변경한 내용은 Data Factory 서비스에 직접 게시됩니다.

![게시 모드](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos Git 통합을 통한 작성
Azure Repos Git 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 공동 작업을 지원합니다. 사용자는 원본 제어, 공동 작업, 버전 관리 등을 위해 Azure Repos Git 조직 리포지토리와 데이터 팩터리를 연결할 수 있습니다. 단일 Azure Repos Git 계정이 여러 리포지토리를 포함할 수는 있지만 각 Azure Repos Git 리포지토리는 데이터 팩터리 하나에만 연결할 수 있습니다. Azure Repos 조직 또는 리포지토리가 없는 경우 [이러한 지침](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)에 따라 리소스를 만듭니다.

> [!NOTE]
> Azure Repos Git 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. Data Factory 파이프라인은 Azure Repos Git 리포지토리에 저장된 스크립트 또는 데이터 파일을 자동으로 Azure Storage에 업로드하지 않습니다.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Azure Data Factory를 통한 Azure Repos Git 리포지토리 구성
데이터 팩터리를 사용해서 두 가지 방법으로 Azure Repos Git 리포지토리를 구성할 수 있습니다.

#### <a name="method1"></a> 구성 방법 1(Azure Repos Git 리포지토리): 시작하기 페이지

Azure Data Factory에서 **시작하기** 페이지로 이동합니다. **코드 리포지토리 구성**을 선택합니다.

![Azure Repos 코드 리포지토리 구성](media/author-visually/configure-repo.png)

**리포지토리 설정** 구성 창이 나타납니다.

![코드 리포지토리 설정 구성](media/author-visually/repo-settings.png)

창에 다음 Azure Repos 코드 리포지토리 설정이 표시됩니다.

| 설정 | 설명 | 값 |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다.<br/>**참고**: GitHub는 현재 지원되지 않습니다. | Azure Repos Git |
| **Azure Active Directory** | Azure AD 테넌트 이름입니다. | <your tenant name> |
| **Azure Repos 조직** | Azure Repos 조직 이름입니다. Azure Repos 조직 이름은 `https://{organization name}.visualstudio.com`에서 확인할 수 있습니다. [Azure Repos 조직에 로그인](https://www.visualstudio.com/team-services/git/)하여 Visual Studio 프로필에 액세스하고 리포지토리 및 프로젝트를 확인할 수 있습니다. | <your organization name> |
| **ProjectName** | Azure Repos 프로젝트 이름입니다. Azure Repos 프로젝트 이름은 `https://{organization name}.visualstudio.com/{project name}`에서 확인할 수 있습니다. | <your Azure Repos project name> |
| **RepositoryName** | Azure Repos 코드 리포지토리 이름입니다. Azure Repos 프로젝트는 프로젝트가 확장됨에 따라 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 프로젝트에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | <your Azure Repos code repository name> |
| **공동 작업 분기** | 게시에 사용되는 Azure Repos 공동 작업 분기입니다. 기본값은 `master`입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. | <your collaboration branch name> |
| **루트 폴더** | Azure Repos 공동 작업 분기의 루트 폴더입니다. | <your root folder name> |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | UX **제작 캔버스**에서 기존 데이터 팩터리 리소스를 Azure Repos Git 리포지토리로 가져올 것인지를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>구성 방법 2(Azure Repos Git 리포지토리): UX 제작 캔버스
Azure Data Factory UX **제작 캔버스**에서 사용자의 데이터 팩터리를 찾습니다. **Data Factory** 드롭다운 메뉴를 선택한 다음, **코드 리포지토리 구성**을 선택합니다.

구성 창이 나타납니다. 구성 설정에 대한 자세한 내용은 <a href="#method1">구성 방법 1</a>의 설명을 참조하세요.

![UX 제작을 위한 코드 리포지토리 설정 구성](media/author-visually/configure-repo-2.png)

## <a name="use-a-different-azure-active-directory-tenant"></a>다른 Azure Active Directory 테넌트 사용

다른 Azure Active Directory 테넌트에서 Azure Repos Git 리포지토리를 만들 수 있습니다. 다른 Azure AD 테넌트를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다.

## <a name="use-your-personal-microsoft-account"></a>개인 Microsoft 계정 사용

Git 통합에 개인 Microsoft 계정을 사용하려면 개인 Azure 리포지토리를 조직의 Active Directory에 연결하면 됩니다.

1. 개인 Microsoft 계정을 조직의 Active Directory에 게스트로 추가합니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 공동 작업 사용자 추가](../active-directory/b2b/add-users-administrator.md)를 참조하세요.

2. 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다. 그런 다음, 조직의 Active Directory로 전환합니다.

3. Azure DevOps 섹션으로 이동하면 이제 개인 리포지토리가 보입니다. 리포지토리를 선택하고 Active Directory와 연결합니다.

이러한 구성 단계 후, Data Factory UI에서 Git 통합을 설정할 때 개인 리포지토리를 사용할 수 있습니다.

Azure Repos를 조직의 Active Directory에 연결하는 방법에 대한 자세한 내용은 [Azure DevOps 조직을 Azure Active Directory에 연결](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)을 참조하세요.

## <a name="switch-to-a-different-git-repo"></a>다른 Git 리포지토리로 전환

다른 Git 리포지토리로 전환하려면 다음 스크린샷에 표시된 대로 Data Factory 개요 페이지의 위쪽 오른쪽 모서리에서 아이콘을 찾습니다. 아이콘이 표시되지 않으면 로컬 브라우저 캐시의 선택을 취소합니다. 아이콘을 선택하여 현재 리포지토리와 연결을 제거합니다.

현재 리포지토리와 연결을 제거한 후에 다른 리포지토리를 사용하도록 Git 설정을 구성할 수 있습니다. 그러면 새로운 리포지토리로 기존 Data Factory 리소스를 가져올 수 있습니다.

![현재 Git 리포지토리와 연결 제거](media/author-visually/remove-repo.png)

## <a name="use-version-control"></a>사용자 버전 제어
_원본 제어_라고도 하는 버전 제어 시스템을 통해 개발자는 코드에 대해 공동 작업을 수행하고, 코드 베이스의 변경 내용을 추적할 수 있습니다. 소스 제어는 개발자가 여러 명인 프로젝트에 반드시 필요한 도구입니다.

데이터 팩터리와 연결된 각 Azure Repos Git 리포지토리에는 공동 작업 분기가 생성됩니다. (`master`는 기본 공동 작업 분기입니다.) 사용자는 **+ 새 분기**를 클릭하여 기능 분기를 만들고 기능 분기에서 개발을 수행할 수도 있습니다.

![동기화 또는 게시로 코드 변경](media/author-visually/sync-publish.png)

기능 분기에서 기능을 개발할 준비가 되면 **끌어오기 요청 만들기**를 클릭할 수 있습니다. 이 작업을 통해 끌어오기 요청을 수행하고, 코드 검토를 수행하고, 공동 작업 분기에 변경 내용을 병합할 수 있는 Azure Repos Git로 이동합니다. (기본값은 `master`입니다.) 공동 작업 분기에서 Data Factory 서비스에 게시할 수만 있습니다. 

![새 끌어오기 요청 만들기](media/author-visually/create-pull-request.png)

## <a name="configure-publishing-settings"></a>게시 설정 구성

게시 분기(Resource Manager 템플릿이 저장되는 분기)를 구성하려면 공동 작업 분기의 루트 폴더에 `publish_config.json` 파일을 추가합니다. Data Factory는 이 파일을 읽고 `publishBranch` 필드를 찾은 다음 제공된 값을 사용하여 새 분기(없는 경우)를 만듭니다. 그런 다음 지정된 위치에 모든 Resource Manager 템플릿을 저장합니다. 예: 

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Git 모드에서 게시하는 경우 아래 스크린샷에 나와 있는 것처럼 Data Factory가 필요한 게시 분기를 사용 중임을 확인할 수 있습니다.

![올바른 게시 분기 확인](media/author-visually/configure-publish-branch.png)

새 게시 분기를 지정할 때 Data Factory는 이전 게시 분기를 삭제하지 않습니다. 이전 게시 분기를 제거하려는 경우에는 수동으로 해당 분기를 삭제합니다.

Data Factory는 팩터리를 로드할 때만 `publish_config.json` 파일을 읽습니다. 포털에 팩터리가 이미 로드되어 있으면 브라우저를 새로 고쳐서 변경 내용을 적용합니다.

## <a name="publish-code-changes"></a>코드 변경 내용 게시
공동 작업 분기에 변경 내용을 병합한 후에(기본값은 `master`) **게시**를 선택하여 마스터 분기의 코드 변경 내용을 Data Factory 서비스에 수동으로 게시합니다.

![Data Factory 서비스에 변경 내용 게시](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> 마스터 분기는 Data Factory 서비스에 배포된 내용을 반영하지 않습니다. 따라서 마스터 분기를 *반드시* Data Factory 서비스에 수동으로 게시해야 합니다.

## <a name="author-with-github-integration"></a>GitHub 통합을 통한 작성

GitHub 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 공동 작업을 지원합니다. 사용자는 원본 제어, 공동 작업 및 버전 관리를 위해 GitHub 계정 리포지토리에 데이터 팩터리를 연결할 수 있습니다. 단일 GitHub 계정은 여러 리포지토리를 가질 수 있지만 GitHub 리포지토리는 단 하나의 데이터 팩터리와 연결할 수 있습니다. GitHub 계정 또는 리포지토리가 없는 경우  [이러한 지침](https://github.com/join) 에 따라 리소스를 만듭니다.

Data Factory를 사용한 GitHub 통합은 공용 GitHub(즉, [https://github.com](https://github.com)) 및 GitHub Enterprise 모두를 지원합니다. GitHub의 리포지토리에 대한 읽기 및 쓰기 권한이 있다면 Data Factory를 사용하여 공용 및 개인 GitHub 리포지토리를 모두 사용할 수 있습니다.

GitHub 리포지토리를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다.

9분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>제한 사항

- GitHub 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. Data Factory 파이프라인은 GitHub 리포지토리에 저장된 스크립트 또는 데이터 파일을 자동으로 Azure Storage에 업로드하지 않습니다.

- 버전 2.14.0 이상의 GitHub Enterprise는 Microsoft Edge 브라우저에서 작동하지 않습니다.

- Data Factory 시각적 저작 도구를 사용한 GitHub 통합은 Data Factory의 일반적으로 사용 가능한 버전에서만 작동합니다.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Azure Data Factory를 통한 공용 GitHub 리포지토리 구성

데이터 팩터리를 사용해서 두 가지 방법으로 GitHub 리포지토리를 구성할 수 있습니다.

**구성 방법 1(공용 리포지토리): 시작하기 페이지**

Azure Data Factory에서  **시작하기**  페이지로 이동합니다. 코드  **리포지토리 구성**을 선택합니다.

![Data Factory 시작 페이지](media/author-visually/github-integration-image1.png)

리 **포지토리 설정**  구성 창이 나타납니다.

![GitHub 리포지토리 설정](media/author-visually/github-integration-image2.png)

창에 다음 Azure Repos 코드 리포지토리 설정이 표시됩니다.

| **설정**                                              | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                   | **값**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **리포지토리 유형**                                      | Azure Repos 코드 리포지토리의 유형입니다.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub 계정**                                       | 사용자 GitHub 계정 이름. 이 이름은 https://github.com/{account 이름}/{리포지토리 이름}에서 찾을 수 있습니다. 이 페이지로 이동하면 GitHub 계정에 GitHub OAuth 자격 증명을 입력하라는 메시지가 표시됩니다.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | GitHub 코드 리포지토리 이름입니다. GitHub 계정은 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 계정에 이미 있는 기존 리포지토리를 사용할 수 있습니다.                                                                                                                                                                                                                              |                    |
| **공동 작업 분기**                                 | 게시에 사용되는 GitHub 공동 작업 분기입니다. 기본값은 master입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다.                                                                                                                                                                                                                                                               |                    |
| **루트 폴더**                                          | GitHub 공동 작업 분기의 루트 폴더입니다.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | 기존 데이터 팩터리 리소스를 UX  **제작 캔버스** 에서 GitHub 리포지토리로 가져올지 여부를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **리소스를 가져올 분기**                       | 데이터 팩터리 리소스(파이프라인, 데이터 세트, 연결된 서비스 등)를 가져올 분기를 지정합니다. 다음 분기 중 하나로 리소스를 가져올 수 있습니다. a. 공동 작업 b. 새로 만들기 c. 기존 리소스 사용                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>구성 방법 2(공용 리포지토리): UX 제작 캔버스

Azure Data Factory UX  **제작 캔버스**에서 사용자의 데이터 팩터리를 찾습니다.  **Data Factory** 드롭다운 메뉴를 선택한 다음, 코드  **리포지토리 구성**을 선택합니다.

구성 창이 나타납니다. 구성 설정에 대한 자세한 내용은 위의  *구성 방법 1*에 있는 설명을 참조하세요.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Azure Data Factory를 통한 GitHub Enterprise 리포지토리 구성

데이터 팩터리를 사용해서 두 가지 방법으로 GitHub Enterprise 리포지토리를 구성할 수 있습니다.

 #### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>구성 방법 1(Enterprise 리포지토리): 시작하기 페이지

Azure Data Factory에서  **시작하기**  페이지로 이동합니다. 코드  **리포지토리 구성**을 선택합니다.

![Data Factory 시작 페이지](media/author-visually/github-integration-image1.png)

리 **포지토리 설정**  구성 창이 나타납니다.

![GitHub 리포지토리 설정](media/author-visually/github-integration-image3.png)

창에 다음 Azure Repos 코드 리포지토리 설정이 표시됩니다.

| **설정**                                              | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                   | **값**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **리포지토리 유형**                                      | Azure Repos 코드 리포지토리의 유형입니다.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub Enterprise 사용**                                | GitHub Enterprise를 선택하기 위한 확인란                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **GitHub Enterprise URL**                                | GitHub Enterprise 루트 URL입니다. 예: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **GitHub 계정**                                       | 사용자 GitHub 계정 이름. 이 이름은 https://github.com/{account 이름}/{리포지토리 이름}에서 찾을 수 있습니다. 이 페이지로 이동하면 GitHub 계정에 GitHub OAuth 자격 증명을 입력하라는 메시지가 표시됩니다.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | GitHub 코드 리포지토리 이름입니다. GitHub 계정은 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 계정에 이미 있는 기존 리포지토리를 사용할 수 있습니다.                                                                                                                                                                                                                              |                    |
| **공동 작업 분기**                                 | 게시에 사용되는 GitHub 공동 작업 분기입니다. 기본값은 master입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다.                                                                                                                                                                                                                                                               |                    |
| **루트 폴더**                                          | GitHub 공동 작업 분기의 루트 폴더입니다.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | 기존 데이터 팩터리 리소스를 UX  **제작 캔버스** 에서 GitHub 리포지토리로 가져올지 여부를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **리소스를 가져올 분기**                       | 데이터 팩터리 리소스(파이프라인, 데이터 세트, 연결된 서비스 등)를 가져올 분기를 지정합니다. 다음 분기 중 하나로 리소스를 가져올 수 있습니다. a. 공동 작업 b. 새로 만들기 c. 기존 리소스 사용                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>구성 방법 2(Enterprise 리포지토리): UX 제작 캔버스

Azure Data Factory UX  **제작 캔버스**에서 사용자의 데이터 팩터리를 찾습니다.  **Data Factory** 드롭다운 메뉴를 선택한 다음, 코드  **리포지토리 구성**을 선택합니다.

구성 창이 나타납니다. 구성 설정에 대한 자세한 내용은 위의  *구성 방법 1*에 있는 설명을 참조하세요.

## <a name="use-the-expression-language"></a>식 언어 사용
Azure Data Factory에서 지원하는 식 언어를 사용하여 속성 값에 대한 식을 지정할 수 있습니다.

**동적 콘텐츠 추가**를 선택하여 속성 값에 대한 식을 지정합니다.

![식 언어 사용](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>함수 및 매개 변수 사용

Data Factory **식 작성기**에서 파이프라인 및 데이터 세트에 대해 함수를 사용하거나 매개 변수를 지정할 수 있습니다.

지원되는 식에 대한 자세한 내용은 [Azure Data Factory의 식 및 함수](control-flow-expression-language-functions.md)를 참조하세요.

![동적 콘텐츠 추가](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>피드백 제공
기능에 대한 의견을 남기거나 Microsoft에 도구 관련 문제를 알리려면 **피드백**을 선택하세요.

![사용자 의견](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>다음 단계
파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
