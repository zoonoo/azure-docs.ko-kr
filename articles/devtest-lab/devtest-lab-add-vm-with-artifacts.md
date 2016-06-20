<properties
	pageTitle="랩에 아티팩트를 사용하여 VM 추가 | Microsoft Azure"
	description="DevTest Lab에서 아티팩트를 사용하여 VM을 추가하는 방법 알아보기"
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
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# 아티팩트를 사용하여 랩에 VM 추가

> [AZURE.NOTE] 이 문서와 함께 제공되는 비디오를 보려면 다음 링크를 클릭하세요. [How to create VMs with artifacts in a lab](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)(랩에서 아티팩트를 사용하여 VM을 만드는 방법)

## 개요

[사용자 지정 이미지](./devtest-lab-create-template.md), [수식](./devtest-lab-manage-formulas.md), 또는 [마켓플레이스 이미지](./devtest-lab-configure-marketplace-images.md) 등을 *기본*으로 사용하여 랩에 VM을 만듭니다.

DevTest Lab *아티팩트*를 통해 VM을 만들 때 수행하는 *작업*을 지정할 수 있습니다.

아티팩트 작업은 Windows Powershell 스크립트 실행, Bash 명령 실행 및 소프트웨어 설치와 같은 절차를 수행할 수 있습니다.

아티팩트 *매개 변수*를 통해 특정 시나리오에 대한 아티팩트를 사용자 지정할 수 있습니다.

이 문서에서는 아티팩트를 사용하여 랩에 VM을 만드는 방법을 보여 줍니다.

## 아티팩트를 사용하여 VM 추가

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. 선택 **찾아보기**, 를 선택한 다음 **DevTest 실습** 목록에서 합니다.

1. 랩 목록에서 새 VM을 만들려는 랩을 탭합니다.

