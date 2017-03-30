---
title: "Azure DevTest Labs에서 랩에 클레임할 수 있는 VM 추가 | Microsoft Docs"
description: "Azure DevTest Labs에서 랩에 클레임할 수 있는 가상 컴퓨터를 추가하는 방법 알아보기"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: v-craic
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: fde776806c3b5eb126540841c12267ba1a6a90e4
ms.lasthandoff: 03/21/2017


---
# <a name="add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 클레임할 수 있는 VM 추가
클레임할 수 있는 VM은 [표준 VM을 추가](devtest-lab-add-vm.md)하는 것과 유사한 방식으로([사용자 지정 이미지](devtest-lab-create-template.md), [수식](devtest-lab-manage-formulas.md) 또는 [Marketplace 이미지](devtest-lab-configure-marketplace-images.md)를 *기반*으로) 랩에 추가합니다. 이 자습서에서는 Azure Portal을 사용하여 클레임할 수 있는 VM을 DevTest Labs의 랩에 추가하는 단계를 안내하고 VM을 클레임하기 위해 따라야 하는 프로세스를 보여줍니다.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 클레임할 수 있는 VM을 추가하는 단계
1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
1. 랩 목록에서 VM을 만들려는 랩을 선택합니다.  
1. 랩의 **개요** 블레이드에서 **+ 추가**를 선택합니다.  

    ![VM 단추 추가](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. **기본 선택** 블레이드에서 VM의 기본을 선택합니다.
1. **가상 컴퓨터** 블레이드의 **가상 컴퓨터 이름** 텍스트 상자에 새 가상 컴퓨터의 이름을 입력합니다.

    ![랩 VM 블레이드](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. 가상 컴퓨터에서 관리자 권한이 부여된 **사용자 이름**을 입력합니다.  
1. [비밀 저장소](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)에 저장된 암호를 사용하려는 경우 **저장된 비밀 사용**을 선택하고 비밀(암호)에 해당하는 키 값을 지정합니다. 그렇지 않으면 **값 입력** 텍스트 필드에 암호를 입력합니다.
1. **가상 컴퓨터 디스크 유형**은 랩에서 가상 컴퓨터의 저장소 디스크 유형을 허용하는지 확인합니다.
1. **가상 컴퓨터 크기** 를 선택하고 만들려는 프로세서 코어, RAM 크기 및 VM의 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 선택합니다.
1. **아티팩트** 를 선택하고 아티팩트 목록에서 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다.
    **참고:** DevTest Lab을 처음 접하거나 아티팩트를 구성 중인 경우 [VM에 기존 아티팩트 추가](./devtest-lab-add-vm-with-artifacts.md#add-an-existing-artifact-to-a-vm) 섹션을 참조한 다음 완료되면 여기로 돌아옵니다.
1. **고급 설정**을 선택하여 VM의 네트워크 옵션 및 만료 옵션을 구성합니다. **Claim options(클레임 옵션)**에서 **예**를 선택하여 컴퓨터를 클레임할 수 있도록 만듭니다.

  ![VM을 클레임할 수 있도록 선택합니다.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Azure Resource Manager 템플릿을 보거나 복사하려면 [Azure Resource Manager 템플릿 저장](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template) 섹션을 참조하여 원하는 작업을 마친 후에 여기로 다시 돌아옵니다.
1. **만들기** 를 누르고 지정된 VM을 랩에 추가합니다.
1. 랩 블레이드는 VM의 만들기 상태를 표시합니다. 처음에는 **생성 중**이 표시되고 VM이 시작하면 **실행 중**으로 표시됩니다.

> [!NOTE]
> [Azure Resource Manager 템플릿](devtest-lab-create-environment-from-arm.md)을 통해 랩 VM을 배포하는 경우 속성 섹션에서 **allowClaim** 속성을 true로 설정하면 클레임할 수 있는 VM을 만들 수 있습니다.
>
>

### <a name="using-a-claimable-vm"></a>클레임할 수 있는 VM 사용

다음 단계 중 하나를 수행하면 “클레임할 수 있는 가상 컴퓨터” 목록에서 원하는 VM을 클레임할 수 있습니다.

* 랩의 개요 블레이드 아래쪽에 있는 “클레임할 수 있는 가상 컴퓨터” 목록에서 VM 중 하나를 마우스 오른쪽 단추로 클릭하고 **Claim machine(컴퓨터 클레임)**을 선택합니다.

 ![클레임할 수 있는 특정 VM을 요청합니다.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* **개요** 블레이드 맨 위에서 **Claim any(임의 클레임)**를 선택합니다. 클레임할 수 있는 VM 목록에서 임의의 가상 컴퓨터가 할당됩니다.

 ![클레임할 수 있는 임의의 VM을 요청합니다.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


사용자가 VM을 클레임한 후에는 사용자의 "내 가상 컴퓨터" 목록으로 이동되고 더 이상 다른 사용자가 클레임할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* VM을 만든 후에는 해당 VM의 블레이드에서 **연결** 을 선택하여 VM에 연결할 수 있습니다.
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) 탐색

