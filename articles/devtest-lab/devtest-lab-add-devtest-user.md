    <properties
	pageTitle="Add owners and users to a DevTest Lab | Microsoft Azure"
	description="Securely add a user who is not in your subscription to your Azure DevTest Lab."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# DevTest Lab에 소유자 및 사용자 추가

## 개요

DevTest Lab에 대한 액세스는 Azure RBAC(역할 기반 액세스 제어)에서 제어합니다. 자세한 내용을 보려면 Azure Preview 포털에서 [RBAC(역할 기반 액세스 제어)](https://azure.microsoft.com/searchresults?query=Role%20Based%20Access%20Control%20%28RBAC%29)를 검색합니다.

다음 두 역할을 통해 DevTest Lab에 액세스를 허용합니다.

 - **소유자**: Azure 구독 수준에서 **소유자** 역할에 할당된 사용자는 관리 및 모니터링 기능을 포함하여 랩에 대한 완전한 액세스 권한을 가집니다.

     >[AZURE.NOTE] 구독 수준 이외의 RBAC 수준에서 할당된 **소유자** 역할은 DevTest Lab에서 지원되지 않습니다. 사용자를 DevTest Lab의 **소유자** 역할에 할당하는 것은 지원되지 않습니다.

 -  **DevTest Lab 사용자**: **DevTest Lab 사용자** 역할에 할당된 사용자는 지정된 랩에서 VM을 만들고 업데이트하고 삭제할 수 있습니다. 사용자는 *내부*(구독에 대한 Azure Active Directory 구성원) 또는 *외부*(파트너 조직의 구성원과 같이 Azure AD의 구성원이 아닌 사용자)일 수 있습니다.
	-  **DevTest Lab 사용자** 역할은 랩의 **사용자 추가** 타일을 통해 할당해야 합니다.
	-  **DevTest Lab 사용자** 역할의 사용자는 할당된 랩 내에서만 이러한 작업을 수행할 수 있습니다. 예를 들어 **DevTest Lab 사용자**는 구독의 가상 컴퓨터 서비스를 사용하여 가상 컴퓨터를 만들 수 없습니다. 가상 컴퓨터를 만드는 것은 DevTest Lab 계정에서만 허용됩니다.
	- *외부* 사용자는 Microsoft 계정 도메인(예: @hotmail.com, @live.com, @msn.com, @passport.com, @outlook.com 또는 특정 국가에서 사용하는 변형) 중 하나에 계정이 있어야 합니다.

## 랩에 소유자 추가

DevTest Lab은 랩이 포함된 Azure 구독의 소유자를 해당 랩의 소유자로 간주합니다. Azure Preview 포털에서 랩의 블레이드를 통해 DevTest Lab에 추가 소유자를 추가할 수 있지만 현재는 이 기능이 지원되지 않습니다.

이미 랩을 만들었거나 새 랩을 만드는 Azure 구독에 소유자를 추가하려면 다음 단계를 수행합니다.

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 탐색에서 **구독**을 탭합니다.

	![구독 링크](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. 랩을 포함시킬 구독을 탭합니다.

1. **액세스** 아이콘을 탭합니다.

	![사용자 액세스](./media/devtest-lab-add-devtest-user/access-users.png)

1. **사용자** 블레이드에서 **추가**를 탭합니다.

	![사용자 추가](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. **역할 선택** 블레이드에서 **소유자**를 탭합니다.

1. 소유자로 추가하려는 사용자의 메일을 **사용자** 텍스트 상자에 입력합니다. 사용자를 찾을 수 없는 경우 문제를 설명하는 오류 메시지가 표시됩니다. 사용자를 찾은 경우 해당 사용자가 **사용자** 텍스트 상자 아래에 나열됩니다.

1. 찾은 사용자 이름을 탭합니다.

1. **선택**을 탭합니다.

1. **확인**을 탭하여 **액세스 추가** 블레이드를 닫습니다.

1. **사용자** 블레이드로 돌아가면 사용자가 소유자로 추가된 것을 확인할 수 있습니다. 이 사람은 이제 이 구독 아래에 만든 랩의 소유자이므로 소유자 작업을 수행할 수 있습니다.

## 랩에 DevTest Lab 사용자 추가

랩에 DevTest Lab 사용자를 추가하려면 다음 단계를 수행합니다.

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

1. **찾아보기**를 탭합니다.

1. **DevTest Lab**을 탭합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. **액세스** 아이콘을 탭합니다.

	![사용자 액세스](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. **사용자** 블레이드에서 **추가**를 탭합니다.

	![사용자 추가](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. **역할 선택** 블레이드에서 **DevTest Lab 사용자**를 탭합니다.

1. **사용자 추가** 블레이드에서

	1. **사용자 추가** 블레이드에 기본 제공 사용자 목록이 표시됩니다. 원하는 사용자가 이미 목록에 있는 경우 사용자 행을 탭하기만 하면 해당 사용자를 선택할 수 있습니다. 확인 표시가 사용자의 왼쪽에 나타나 사용자가 선택되었다는 것을 나타냅니다. 여러 사용자를 선택하려면 **&lt;Ctrl>** 키를 누른 채 각 사용자를 클릭합니다. 사용자를 선택 해제하려면 **&lt;Ctrl>** 키를 누른 채 사용자를 클릭합니다. 블레이드 맨 아래에 있는 카운터는 선택한 사용자의 수를 나타냅니다.

	1. 원하는 사용자가 목록에 없는 경우 **사용자** 텍스트 상자에 유효한 Microsoft 메일 계정을 입력합니다. 메일 주소가 유효한 경우 사용자가 **사용자** 텍스트 상자 아래에 표시됩니다. 탭하면 선택할 수 있습니다.

	1. 랩에 추가하려는 사용자를 선택했으면 **선택**을 탭합니다.

	1. **확인**을 탭하여 **액세스 추가** 블레이드를 닫습니다.

	1. **사용자** 블레이드에 추가된 역할 및 사용자가 표시됩니다.

<!---HONumber=AcomDC_0128_2016-->