1. 랩의 블레이드에서 다음 그림에 나와 있는 대로 **+ Lab VM**(+ 랩 VM)을 탭합니다. ![랩 VM 추가 단추](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. **기본 선택** 블레이드에서 VM의 기본을 선택합니다.

1. **Lab VM**(랩 VM) 블레이드에서 **Lab VM Name**(랩 VM 이름) 텍스트 상자에 새 가상 컴퓨터의 이름을 입력합니다.

	![랩 VM 블레이드](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. 가상 컴퓨터에서 관리자 권한이 부여된 **사용자 이름**을 입력합니다.

1. 선택한 기본 이미지의 OS 유형이 Linux인 경우 인증 유형을 *암호* 또는 *SSH 공개 키*로 지정합니다.

1. 지정한 인증 유형에 따라 암호 또는 SSH 공개 키를 입력합니다.

1. **VM 크기**를 탭하고 만들려는 프로세서 코어, RAM 크기 및 VM의 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 선택합니다.

1. **가상 네트워크**를 탭하고 원하는 가상 네트워크를 선택합니다.

1. **서브넷**을 누르고 서브넷을 선택합니다.

1. 선택한 서브넷에 공용 IP 주소를 허용하도록 랩 정책이 설정되어 있으면 **예** 또는 **아니요**를 선택하여 IP 주소를 공용으로 할 것인지 여부를 지정합니다. 그렇지 않으면 이 옵션이 비활성화되고 **아니요**가 선택됩니다.

1. **아티팩트**를 탭하고 아티팩트 목록에서 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다. **참고:** DevTest Lab을 처음 접하거나 아티팩트를 구성 중인 경우 [VM에 기존 아티팩트 추가](#add-an-existing-artifact-to-a-vm) 섹션으로 건너뛴 다음 완료되면 여기로 돌아옵니다.

1. ARM 템플릿을 보거나 복사하려면 [ARM 템플릿 저장](#save-arm-template) 섹션으로 건너뛰어 원하는 작업을 마친 후에 여기로 다시 돌아옵니다.

1. **만들기**를 탭하고 지정된 VM을 랩에 추가합니다.

1. 랩 블레이드는 VM의 만들기 상태를 표시합니다. 처음에는 **만드는 중**이 표시되고 VM이 시작하면 **실행 중**으로 표시됩니다.

1. [다음 단계](#next-steps) 섹션으로 이동합니다.

## VM에 기존 아티팩트 추가

VM을 만드는 동안 기존 아티팩트를 추가할 수 있습니다. 각 랩에는 공용 DevTest Lab 아티팩트 리포지토리의 아티팩트 및 사용자가 만들어서 사용자 고유 아티팩트 리포지토리에 추가한 아티팩트가 포함되어 있습니다. 아티팩트를 만드는 방법을 알아보려면 [DevTest Labs와 함께 사용할 사용자 고유의 아티팩트를 저작하는 방법 알아보기](devtest-lab-artifact-author.md) 문서를 참조하세요.

1. 에 **랩 VM** 블레이드, **아티팩트**합니다. 

1. **아티팩트 추가** 블레이드에서 원하는 아티팩트를 누릅니다.

![아티팩트 추가 블레이드](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. 필수 매개 변수 값 및 필요한 선택적 매개 변수를 입력합니다.  

1. **추가**를 탭하여 아티팩트를 추가하고 **아티팩트 추가** 블레이드로 돌아갑니다.

1. VM에 필요한 만큼 계속해서 아티팩트를 추가합니다.

1. 아티팩트를 추가한 후에는 [아티팩트가 실행되는 순서를 변경](#change-the-order-in-which-artifacts-are-run)할 수 있습니다. 뒤로 돌아가서 [아티팩트를 확인 또는 수정](#view-or-modify-an-artifact)할 수도 있습니다.

## 아티팩트가 실행되는 순서 변경

기본적으로 아티팩트 작업은 VM에 추가된 순서로 실행됩니다. 다음 단계에서는 아티팩트가 실행되는 순서를 변경하는 방법을 보여 줍니다.

1. **아티팩트 추가** 블레이드의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 누릅니다.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 목록의 아티팩트를 원하는 순서대로 끌어 놓습니다. **참고:** 아티팩트를 끌어 놓는 데 문제가 있으면 아티팩트의 왼쪽에서 끌어 놓으세요.

1. 선택 **확인** 완료 합니다.

## 아티팩트 확인 또는 수정

다음 단계에서는 아티팩트의 매개 변수를 확인 또는 수정하는 방법을 보여 줍니다.

1. **아티팩트 추가** 블레이드의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 누릅니다.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. **선택한 아티팩트** 블레이드에서 확인 또는 편집하려는 아티팩트를 누릅니다.

1. **아티팩트 추가** 블레이드에서 필요한 부분을 변경하고, **확인**을 눌러 **아티팩트 추가** 블레이드를 닫습니다.

1. **확인**을 탭하여 **선택한 아티팩트** 블레이드를 닫습니다.

## ARM 템플릿 저장

ARM 템플릿은 반복 가능한 배포를 정의하는 선언적 방법을 제공합니다. 다음 단계는 생성 중인 VM에 대한 ARM 템플릿을 저장하는 방법을 설명합니다. 저장한 후에는, ARM 템플릿을 사용하여 [Azure PowerShell로 새 VM을 배포](../resource-group-overview.md#template-deployment)할 수 있습니다.

1. **Lab VM**(랩 VM) 블레이드에서 **View ARM Template**(ARM 템플릿 보기)를 탭합니다.

1. **View Azure Resource Manager Template blade**(Azure Resource Manager 템플릿 블레이드 보기)에서 템플릿 텍스트를 모두 선택합니다.

1. 선택한 텍스트를 클립보드에 복사합니다.

1. **확인**을 클릭하여 **View Azure Resource Manager Template blade**(Azure Resource Manager 템플릿 블레이드 보기)를 닫습니다.

1. 텍스트 편집기를 엽니다.

1. 클립보드의 템플릿 텍스트를 붙여넣습니다.

1. 나중에 사용할 수 있도록 파일을 저장합니다.

## 다음 단계

- VM이 생성되면 해당 VM의 블레이드에서 **연결**을 눌러 VM에 연결할 수 있습니다.
- [DevTest Lab VM에 대한 사용자 지정 아티팩트를 만드는](devtest-lab-artifact-author.md) 방법을 알아봅니다.
- [DevTest Lab ARM QuickStart 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) 탐색

<!---HONumber=AcomDC_0608_2016-->