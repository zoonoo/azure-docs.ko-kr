<properties 
	pageTitle="Azure 플랫폼 간 명령줄 인터페이스를 사용하여 역할 기반 액세스 제어 관리" 
	description="플랫폼 간 명령줄 인터페이스를 사용하여 역할 기반 액세스 제어 관리" 
	services="" 
	documentationCenter="" 
	authors="guangyang" 
	manager="terrylan" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/26/2014" 
	ms.author="guayan"/>

# 플랫폼 간 명령줄 인터페이스를 사용하여 역할 기반 액세스 제어 관리 #

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/ko-kr/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI">플랫폼 간 CLI</a></div>

Azure 미리 보기 포털 및 Azure 리소스 관리자 API의 RBAC(역할 기반 액세스 제어)를 사용하면 세밀한 수준에서 구독에 대한 액세스를 관리할 수 있습니다. 이 기능을 통해 특정 범위에서 Active Directory 사용자, 그룹 또는 서비스 사용자에게 일부 역할을 할당하여 액세스 권한을 부여할 수 있습니다.

이 자습서에서는 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)를 사용하여 RBAC를 관리하는 방법에 대해 알아보고 역할 할당을 만들고 확인하는 프로세스를 단계별로 살펴봅니다.

**예상 완료 시간:** 15분

## 필수 구성 요소 ##

xplat-cli를 사용하여 RBAC를 관리하려면 다음 항목이 필요합니다.

- Azure 플랫폼 간 명령줄 인터페이스 버전 0.8.8 이상. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성](http://azure.microsoft.com/documentation/articles/xplat-cli/)을 참조하세요.
- Azure 플랫폼 간 명령줄 인터페이스에서 Azure 리소스 관리자를 설치하고 사용하는 방법을 익히려면 다음 자습서도 확인하세요. [리소스 관리자에서 Azure 플랫폼 간 명령줄 인터페이스 사용](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)

## 자습서 내용 ##

* [구독에 연결](#connect)
* [기존 역할 할당 확인](#check)
* [역할 할당 만들기](#create)
* [권한 확인](#verify)
* [다음 단계](#next)

## <a id="connect"></a>구독에 연결 ##

RBAC는 Azure 리소스 관리자에서만 작동하므로 먼저 Azure 리소스 관리자 모드로 전환해야 합니다. 이렇게 하려면 다음을 입력합니다.

    azure config mode arm

자세한 내용은 [리소스 관리자에서 Azure 플랫폼 간 명령줄 인터페이스 사용](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)을 참조하세요.

Azure 구독에 연결하려면 다음을 입력합니다.

    azure login -u <사용자 이름>

명령줄 프롬프트에 Azure 계정 암호를 입력합니다. 조직 ID만 지원됩니다. xplat-cli는 이 계정에 포함된 모든 구독을 가져오며 첫 번째 구독을 기본값으로 사용하도록 자체적으로 구성합니다. RBAC를 사용하는 경우에는 자신이 공동 관리자이거나 역할을 할당받아 특정 권한을 가진 구독만 가져올 수 있습니다. 

구독이 여러 개인 경우 다른 구독으로 전환하려면 다음을 입력합니다.

    # 계정 아래에 구독이 표시됩니다.
    azure account list
    # 구독 이름을 사용하여 작업할 구독을 선택합니다.
    azure account set <구독 이름>

자세한 내용은 [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성](http://azure.microsoft.com/documentation/articles/xplat-cli/)을 참조하세요.

## <a id="check"></a>기존 역할 할당 확인 ##

구독에 이미 포함되어 있는 역할 할당을 확인해 보겠습니다. 다음을 입력합니다.

    azure role assignment list

그러면 구독의 모든 역할 할당이 반환됩니다. 여기서 유의해야 할 점은 다음의 두 가지입니다.

1. 구독 수준에서 읽기 권한이 있어야 합니다.
2. 구독에 역할 할당이 많은 경우 모든 할당을 가져오려면 시간이 오래 걸릴 수 있습니다.

특정 범위에서 특정 사용자에 대한 특정 역할 정의의 기존 역할 할당을 확인할 수도 있습니다. 다음을 입력합니다.

    azure role assignment list -g group1 --mail <사용자 메일> -o Owner

그러면 AD 테넌트에서 리소스 그룹 "group1"에 대해 "Owner" 역할이 할당된 특정 사용자에 대한 모든 역할 할당이 반환됩니다. 역할 할당은 다음의 두 위치에서 수행될 수 있습니다.

1. 리소스 그룹의 사용자에 대한 "Owner" 역할 할당
2. 리소스 그룹 부모 항목(여기서는 구독)의 사용자에 대한 "Owner" 역할 할당. 특정 수준에서 권한이 있으면 해당 수준의 모든 자식 항목에도 같은 권한이 있기 때문입니다.

이 cmdlet의 모든 매개 변수는 선택 사항입니다. 매개 변수를 적절하게 조합하여 각기 다른 필터로 역할 할당을 확인할 수 있습니다.

## <a id="create"></a>역할 할당 만들기 ##

역할 할당을 만들려면 다음 사항을 고려해야 합니다.

- 역할을 할당할 대상: 다음 Azure Active Directory cmdlet을 사용하여 AD 테넌트에 포함된 사용자, 그룹 및 서비스 사용자를 확인할 수 있습니다.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

- 할당할 역할: 다음 cmdlet을 사용하여 지원되는 역할 정의를 확인할 수 있습니다.

    `azure role list`

- 할당할 범위: 범위에는 세 가지 수준이 있습니다.

    - 현재 구독
    - 리소스 그룹. 리소스 그룹 목록을 가져오려면 `azure group list`를 입력합니다.
    - 리소스. 리소스 목록을 가져오려면 `azure resource list`를 입력합니다.

그런 후 `azure role assignment create`를 사용하여 역할 할당을 만듭니다. 예를 들면 다음과 같습니다.

 - 아래 명령을 입력하면 사용자에 대해 읽기 권한자로 현재 구독 수준에 역할 할당이 생성됩니다.

    `azure role assignment create --mail <user's email> -o Reader`

- 아래 명령을 입력하면 리소스 그룹 수준에서 역할 할당이 생성됩니다.

    `PS C:> azure role assignment create --mail <사용자 메일> -o Contributor -g group1`

- 아래 명령을 입력하면 리소스 수준에서 역할 할당이 생성됩니다.

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>권한 확인 ##

계정에 역할이 할당되었음을 확인한 후에는 다음 명령을 실행하여 이러한 역할 할당을 통해 부여되는 권한을 실제로 확인할 수 있습니다.

    PS C:> azure group list
    PS C:> azure resource list

이 두 cmdlet은 읽기 권한이 있는 리소스 그룹 또는 리소스만 반환하며, 사용자가 소유한 권한도 표시합니다.

`azure group create` 등의 다른 cmdlet을 실행할 때 권한이 없으면 액세스 거부 오류가 발생합니다.

## <a id="next"></a>다음 단계 ##

xplat-cli를 사용하여 역할 기반 액세스 제어를 관리하는 방법과 관련 항목에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성](http://azure.microsoft.com/documentation/articles/xplat-cli/)
- [리소스 관리자에서 Azure 플랫폼 간 명령줄 인터페이스 사용](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)
- [리소스 그룹을 사용하여 Azure 리소스 관리](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups): Azure 관리 포털에서 리소스 그룹을 만들고 관리하는 방법을 알아봅니다.
- [Azure 블로그](http://blogs.msdn.com/windowsazure)(영문): Azure의 새로운 기능에 대해 알아봅니다.

<!--HONumber=46--> 
