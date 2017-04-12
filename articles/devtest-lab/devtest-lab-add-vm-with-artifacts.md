---
title: "Azure DevTest Labs에서 VM 아티팩트 관리 | Microsoft Docs"
description: "Azure DevTest Labs에서 VM 아티팩트를 관리하는 방법 알아보기"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 576509ce-6a33-4c26-87c7-de8b40271efa
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d298c210fa46cc4473fc7e8809e55bfc102481d
ms.openlocfilehash: c970bca8f6ab9e97741370f74efc5ee66e592468
ms.lasthandoff: 02/27/2017


---
# <a name="manage-vm-artifacts-in-azure-devtest-labs"></a>Azure DevTest Labs에서 VM 아티팩트 관리
Azure DevTest Labs *아티팩트*를 통해 VM을 프로비전할 때 수행하는 *작업*을 지정할 수 있습니다. 

아티팩트 작업은 Windows Powershell 스크립트 실행, Bash 명령 실행 및 소프트웨어 설치와 같은 절차를 수행할 수 있습니다. 

아티팩트 *매개 변수* 를 통해 특정 시나리오에 대한 아티팩트를 사용자 지정할 수 있습니다.

이 문서에서는 랩에서 VM의 아티팩트를 만드는 방법을 보여 줍니다.

## <a name="add-an-existing-artifact-to-a-vm"></a>VM에 기존 아티팩트 추가
VM을 만드는 동안 기존 아티팩트를 추가할 수 있습니다. 각 랩에는 공용 DevTest Lab 아티팩트 리포지토리의 아티팩트 및 사용자가 만들어서 사용자 고유 아티팩트 리포지토리에 추가한 아티팩트가 포함되어 있습니다.
아티팩트를 만드는 방법을 알아보려면 [DevTest Labs와 함께 사용할 사용자 고유의 아티팩트를 저작하는 방법 알아보기](devtest-lab-artifact-author.md)문서를 참조하세요.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
1. 랩 목록에서 사용하려는 VM을 포함하는 랩을 선택합니다.  
1. **내 가상 컴퓨터**를 선택합니다.
1. 원하는 VM을 선택합니다.
1. **아티팩트**를 선택합니다. 
1. **아티팩트 적용**을 선택합니다.
1. **아티팩트 적용** 블레이드에서 VM에 추가하려는 아티팩트를 선택합니다.
1. **아티팩트 추가** 블레이드에서 필수 매개 변수 값 및 필요한 선택적 매개 변수를 입력합니다.  
1. **추가**를 선택하여 아티팩트를 추가하고 **아티팩트 적용** 블레이드로 돌아갑니다.
1. VM에 필요한 만큼 계속해서 아티팩트를 추가합니다.
1. 아티팩트를 추가한 후에는 [아티팩트가 실행되는 순서를 변경](#change-the-order-in-which-artifacts-are-run)할 수 있습니다. 뒤로 돌아가서 [아티팩트를 확인 또는 수정](#view-or-modify-an-artifact)할 수도 있습니다.
1. 아티팩트 추가를 마친 경우 **적용**을 선택합니다.

## <a name="change-the-order-in-which-artifacts-are-run"></a>아티팩트가 실행되는 순서 변경
기본적으로 아티팩트 작업은 VM에 추가된 순서로 실행됩니다. 다음 단계에서는 아티팩트가 실행되는 순서를 변경하는 방법을 보여 줍니다.

1. **아티팩트 적용** 블레이드의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 선택합니다.
   
    ![VM에 추가된 아티팩트의 수](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **선택한 아티팩트** 블레이드에서 아티팩트를 원하는 순서로 끌어다 놓습니다. **참고:** 아티팩트를 끌어 놓는 데 문제가 있으면 아티팩트의 왼쪽에서 끌어 놓으세요. 
1. 완료되면 **확인** 을 선택합니다.  

## <a name="view-or-modify-an-artifact"></a>아티팩트를 확인 또는 수정
다음 단계에서는 아티팩트의 매개 변수를 확인 또는 수정하는 방법을 보여 줍니다.

1. **아티팩트 적용** 블레이드의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 선택합니다.
   
    ![VM에 추가된 아티팩트의 수](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **선택한 아티팩트** 블레이드에서 확인 또는 편집하려는 아티팩트를 선택합니다.  
1. **아티팩트 추가** 블레이드에서 필요한 부분을 변경하고 **확인**을 선택하여 **아티팩트 추가** 블레이드를 닫습니다.
1. **확인**을 선택하여 **선택한 아티팩트** 블레이드를 닫습니다.

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 저장
Azure Resource Manager 템플릿을 사용하면 반복 가능한 배포를 선언적으로 정의할 수 있습니다. 다음 단계는 생성 중인 VM에 대한 Azure Resource Manager 템플릿을 저장하는 방법을 설명합니다.
저장한 Azure Resource Manager 템플릿은 [Azure PowerShell로 새 VM을 배포](../azure-resource-manager/resource-group-overview.md#template-deployment)하는 데 사용할 수 있습니다.

1. **가상 컴퓨터** 블레이드에서 **ARM 템플릿 보기**를 선택합니다.
2. **Azure Resource Manager 템플릿 보기** 블레이드에서 템플릿 텍스트를 선택합니다.
3. 선택한 텍스트를 클립보드에 복사합니다.
4. **확인**을 선택하여 **Azure Resource Manager 템플릿 블레이드 보기**를 닫습니다.
5. 텍스트 편집기를 엽니다.
6. 클립보드의 템플릿 텍스트를 붙여넣습니다.
7. 나중에 사용할 수 있도록 파일을 저장합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
* [DevTest Labs VM용 사용자 지정 아티팩트 작성](devtest-lab-artifact-author.md)방법을 알아봅니다.

