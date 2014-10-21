<properties pageTitle="Managing Role-Based Access Control with Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Managing role-based access control with Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Windows PowerShell을 사용하여 역할 기반 액세스 제어 관리

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/ko-kr/documentation/articles/xplat-cli-rbac.md" title="플랫폼 간 CLI">플랫폼 간 CLI</a></div>

Azure 미리 보기 포털 및 Azure 리소스 관리자 API의 RBAC(역할 기반 액세스 제어)를 사용하면 세밀한 수준에서 구독에 대한 액세스를 관리할 수 있습니다. 이 기능을 통해 특정 범위에서 Active Directory 사용자, 그룹 또는 서비스 사용자에게 일부 역할을 할당하여 액세스 권한을 부여할 수 있습니다.

이 자습서에서는 Windows PowerShell을 사용하여 RBAC를 관리하는 방법에 대해 알아보고 역할 할당을 만들고 확인하는 프로세스를 단계별로 살펴봅니다.

**예상 완료 시간:** 15분

## 필수 조건

Windows PowerShell을 사용하여 RBAC를 관리하려면 다음 항목이 필요합니다.

-   Windows PowerShell 버전 3.0 또는 4.0. Windows PowerShell 버전을 확인하려면 `$PSVersionTable`을 입력합니다. `PSVersion`의 값이 3.0인지 4.0인지를 확인합니다. 호환 버전을 설치하려면 [Windows Management Framework 3.0][Windows Management Framework 3.0] 또는 [Windows Management Framework 4.0][Windows Management Framework 4.0]을 참조하세요.

-   Azure PowerShell 버전 0.8.8 이상. 최신 버전을 설치하여 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성 방법][Azure PowerShell 설치 및 구성 방법](영문)을 참조하십시오.

이 자습서는 Windows PowerShell 초보자용으로 설계되었지만, 모듈, cmdlet, 세션 등과 같은 기본 개념을 잘 알고 있다고 가정합니다. Windows PowerShell에 대한 자세한 내용은 [Windows PowerShell 시작][Windows PowerShell 시작](영문)을 참조하십시오.

이 설명서에 나오는 cmdlet에 대한 자세한 도움말을 보려면 Get-Help cmdlet을 사용합니다.

    Get-Help <cmdlet-name> -Detailed

예를 들어 Add-AzureAccount cmdlet에 대한 도움말을 보려면 다음과 같이 입력합니다.

    Get-Help Add-AzureAccount -Detailed

Windows PowerShell에서 Azure 리소스 관리자를 설치하고 사용하는 방법을 익히려면 다음 자습서도 확인하세요.

-   [Azure PowerShell을 설치 및 구성하는 방법(영문)][Azure PowerShell을 설치 및 구성하는 방법(영문)]
-   [리소스 관리자에서 Windows PowerShell 사용][리소스 관리자에서 Windows PowerShell 사용]

## 자습서 내용

-   [구독에 연결][구독에 연결]
-   [기존 역할 할당 확인][기존 역할 할당 확인]
-   [역할 할당 만들기][역할 할당 만들기]
-   [권한 확인][권한 확인]
-   [다음 단계][다음 단계]

## <span id="connect"></span></a>구독에 연결

RBAC는 Azure 리소스 관리자에서만 작동하므로 먼저 Azure 리소스 관리자 모드로 전환해야 합니다. 이렇게 하려면 다음을 입력합니다.

    PS C:PS C:\> Switch-AzureMode -Name AzureResourceManagergt; Switch-AzureMode -Name AzureResourceManager

자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용][리소스 관리자에서 Windows PowerShell 사용]을 참조하세요.

Azure 구독에 연결하려면 다음을 입력합니다.

    PS C:PS C:\> Add-AzureAccountgt; Add-AzureAccount

팝업 브라우저 컨트롤에 Azure 계정 사용자 이름 및 암호를 입력합니다. PowerShell은 이 계정에 포함된 모든 구독을 가져오며 첫 번째 구독을 기본값으로 사용하도록 구성합니다. RBAC를 사용하는 경우에는 자신이 공동 관리자이거나 역할을 할당받아 특정 권한을 가진 구독만 가져올 수 있습니다.

구독이 여러 개인 경우 다른 구독으로 전환하려면 다음을 입력합니다.

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법][Azure PowerShell을 설치 및 구성하는 방법(영문)]을 참조하세요.

