---
title: Azure Automation에서 원본 제어 통합
description: 이 문서에서는 Azure Automation에서 GitHub를 사용하는 원본 제어 통합을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132921"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation에서 원본 제어 통합

 Azure Automation의 소스 제어 통합은 소스 제어 리포지토리에서 단일 방향 동기화를 지원합니다. 소스 제어를 사용하면 GitHub 또는 Azure Repos 소스 제어 리포지토리의 스크립트를 사용하여 Runbook을 자동화 계정에 최신 상태로 유지할 수 있습니다. 이 기능을 사용하면 개발 환경에서 테스트된 코드를 프로덕션 자동화 계정으로 쉽게 승격할 수 있습니다.
 
 소스 제어 통합을 사용하면 팀과 쉽게 협업하고 변경 내용을 추적하며 이전 버전의 Runbook으로 롤백할 수 있습니다. 예를 들어 소스 제어를 사용하면 소스 제어의 여러 분기를 개발, 테스트 및 프로덕션 자동화 계정과 동기화할 수 있습니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="source-control-types"></a>소스 제어 유형

Azure 자동화는 세 가지 유형의 소스 제어를 지원합니다.

* GitHub
* Azure 리포지토리(Git)
* Azure 리포지토리(TFVC)

## <a name="prerequisites"></a>사전 요구 사항

* 소스 제어 리포지토리(GitHub 또는 Azure 리포지토리)
* [계정으로 실행](manage-runas-account.md)
* 자동화 [계정의 최신 Azure](automation-update-azure-modules.md) 모듈(Az `Az.Accounts` 모듈과 `AzureRM.Profile`동등한)

> [!NOTE]
> 소스 제어 동기화 작업은 사용자의 자동화 계정에서 실행되며 다른 자동화 작업과 동일한 비율로 청구됩니다.

## <a name="configuring-source-control"></a>소스 제어 구성

이 섹션에서는 자동화 계정에 대한 소스 제어를 구성하는 방법을 설명합니다. Azure 포털 또는 PowerShell을 사용할 수 있습니다.

### <a name="configure-source-control-in-azure-portal"></a>Azure 포털에서 소스 제어 구성

이 절차를 사용하여 Azure 포털을 사용하여 원본 제어를 구성합니다.

1. 자동화 계정에서 소스 **제어를** 선택하고 **추가 를**클릭합니다.

    ![소스 제어 선택](./media/source-control-integration/select-source-control.png)

2. **소스 제어 유형을**선택한 다음 인증을 **클릭합니다.** 

3. 브라우저 창이 열리고 로그인하라는 메시지가 표시됩니다. 프롬프트에 따라 인증을 완료합니다.

