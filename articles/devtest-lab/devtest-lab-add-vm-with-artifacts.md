<properties
	pageTitle="DevTest 랩에 아티팩트를 사용하여 VM 추가 | Microsoft Azure"
	description="DevTest 랩에서 아티팩트를 사용하여 새 가상 컴퓨터를 만듭니다."
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
	ms.date="02/18/2016"
	ms.author="tarcher"/>

# Azure DevTest Lab에 아티팩트를 사용하여 VM 추가

> [AZURE.NOTE] 이 문서와 함께 제공되는 비디오를 보려면 다음 링크를 클릭하세요. [DevTest Lab에서 아티팩트를 사용하여 VM을 만드는 방법](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)

## 개요

랩에 업로드한 이미지 또는 Azure 기본 이미지로 시작하는 DevTest Lab에 가상 컴퓨터를 만듭니다.

DevTest Lab *아티팩트*를 통해 VM을 만들 때 수행하는 작업을 지정할 수 있습니다. 아티팩트 작업은 Powershell 및 Bash 명령 실행 및 소프트웨어 설치와 같은 절차를 수행할 수 있습니다. 아티팩트 *매개 변수*를 통해 특정 시나리오에 대한 아티팩트를 사용자 지정할 수 있습니다.

랩에는 공용 DevTest Lab 아티팩트 리포지토리의 아티팩트 및 사용자가 만들어서 사용자 고유 아티팩트 리포지토리에 추가한 아티팩트가 포함되어 있습니다.

이 문서에서는 아티팩트를 사용하여 랩에 VM을 만드는 방법을 보여 줍니다.

## 아티팩트를 사용하여 VM 추가

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

1. **찾아보기**를 탭한 다음 목록에서 **DevTest Lab**을 탭합니다.

1. 랩 목록에서 새 VM을 만들려는 랩을 탭합니다.

1. 랩의 블레이드에서 다음 그림에 나와 있는 대로 **+ Lab VM**(+ 랩 VM)을 탭합니다. ![DevTest Lab 홈 블레이드](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. **Lab VM**(랩 VM) 블레이드에서 **Lab VM Name**(랩 VM 이름) 텍스트 상자에 새 가상 컴퓨터의 이름을 입력합니다.

1. **기본/필요한 설정 구성**을 탭하고 VM에 대한 기본 이미지를 선택합니다.

    ![랩 VM 설정](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. 기본 이미지를 선택한 후 **사용자 이름** 및 **암호** 항목을 포함하도록 **Lab VM**(랩 VM) 블레이드가 확장됩니다. 가상 컴퓨터에서 관리자 권한이 부여된 **사용자 이름**을 입력합니다.

    ![확장된 랩 VM 블레이드](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. **암호**를 입력합니다.

1. **VM 크기**를 탭하고 만들려는 프로세서 코어, RAM 크기 및 VM의 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 선택합니다.

1. **아티팩트**를 탭하고 아티팩트 목록에서 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다. **참고:** DevTest Lab을 처음 접하거나 아티팩트를 구성 중인 경우 [아티팩트 선택 및 구성](#configuring-an-artifact) 섹션으로 건너뛴 다음 완료되면 여기로 돌아옵니다.

1. **만들기**를 탭하고 지정된 VM을 랩에 추가합니다.

1. 랩 블레이드는 VM의 만들기 상태를 표시합니다. 처음에는 **만드는 중**이 표시되고 VM이 시작하면 **실행 중**으로 표시됩니다. VM에 연결하려면 VM을 탭한 다음 VM의 블레이드에서 **연결**을 탭합니다.

## 아티팩트 선택 및 구성

VM을 만드는 동안 **Lab VM**(랩 VM) 블레이드에서 **아티팩트**를 탭하여 아티팩트를 추가할 수 있습니다. 그러면 공식 DevTest Lab 리포지토리(**공식 리포지토리**)의 VM 아티팩트 및 팀 리포지토리의 아티팩트를 추가하고 구성할 수 있는 **아티팩트 추가** 블레이드가 표시됩니다.

![아티팩트 추가 블레이드](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

**VM에 아티팩트 추가**

VM에 추가하려는 각 아티팩트에 대해 다음 단계를 수행합니다.

1. **아티팩트 추가** 블레이드에서 원하는 아티팩트를 탭하여 아티팩트의 매개 변수를 지정할 수 있는 블레이드를 봅니다.  

2. 필수 매개 변수 값 및 필요한 선택적 매개 변수를 입력합니다.

3. **추가**를 탭하여 아티팩트를 추가하고 **아티팩트 추가** 블레이드로 돌아갑니다.

**아티팩트가 실행되는 순서 변경**

VM에 아티팩트를 추가하고 구성할 때 현재 아티팩트의 수를 보여 주는 링크가 **아티팩트 추가** 블레이드의 맨 위에 표시됩니다. 기본적으로 아티팩트 작업은 VM에 추가된 순서로 실행됩니다. 아티팩트가 실행되는 순서를 변경하려면 목록에 있는 아티팩트를 끌어서 놓고 완료되면 **확인**을 탭하면 됩니다.

**선택한 아티팩트 보기/수정**

선택한 아티팩트의 매개 변수를 보거나 수정하려면 다음 단계를 수행합니다.

1. **아티팩트 추가** 블레이드의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 탭합니다.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 특정 아티팩트의 매개 변수를 보거나 편집하려면 **선택한 아티팩트** 블레이드에서 해당 아티팩트를 탭합니다.

1. 필요한 변경을 수행한 다음 **확인**을 클릭하여 **아티팩트 추가** 블레이드를 닫습니다.

1. **확인**을 탭하여 **선택한 아티팩트** 블레이드를 닫습니다.

1. **확인**을 클릭하여 **아티팩트 추가** 블레이드를 닫습니다.

## 다음 단계

- [VM에 대한 사용자 지정 아티팩트 작성](devtest-lab-artifact-author.md) 방법에 대해 알아봅니다.

<!---HONumber=AcomDC_0224_2016-->