---
title: Azure Automation에서 원본 제어 통합
description: 이 문서에서는 Azure Automation에서 GitHub를 사용하는 원본 제어 통합을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eef67ca8111983adb4d9994894ba215240daee6f
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253741"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation에서 원본 제어 통합

 Azure Automation의 소스 제어 통합은 소스 제어 리포지토리에서 단일 방향 동기화를 지원 합니다. 원본 제어를 사용 하면 GitHub 또는 Azure Repos 소스 제어 리포지토리의 스크립트를 사용 하 여 Automation 계정의 runbook을 최신 상태로 유지할 수 있습니다. 이 기능을 사용 하면 개발 환경에서 테스트 된 코드를 프로덕션 Automation 계정으로 쉽게 승격할 수 있습니다.
 
 원본 제어 통합을 사용 하면 팀과 쉽게 공동 작업 하 고, 변경 내용을 추적 하 고, runbook의 이전 버전으로 롤백할 수 있습니다. 예를 들어 소스 제어를 사용 하면 소스 제어의 여러 분기를 개발, 테스트 및 프로덕션 Automation 계정과 동기화 할 수 있습니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="source-control-types"></a>소스 제어 형식

Azure Automation는 다음과 같은 세 가지 형식의 원본 제어를 지원 합니다.

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>사전 요구 사항

* 소스 제어 리포지토리 (GitHub 또는 Azure Repos)
* [실행 계정](manage-runas-account.md)
* **Az. Accounts** 모듈 (AzureRM에 해당 하는 az module)을 포함 하는 Automation 계정의 [최신 Azure 모듈](automation-update-azure-modules.md)

> [!NOTE]
> 원본 제어 동기화 작업은 사용자의 Automation 계정에서 실행 되 고 다른 자동화 작업과 동일한 속도로 청구 됩니다.

## <a name="configuring-source-control"></a>원본 제어 구성

이 섹션에서는 Automation 계정에 대 한 소스 제어를 구성 하는 방법을 설명 합니다. Azure Portal 또는 PowerShell 중 하나를 사용할 수 있습니다.

### <a name="configure-source-control----azure-portal"></a>소스 제어 구성-Azure Portal

Azure Portal를 사용 하 여 소스 제어를 구성 하려면 다음 절차를 따르십시오.

1. Automation 계정 내에서 **원본 제어** 를 선택 하 고 **+ 추가**를 클릭 합니다.

    ![소스 제어 선택](./media/source-control-integration/select-source-control.png)

2. **원본 제어 유형**을 선택한 다음 **인증**을 클릭 합니다. 

3. 브라우저 창이 열리고 로그인 하 라는 메시지가 표시 됩니다. 프롬프트에 따라 인증을 완료 합니다.