4. 소스 제어 요약 페이지에서 필드를 사용하여 아래에 정의된 소스 제어 속성을 채웁니다. 완료되면 **저장**을 클릭합니다. 

    |속성  |설명  |
    |---------|---------|
    |소스 제어 이름     | 소스 컨트롤에 대 한 친숙한 이름입니다. 이 이름에는 문자와 숫자만 포함되어야 합니다.        |
    |소스 제어 형식     | 소스 제어 메커니즘의 유형입니다. 사용 가능한 옵션은 다음과 같습니다.</br> * GitHub</br>* Azure 리포지토리(Git)</br> * Azure 리포지토리(TFVC)        |
    |리포지토리     | 리포지토리 또는 프로젝트의 이름입니다. 처음 200개의 리포지토리가 검색됩니다. 리포지토리를 검색하려면 필드에 이름을 입력하고 **GitHub 에서 검색을**클릭합니다.|
    |Branch     | 소스 파일을 가져올 분기입니다. TFVC 소스 제어 유형에는 분기 타겟팅을 사용할 수 없습니다.          |
    |폴더 경로     | 동기화할 Runbook이 포함된 폴더(예: **/Runbooks.** 지정된 폴더의 Runbook만 동기화됩니다. 재귀는 지원되지 않습니다.        |
    |자동 동기화<sup>1</sup>     | 소스 제어 리포지토리에서 커밋이 수행될 때 자동 동기화가 켜지거나 꺼지는 설정입니다.        |
    |Runbook 게시     | 소스 제어에서 동기화한 후 런북이 자동으로 게시되는 경우 On 설정 및 꺼지기           |
    |설명     | 소스 컨트롤에 대한 추가 세부 정보를 지정하는 텍스트입니다.        |

    <sup>1</sup> Azure Repos와 소스 제어 통합을 구성할 때 자동 동기화를 사용하려면 프로젝트 관리자여야 합니다.

   ![소스 제어 요약](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 원본 제어 리포지토리에 대한 로그인은 Azure 포털에 대한 로그인과 다를 수 있습니다. 소스 제어를 구성할 때 소스 제어 리포지토리에 대한 올바른 계정으로 로그인해야 합니다. 의심스러운 경우 브라우저에서 새 탭을 열고 **dev.azure.com**, **visualstudio.com**또는 **github.com**로그아웃한 다음 소스 제어에 다시 연결해 봅을 시도합니다.

### <a name="configure-source-control-in-powershell"></a>PowerShell에서 소스 제어 구성

PowerShell을 사용하여 Azure 자동화에서 소스 제어를 구성할 수도 있습니다. 이 작업에 PowerShell cmdlet을 사용하려면 PAT(개인 액세스 토큰)가 필요합니다. [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) cmdlet을 사용하여 소스 제어 연결을 만듭니다. 이 cmdlet에는 PAT에 대한 보안 문자열이 필요합니다. 보안 문자열을 만드는 방법에 대해 알아보려면 [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)을 참조하십시오.

다음 하위 섹션에서는 GitHub, Azure 리포지토리(Git) 및 AZURE 리포지토리(TFVC)에 대한 소스 제어 연결을 PowerShell으로 만드는 것을 보여 줍니다. 

#### <a name="create-source-control-connection-for-github"></a>GitHub에 대한 소스 제어 연결 만들기

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Azure 리포지토리(Git)에 대한 소스 제어 연결 만들기

> [!NOTE]
> Azure Repos(Git)는 이전 형식에서 사용된 **visualstudio.com**대신 **dev.azure.com** 액세스하는 URL을 사용합니다. 이전 URL `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` 형식은 더 이상 사용되지 않지만 여전히 지원됩니다. 새 형식이 선호됩니다.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Azure 리포지토리(TFVC)에 대한 소스 제어 연결 만들기

> [!NOTE]
> TFVC(Azure Repos)는 이전 형식에서 사용되는 **visualstudio.com**대신 **dev.azure.com** 액세스하는 URL을 사용합니다. 이전 URL `https://<accountname>.visualstudio.com/<projectname>/_versionControl` 형식은 더 이상 사용되지 않지만 여전히 지원됩니다. 새 형식이 선호됩니다.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>개인 액세스 토큰(PAT) 권한

소스 컨트롤에는 PAT에 대한 몇 가지 최소 사용 권한이 필요합니다. 다음 하위 섹션에는 GitHub 및 Azure 리포지토리에 필요한 최소 사용 권한이 포함되어 있습니다.

##### <a name="minimum-pat-permissions-for-github"></a>GitHub에 대한 최소 PAT 권한

다음 표는 GitHub에 필요한 최소 PAT 권한을 정의합니다. GitHub에서 PAT를 만드는 것에 대한 자세한 내용은 [명령줄에 대한 개인 액세스 토큰 만들기를](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)참조하십시오.

|Scope  |설명  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | 커밋 상태 액세스         |
|`repo_deployment`      | 배포 상태 액세스         |
|`public_repo`     | 공용 리포지토리 액세스         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | 리포지토리 후크 쓰기         |
|`read:repo_hook`|리포지토리 후크 읽기|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure 리포지토리에 대한 최소 PAT 권한

다음 목록에서는 Azure Repos에 필요한 최소 PAT 권한을 정의합니다. Azure 리포지토리에서 PAT를 만드는 것에 대한 자세한 내용은 [개인 액세스 토큰을 통해 액세스 인증을 참조하세요.](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)

| Scope  |  액세스 형식  |
|---------| ----------|
| `Code`      | 읽기  |
| `Project and team` | 읽기 |
| `Identity` | 읽기     |
| `User profile` | 읽기     |
| `Work items` | 읽기    |
| `Service connections` | 읽기, 쿼리, 관리<sup>1</sup>    |

<sup>1</sup> `Service connections` 자동 동기화를 사용하도록 설정한 경우에만 권한이 필요합니다.

## <a name="synchronizing"></a>동기화 중

다음 단계에 따라 소스 제어와 동기화합니다. 

1. 소스 제어 페이지의 테이블에서 소스를 선택합니다. 

2. **동기화 시작**을 클릭하여 동기화 프로세스를 시작합니다. 

3. 작업 동기화 **탭을** 클릭하여 현재 동기화 작업 또는 이전 작업의 상태를 봅니다. 

4. 소스 **제어** 드롭다운 메뉴에서 소스 제어 메커니즘을 선택합니다.

    ![동기화 상태](./media/source-control-integration/sync-status.png)

5. 작업을 클릭하면 작업 출력을 볼 수 있습니다. 다음은 소스 제어 동기화 작업의 출력 예입니다.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. 소스 제어 동기화 작업 요약 페이지에서 **모든 로그를** 선택하여 추가 로깅을 사용할 수 있습니다. 이러한 추가 로그 항목은 소스 제어를 사용할 때 발생할 수 있는 문제를 해결하는 데 도움이 될 수 있습니다.

## <a name="disconnecting-source-control"></a>원본 제어 연결 끊기

소스 제어 리포지토리에서 연결을 끊는 려면 다음을 수행하십시오.

1. 자동화 계정의 **계정 설정에서** 오픈 **소스 제어.**

2. 제거할 소스 제어 메커니즘을 선택합니다. 

3. 소스 제어 요약 페이지에서 **삭제**를 클릭합니다.

## <a name="handling-encoding-issues"></a>인코딩 문제 처리

여러 사람이 다른 편집기에서 소스 제어 리포지토리에서 Runbook을 편집하는 경우 인코딩 문제가 발생할 수 있습니다. 이 상황에 대한 자세한 내용은 [인코딩 문제의 일반적인 원인을](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)참조하십시오.

## <a name="updating-the-pat"></a>PAT 업데이트

현재 Azure 포털을 사용하여 소스 제어에서 PAT를 업데이트할 수 없습니다. PAT가 만료되거나 해지되면 다음 방법 중 하나로 새 액세스 토큰으로 소스 제어를 업데이트할 수 있습니다.

* 나머지 [API를](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)사용합니다.
* [업데이트-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet을 사용합니다.

## <a name="next-steps"></a>다음 단계

Runbook 유형 및 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 유형을](automation-runbook-types.md)참조하십시오.
