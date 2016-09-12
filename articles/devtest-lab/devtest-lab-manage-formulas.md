<properties
	pageTitle="VM을 만드는 DevTest Lab 수식 관리 | Microsoft Azure"
	description="DevTest Lab 수식을 만들고 업데이트하고 제거하는 방법과 이 수식을 사용하여 새 VM을 만드는 방법에 대해 알아봅니다."
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

# VM을 만드는 DevTest Lab 수식 관리

## 개요

수식(예: [사용자 지정 이미지](./devtest-lab-create-template.md) 및 [마켓플레이스 이미지](./devtest-lab-configure-marketplace-images.md))은 신속한 VM 프로비전을 위한 메커니즘을 제공합니다. DevTest Lab에 포함된 수식은 랩 VM을 만드는 데 사용되는 기본 속성 값의 목록입니다. 수식을 통해 VM을 만들 때, 기본값을 그대로 사용하거나 수정할 수 있습니다.

이 문서에서는 다음 작업을 수행하는 방법을 알아봅니다.

- [새 수식 만들기](#create-a-new-formula)
- [수식을 사용하여 새 VM 만들기](#use-a-formula-to-create-a-new-vm)
- [수식 수정](#modify-a-formula)
- [수식 삭제](#delete-a-formula)

> [AZURE.NOTE] 수식은 VM을 프로비전하는 데 사용되는 VHD의 기본 이미지를 만들 수 있다는 점에서 [사용자 지정 이미지](./devtest-lab-create-template.md)와 비슷합니다. 사용자의 환경에 적합한 것을 결정하는 데 도움이 필요하면, [DevTest Lab에서 사용자 지정 이미지와 수식 비교](./devtest-lab-comparing-vm-base-image-types.md) 문서를 참조하세요.

## 새 수식 만들기
DevTest Lab *사용자* 권한이 있으면 수식을 기반으로 사용하여 VM을 만들 수 있습니다. 수식을 만드는 방법은 두 가지입니다.

- 처음부터 - 수식의 모든 특성을 처음부터 정의하려는 경우에 사용합니다.
- 기존 랩 VM을 통해 - 기존 VM의 설정을 기반으로 수식을 만들려는 경우에 사용합니다.

### 처음부터 새 수식 만들기
다음 단계는 새 수식을 처음부터 만드는 과정을 안내합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **찾아보기**를 선택한 후 목록에서 **DevTest Lab**을 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. 선택한 랩의 **설정** 블레이드가 표시됩니다.

1. 랩 **설정** 블레이드에서 **수식**을 탭합니다.

    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **Lab formulas**(랩 수식) 블레이드에서 **+ 추가**를 탭합니다.

    ![새 수식 추가](./media/devtest-lab-manage-formulas/add-formula.png)

1. **Choose a base**(기준 선택) 블레이드에서 수식을 만들 때 사용할 사용자 지정 이미지 또는 마켓플레이스 이미지를 탭합니다.

    ![기본 목록](./media/devtest-lab-manage-formulas/base-list.png)

1. **Create formula**(수식 만들기) 블레이드에서 다음 값을 지정합니다.

	- **수식 이름** - 수식의 이름을 입력합니다. 이 값은 VM을 만들 때 기본 이미지 목록에 표시됩니다. 이름 입력 시에 유효성이 검사되고, 유효하지 않으면, 유효한 이름에 필요한 사항을 알려주는 메시지가 표시됩니다.
	- **설명** - 수식에 대한 의미 있는 설명을 입력합니다. 이 값은 VM을 만들 때 수식의 상황에 맞는 메뉴에서 볼 수 있습니다.
	- **이미지** - 이 필드는 이전 블레이드에서 선택한 기본 이미지의 이름을 표시합니다.
	- **VM 크기** - 프로세서 코어, RAM 크기 및 VM의 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 탭합니다.
	- **가상 네트워크** - 원하는 가상 네트워크를 탭하고 선택합니다.
	- **서브넷** 탭하고 원하는 서브넷을 선택합니다.
	- **공용 IP 주소** - 선택한 서브넷에 공용 IP 주소를 허용하도록 랩 정책이 설정되어 있으면 **예** 또는 **아니요**를 선택하여 IP 주소를 공용으로 할 것인지 여부를 지정합니다. 그렇지 않으면 이 옵션이 비활성화되고 **아니요**가 선택됩니다.
	- **아티팩트** - 탭하고 아티팩트 목록에서 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다. 보안 문자열 값은 수식에 저장되지 않기 때문에, 보안 문자열인 아티팩트 매개 변수는 표시되지 않습니다.

    	![수식 만들기](./media/devtest-lab-manage-formulas/create-formula.png)

1. **만들기**를 탭하여 수식을 만듭니다. 수식이 만들어지면, **Lab formulas**(랩 수식) 블레이드에 나열됩니다.

	![새로 만든 수식](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### 랩 VM을 통해 새 수식 만들기
다음 단계는 기존 VM을 기반으로 수식을 만드는 과정을 안내합니다.

> [AZURE.NOTE] 2016년 3월 30일 후에 만든 VM에 대해서만 랩 VM을 통해 새 수식 만들기가 지원됩니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **찾아보기**를 선택한 후 목록에서 **DevTest Lab**을 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. 랩 블레이드에서 **My VMs**라는 제목의 섹션을 찾아서, 새 수식을 만들 때 기준으로 사용할 VM을 클릭합니다.

	![랩 VM](./media/devtest-lab-manage-formulas/my-vms.png)

1. VM의 **설정** 블레이드에서 **Create Formula**(수식 만들기)를 탭합니다.

	![수식 만들기](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. **Create formula**(수식 만들기) 블레이드에서 새 수식의 **이름** 및 **설명**을 입력하고 **확인**을 탭합니다. 수식이 만들어지면, **Lab formulas**(랩 수식) 블레이드에 나열됩니다.

	![수식 만들기 블레이드](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## 수식 수정
수식을 수정하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **찾아보기**를 선택한 후 목록에서 **DevTest Lab**을 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. 랩 **설정** 블레이드에서 **수식**을 탭합니다.

    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **Lab formulas**(랩 수식) 블레이드에서 수정할 수식을 탭합니다.

1. **Update formula**(수식 업데이트) 블레이드에서 원하는 내용을 편집하고 **업데이트**를 탭합니다.

## 수식 삭제 
수식을 삭제하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **찾아보기**를 선택한 후 목록에서 **DevTest Lab**을 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. 랩 **설정** 블레이드에서 **수식**을 탭합니다.

    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. **Lab formulas**(랩 수식) 블레이드에서 삭제할 수식 오른쪽의 줄임표를 클릭합니다.

    ![수식 메뉴](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. 수식의 상황에 맞는 메뉴에서 **삭제**를 선택합니다.

    ![수식 상황에 맞는 메뉴](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. 삭제 확인 대화 상자에서 **예**를 탭합니다.

    ![수식 상황에 맞는 메뉴](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## 다음 단계
VM을 만들 때 사용할 수식을 만들었으면 다음 단계는 [랩에 VM을 추가](./devtest-lab-add-vm-with-artifacts.md)하는 것입니다.

<!---HONumber=AcomDC_0831_2016-->