4. **원본 제어 요약** 페이지에서 필드를 사용 하 여 아래에 정의 된 원본 제어 속성을 입력 합니다. 완료되면 **저장**을 클릭합니다. 

    |속성  |Description  |
    |---------|---------|
    |소스 제어 이름     | 소스 컨트롤에 대 한 친숙 한 이름입니다. 이 이름에는 문자와 숫자만 포함 되어야 합니다.        |
    |소스 제어 형식     | 원본 제어 메커니즘의 유형입니다. 사용 가능한 옵션은 다음과 같습니다.</br> GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
    |리포지토리     | 리포지토리 또는 프로젝트의 이름입니다. 처음 200 리포지토리가 검색 됩니다. 리포지토리를 검색 하려면 필드에 이름을 입력 하 고 **GitHub에서 검색**을 클릭 합니다.|
    |Branch     | 소스 파일을 끌어올 분기입니다. TFVC 소스 제어 형식에 대해 분기 대상 지정을 사용할 수 없습니다.          |
    |폴더 경로     | 동기화 할 runbook을 포함 하는 폴더 (예:/Runbooks.) 지정 된 폴더의 runbook만 동기화 됩니다. 재귀는 지원 되지 않습니다.        |
    |자동 동기화<sup>1</sup>     | 원본 제어 리포지토리에서 커밋이 수행 될 때 자동 동기화를 설정 하거나 해제 하는 설정입니다.        |
    |Runbook 게시     | 원본 제어에서 동기화 한 후 runbook이 자동으로 게시 되는 경우 On의 설정이 고, 그렇지 않으면입니다.           |
    |Description     | 원본 제어에 대 한 추가 정보를 지정 하는 텍스트입니다.        |

    <sup>1</sup> Azure Repos와 원본 제어 통합을 구성할 때 자동 동기화를 사용 하도록 설정 하려면 프로젝트 관리자 여야 합니다.

   ![소스 제어 요약](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 원본 제어 리포지토리의 로그인이 Azure Portal 로그인과 다를 수 있습니다. 원본 제어를 구성할 때 소스 제어 리포지토리의 올바른 계정으로 로그인 했는지 확인 합니다. 확실 하지 않은 경우 브라우저에서 새 탭을 열고 visualstudio.com 또는 github.com에서 로그 아웃 한 다음 원본 제어에 다시 연결 해 보세요.

### <a name="configure-source-control----powershell"></a>원본 제어 구성-PowerShell

PowerShell을 사용 하 여 Azure Automation에서 소스 제어를 구성할 수도 있습니다. 이 작업에 PowerShell cmdlet을 사용 하려면 PAT (개인용 액세스 토큰)가 필요 합니다. [AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) cmdlet을 사용 하 여 원본 제어 연결을 만듭니다. 이 cmdlet에는 PAT에 대 한 보안 문자열이 필요 합니다. 보안 문자열을 만드는 방법에 대 한 자세한 내용은 [convertto-html](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)를 참조 하세요.

다음 하위 섹션에서는 GitHub, Azure Repos (Git) 및 Azure Repos (TFVC)에 대 한 원본 제어 연결의 PowerShell을 만드는 방법을 보여 줍니다.

#### <a name="create-source-control-connection-for-github"></a>GitHub에 대 한 소스 제어 연결 만들기

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Azure Repos에 대 한 소스 제어 연결 만들기 (Git)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Azure Repos에 대 한 소스 제어 연결 만들기 (TFVC)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>PAT (개인용 액세스 토큰) 권한

원본 제어에는 PATs에 대 한 몇 가지 최소 권한이 필요 합니다. 다음 하위 섹션에는 GitHub 및 Azure Repos에 필요한 최소 사용 권한이 포함 되어 있습니다.

##### <a name="minimum-pat-permissions-for-github"></a>GitHub에 대 한 최소 PAT 권한

다음 표에서는 GitHub에 필요한 최소 PAT 사용 권한을 정의 합니다. GitHub에서 PAT를 만드는 방법에 대 한 자세한 내용은 [명령줄에 대 한 개인용 액세스 토큰 만들기](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)를 참조 하세요.

|범위  |Description  |
|---------|---------|
|**리포지토리**     |         |
|repo:status     | 커밋 상태 액세스         |
|repo_deployment      | 배포 상태 액세스         |
|public_repo     | 공용 리포지토리 액세스         |
|**admin:repo_hook**     |         |
|write:repo_hook     | 리포지토리 후크 쓰기         |
|read:repo_hook|리포지토리 후크 읽기|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure Repos에 대 한 최소 PAT 권한

다음 목록에서는 Azure Repos 하는 데 필요한 최소 PAT 권한을 정의 합니다. Azure Repos에서 PAT를 만드는 방법에 대 한 자세한 내용은 [개인 액세스 토큰을 사용 하 여 액세스 인증](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)을 참조 하세요.

| 범위  |  액세스 형식  |
|---------| ----------|
| 코드      | 읽기  |
| 프로젝트 및 팀 | 읽기 |
| ID | 읽기     |
| 사용자 프로필 | 읽기     |
| 작업 항목 | 읽기    |
| 서비스 연결 | 읽기, 쿼리, 관리<sup>1</sup>    |

<sup>1</sup> 서비스 연결 권한은 autosync를 사용 하도록 설정한 경우에만 필요 합니다.

## <a name="synchronizing"></a>동기화 중

다음 작업을 수행 하 여 원본 제어와 동기화 합니다. 

1. **원본 제어** 페이지의 테이블에서 원본을 선택 합니다. 

2. **동기화 시작**을 클릭하여 동기화 프로세스를 시작합니다. 

3. **동기화 작업** 탭을 클릭 하 여 현재 동기화 작업 또는 이전 작업의 상태를 확인 합니다. 

4. **소스 제어** 드롭다운 메뉴에서 원본 제어 메커니즘을 선택 합니다.

    ![동기화 상태](./media/source-control-integration/sync-status.png)

5. 작업을 클릭하면 작업 출력을 볼 수 있습니다. 다음은 소스 제어 동기화 작업의 출력 예입니다.

    ```output
    ============================================================================

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

     =========================================================================

    ```

6. **소스 제어 동기화 작업 요약** 페이지에서 **모든 로그** 를 선택 하 여 추가 로깅을 사용할 수 있습니다. 이러한 추가 로그 항목은 원본 제어를 사용할 때 발생할 수 있는 문제를 해결 하는 데 도움이 될 수 있습니다.

## <a name="disconnecting-source-control"></a>원본 제어 연결 끊기

소스 제어 리포지토리에서 연결을 끊으려면:

1. Automation 계정의 **계정 설정** 에서 **소스 제어** 를 엽니다.

2. 제거할 원본 제어 메커니즘을 선택 합니다. 

3. **소스 제어 요약** 페이지에서 **삭제**를 클릭합니다.

## <a name="handling-encoding-issues"></a>인코딩 문제 처리

여러 사용자가 서로 다른 편집기를 사용 하 여 소스 제어 리포지토리에서 runbook을 편집 하는 경우 인코딩 문제가 발생할 수 있습니다. 이러한 상황에 대해 자세히 알아보려면 [인코딩 문제의 일반적인 원인](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues) 을 참조 하세요.

## <a name="updating-the-pat"></a>PAT 업데이트

현재 Azure Portal를 사용 하 여 소스 제어에서 PAT를 업데이트할 수 있는 방법은 없습니다. PAT가 만료 되거나 해지 된 후에는 다음 방법 중 하나로 새 액세스 토큰을 사용 하 여 소스 제어를 업데이트할 수 있습니다.

* [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)를 사용 합니다.
* [AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet을 사용 합니다.

## <a name="next-steps"></a>다음 단계

Runbook 형식 및 해당 장점 및 제한 사항에 대 한 자세한 내용은 [runbook types Azure Automation](automation-runbook-types.md)를 참조 하세요.
