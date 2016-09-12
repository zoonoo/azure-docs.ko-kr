<properties
	pageTitle="랩에 소유자 및 사용자 추가 | Microsoft Azure"
	description="구독에 없는 사용자를 Azure DevTest Labs에 안전하게 추가하기"
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
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# 랩에 소유자 및 사용자 추가

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

## 개요
DevTest Lab 액세스는 Azure 역할 기반 액세스 제어(RBAC)를 통해 제어됩니다. 자세한 내용을 보려면 [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 [RBAC(역할 기반 액세스 제어)](https://azure.microsoft.com/search/?q=role%20based%20access%20control)를 검색하세요.


다음 두 역할을 통해 랩에 대한 액세스를 허용합니다.

- **소유자**: 랩 수준에서 **소유자** 역할에 할당된 사용자는 관리 및 모니터링 기능을 포함하여 랩에 대한 완전한 액세스 권한을 가집니다. 랩 수준에서 할당된 **소유자** 역할은 사용자에게 랩 범위 밖의 구독에 있는 리소스에 액세스할 수 있는 권한을 부여하지 않습니다. Azure 구독 수준에서 **소유자** 역할이 할당된 사용자에게는 랩 및 VM을 비롯한 해당 구독에서 만들어진 모든 랩에 대한 **소유자** 권한이 자동으로 부여됩니다.

-  **DevTest 랩 사용자**: **DevTest 랩 사용자** 역할에 할당된 사용자는 VM, 정책 또는 가상 네트워크와 같은 모든 랩 리소스를 볼 수 있을 뿐만 아니라 지정된 랩에서 VM을 만들 수 있습니다. 사용자는 *내부*(구독에 대한 Azure Active Directory 구성원) 또는 *외부*(파트너 조직의 구성원과 같이 Azure AD의 구성원이 아닌 사용자)일 수 있습니다.
	-  **DevTest Lab 사용자** 역할은 랩의 **사용자 추가** 타일을 통해 할당해야 합니다.
	-  **DevTest Lab 사용자** 역할의 사용자는 할당된 랩 내에서만 이러한 작업을 수행할 수 있습니다. 예를 들어 **DevTest Lab 사용자**는 구독의 가상 컴퓨터 서비스를 사용하여 가상 컴퓨터를 만들 수 없습니다. 가상 컴퓨터를 만드는 것은 DevTest Lab 계정에서만 허용됩니다.
	- *외부* 사용자는 MSA(Microsoft 계정)을 사용하는 사용자입니다.
 
VM이 만들어지면 이를 만든 사용자는 생성된 VM의 **소유자** 역할에 자동으로 할당되어 랩에서 제공되는 모든 작업을 수행할 수 있도록 합니다.

## 랩에 소유자 추가

권한이 Azure의 부모 범위에서 자식 범위로 전파되는 방식으로 인해 랩이 포함된 Azure 구독의 소유자는 자동으로 해당 랩의 소유자가 됩니다. 뿐만 아니라 랩 사용자가 만든 모든 VM 및 다른 리소스와 DevTest 랩 서비스의 소유자가 됩니다. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 랩의 블레이드를 통해 랩에 추가 소유자를 추가할 수 있지만 DevTest Lab 서비스가 구독에 만든 일부 리소스에 대한 모든 권한을 갖지 않기 때문에 해당 관리 범주는 구독 소유자보다 더 세부적입니다.

이미 랩을 만들었거나 새 랩을 만드는 Azure 구독에 소유자를 추가하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. 왼쪽 탐색 창에서 선택 **구독**합니다.

	![구독 링크](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. 랩을 포함시킬 구독을 선택합니다.

1. **액세스** 아이콘을 탭합니다.

	![사용자 액세스](./media/devtest-lab-add-devtest-user/access-users.png)

1. **사용자** 블레이드에서 **추가**를 선택합니다.

	![사용자 추가](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. **역할 선택** 블레이드에서 **소유자**를 탭합니다.

1. 소유자로 추가하려는 사용자의 메일을 **사용자** 텍스트 상자에 입력합니다. 사용자를 찾을 수 없는 경우 문제를 설명하는 오류 메시지가 표시됩니다. 사용자를 찾은 경우 해당 사용자가 **사용자** 텍스트 상자 아래에 나열됩니다.

1. 찾은 사용자 이름을 탭합니다.

1. **선택**을 선택합니다.

1. **확인**을 탭하여 **액세스 추가** 블레이드를 닫습니다.

1. **사용자** 블레이드로 돌아가면 사용자가 소유자로 추가된 것을 확인할 수 있습니다. 이 사람은 이제 이 구독 아래에 만든 랩의 소유자이므로 소유자 작업을 수행할 수 있습니다.

## 랩에 DevTest Lab 사용자 추가

랩에 DevTest Lab 사용자를 추가하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **모두 찾아보기**를 선택합니다.

1. 선택 **DevTest 실습**합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. **액세스** 아이콘을 탭합니다.

	![사용자 액세스](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. **사용자** 블레이드에서 **추가**를 선택합니다.

	![사용자 추가](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. **역할 선택** 블레이드에서 **DevTest Lab 사용자**를 탭합니다.

1. **사용자 추가** 블레이드에서

	1. **사용자 추가** 블레이드에 기본 제공 사용자 목록이 표시됩니다. 원하는 사용자가 이미 목록에 있는 경우 사용자 행을 탭하기만 하면 해당 사용자를 선택할 수 있습니다. 확인 표시가 사용자의 왼쪽에 나타나 사용자가 선택되었다는 것을 나타냅니다. 여러 사용자를 선택하려면 **&lt;Ctrl>** 키를 누른 채 각 사용자를 클릭합니다. 사용자를 선택 해제하려면 **&lt;Ctrl>** 키를 누른 채 사용자를 클릭합니다. 블레이드 맨 아래에 있는 카운터는 선택한 사용자의 수를 나타냅니다.

	1. 원하는 사용자가 목록에 없는 경우 **사용자** 텍스트 상자에 유효한 Microsoft 메일 계정을 입력합니다. 메일 주소가 유효한 경우 사용자가 **사용자** 텍스트 상자 아래에 표시됩니다.

	1. 랩에 추가하려는 사용자를 선택했으면 **선택**을 탭합니다.

	1. **확인**을 탭하여 **액세스 추가** 블레이드를 닫습니다.

1. **사용자** 블레이드에 추가된 역할 및 사용자가 표시됩니다.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

<!---HONumber=AcomDC_0831_2016-->