## <span id="check"></span></a>기존 역할 할당 확인

구독에 이미 포함되어 있는 역할 할당을 확인해 보겠습니다. 형식:

    PS C:PS C:\> Get-AzureRoleAssignmentgt; Get-AzureRoleAssignment

그러면 구독의 모든 역할 할당이 반환됩니다. 여기서 유의해야 할 점은 다음의 두 가지입니다.

1.  구독 수준에서 읽기 권한이 있어야 합니다.
2.  구독에 역할 할당이 많은 경우 모든 할당을 가져오려면 시간이 오래 걸릴 수 있습니다.

특정 범위에서 특정 사용자에 대한 특정 역할 정의의 기존 역할 할당을 확인할 수도 있습니다. 형식:

    PS C:PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Ownergt; Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

그러면 AD 테넌트에서 리소스 그룹 "group1"에 대해 "Owner" 역할이 할당된 특정 사용자에 대한 모든 역할 할당이 반환됩니다. 역할 할당은 다음의 두 위치에서 수행될 수 있습니다.

1.  리소스 그룹의 사용자에 대한 "Owner" 역할 할당
2.  리소스 그룹 부모 항목(여기서는 구독)의 사용자에 대한 "Owner" 역할 할당. 특정 수준에서 권한이 있으면 해당 수준의 모든 자식 항목에도 같은 권한이 있기 때문입니다.

이 cmdlet의 모든 매개 변수는 선택 사항입니다. 매개 변수를 적절하게 조합하여 각기 다른 필터로 역할 할당을 확인할 수 있습니다.

## <span id="create"></span></a>역할 할당 만들기

역할 할당을 만들려면 다음 사항을 고려해야 합니다.

-   역할을 할당할 대상: 다음 Azure Active Directory cmdlet을 사용하여 AD 테넌트에 포함된 사용자, 그룹 및 서비스 사용자를 확인할 수 있습니다.

    `PS C:PS C:\> Get-AzureADUser PS C:\> Get-AzureADGroup PS C:\> Get-AzureADGroupMember PS C:\> Get-AzureADServicePrincipal`gt; Get-AzureADUser PS C:`PS C:\> Get-AzureADUser PS C:\> Get-AzureADGroup PS C:\> Get-AzureADGroupMember PS C:\> Get-AzureADServicePrincipal`gt; Get-AzureADGroup PS C:`PS C:\> Get-AzureADUser PS C:\> Get-AzureADGroup PS C:\> Get-AzureADGroupMember PS C:\> Get-AzureADServicePrincipal`gt; Get-AzureADGroupMember PS C:`PS C:\> Get-AzureADUser PS C:\> Get-AzureADGroup PS C:\> Get-AzureADGroupMember PS C:\> Get-AzureADServicePrincipal`gt; Get-AzureADServicePrincipal</code>

-   할당할 역할: 다음 cmdlet을 사용하여 지원되는 역할 정의를 확인할 수 있습니다.

    `PS C:PS C:\> Get-AzureRoleDefinition`gt; Get-AzureRoleDefinition</code>

-   할당할 범위: 범위에는 세 가지 수준이 있습니다.

    -   현재 구독
    -   리소스 그룹. 리소스 그룹 목록을 가져오려면 `PS C:PS C:\> Get-AzureResourceGroup`gt; Get-AzureResourceGroup</code>을 입력합니다.
    -   리소스. 리소스 목록을 가져오려면 `PS C:PS C:\> Get-AzureResource`gt; Get-AzureResource</code>를 입력합니다.

그런 다음 `New-AzureRoleAssignment`를 사용하여 역할 할당을 만듭니다. 예를 들면 다음과 같습니다.

-   아래 명령을 입력하면 사용자에 대해 읽기 권한자로 현재 구독 수준에 역할 할당이 생성됩니다.

    `PS C:PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`gt; New-AzureRoleAssignment -Mail \<user's email\> -RoleDefinitionName Reader</code>

-   아래 명령을 입력하면 리소스 그룹 수준에서 역할 할당이 생성됩니다.

    `PS C:PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`gt; New-AzureRoleAssignment -Mail \<user's email\> -RoleDefinitionName Contributor -ResourceGroupName group1</code>

-   아래 명령을 입력하면 리소스 수준에서 역할 할당이 생성됩니다.

    `PS C:PS C:\> $resources = Get-AzureResource PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`gt; $resources = Get-AzureResource PS C:`PS C:\> $resources = Get-AzureResource PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`gt; New-AzureRoleAssignment -Mail \<user's email\> -RoleDefinitionName Owner -Scope $resources[0].ResourceId</code>

## <span id="verify"></span></a>권한 확인

계정에 역할이 할당되었음을 확인한 후에는 다음 명령을 실행하여 이러한 역할 할당을 통해 부여되는 권한을 실제로 확인할 수 있습니다.

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

이 두 cmdlet은 읽기 권한이 있는 리소스 그룹 또는 리소스만 반환하며, 사용자가 소유한 권한도 표시합니다.

`New-AzureResourceGroup` 등의 다른 cmdlet을 실행할 때 권한이 없으면 액세스 거부 오류가 발생합니다.

## <span id="next"></span></a>다음 단계

Windows PowerShell을 사용하여 역할 기반 액세스 제어를 관리하는 방법과 관련 항목에 대해 자세히 알아보려면 다음 항목을 참조하세요.

-   [Windows Azure의 역할 기반 액세스 제어][Windows Azure의 역할 기반 액세스 제어]
-   [Azure 리소스 관리자 Cmdlet][Azure 리소스 관리자 Cmdlet](영문): AzureResourceManager 모듈에서 cmdlet을 사용하는 방법을 알아봅니다.
-   [리소스 그룹을 사용하여 Azure 리소스 관리][리소스 그룹을 사용하여 Azure 리소스 관리]: Azure 관리 포털에서 리소스 그룹을 만들고 관리하는 방법을 알아봅니다.
-   [Azure 블로그][Azure 블로그](영문): Azure의 새로운 기능에 대해 알아보십시오.
-   [Windows PowerShell 블로그][Windows PowerShell 블로그]: Windows PowerShell의 새로운 기능에 대해 알아보십시오.
-   ["Hey, Scripting Guy!" 블로그]["Hey, Scripting Guy!" 블로그](영문): Windows PowerShell 커뮤니티에서 실제 팁과 요령을 확인할 수 있습니다.
-   [XPLAT CLI를 사용하여 역할 기반 액세스 제어 구성][XPLAT CLI를 사용하여 역할 기반 액세스 제어 구성]
-   [역할 기반 액세스 제어 문제 해결][역할 기반 액세스 제어 문제 해결]

  [Windows PowerShell]: /ko-kr/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [플랫폼 간 CLI]: /ko-kr/documentation/articles/xplat-cli-rbac.md "플랫폼 간 CLI"
  [Windows Management Framework 3.0]: http://www.microsoft.com/en-us/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/en-us/download/details.aspx?id=40855
  [Azure PowerShell 설치 및 구성 방법]: http://www.windowsazure.com/ko-kr/documentation/articles/install-configure-powershell/
  [Windows PowerShell 시작]: http://technet.microsoft.com/en-us/library/hh857337.aspx
  [Azure PowerShell을 설치 및 구성하는 방법(영문)]: http://azure.microsoft.com/ko-kr/documentation/articles/install-configure-powershell/
  [리소스 관리자에서 Windows PowerShell 사용]: http://azure.microsoft.com/ko-kr/documentation/articles/powershell-azure-resource-manager/
  [구독에 연결]: #connect
  [기존 역할 할당 확인]: #check
  [역할 할당 만들기]: #create
  [권한 확인]: #verify
  [다음 단계]: #next
  [Windows Azure의 역할 기반 액세스 제어]: http://azure.microsoft.com/ko-kr/documentation/articles/role-based-access-control-configure/
  [Azure 리소스 관리자 Cmdlet]: http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409
  [리소스 그룹을 사용하여 Azure 리소스 관리]: http://azure.microsoft.com/ko-kr/documentation/articles/azure-preview-portal-using-resource-groups
  [Azure 블로그]: http://blogs.msdn.com/windowsazure
  [Windows PowerShell 블로그]: http://blogs.msdn.com/powershell
  ["Hey, Scripting Guy!" 블로그]: http://blogs.technet.com/b/heyscriptingguy/
  [XPLAT CLI를 사용하여 역할 기반 액세스 제어 구성]: http://azure.microsoft.com/ko-kr/documentation/articles/role-based-access-control-xplat-cli/
  [역할 기반 액세스 제어 문제 해결]: http://azure.microsoft.com/ko-kr/documentation/articles/role-based-access-control-troubleshooting/
