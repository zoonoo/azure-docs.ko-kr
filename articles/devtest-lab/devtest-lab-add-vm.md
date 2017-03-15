---
title: "Azure DevTest Labs에서 랩에 VM 추가 | Microsoft Docs"
description: "Azure DevTest Labs에서 랩에 가상 컴퓨터를 추가하는 방법 알아보기"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 7bac59810ee3c1da239716b58d41b97088035133
ms.openlocfilehash: e8de45686102cd6c4671484a05568a10dd22fa67
ms.lasthandoff: 02/27/2017


---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 VM 추가
[사용자 지정 이미지](devtest-lab-create-template.md), [수식](devtest-lab-manage-formulas.md) 또는 [Marketplace 이미지](devtest-lab-configure-marketplace-images.md) 등을 *기본*으로 사용하여 랩에 VM을 추가합니다. 이 자습서에서는 DevTest Labs에서 랩에 VM을 추가하기 위해 Azure Portal을 사용하는 방법을 설명합니다.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 VM을 추가하는 단계
1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
1. 랩 목록에서 VM을 만들려는 랩을 선택합니다.  
1. 랩의 **개요** 블레이드에서 **+ 추가**를 선택합니다.  

    ![VM 단추 추가](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. **기본 선택** 블레이드에서 VM의 기본을 선택합니다.
1. **가상 컴퓨터** 블레이드의 **가상 컴퓨터 이름** 텍스트 상자에 새 가상 컴퓨터의 이름을 입력합니다.
   
    ![랩 VM 블레이드](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. 가상 컴퓨터에서 관리자 권한이 부여된 **사용자 이름**을 입력합니다.  
1. [비밀 저장소](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)에 저장된 암호를 사용하려는 경우 **저장된 비밀 사용**을 선택하고 비밀(암호)에 해당하는 키 값을 지정합니다. 그렇지 않으면 **값 입력** 텍스트 필드에 암호를 입력합니다.
1. **가상 컴퓨터 디스크 유형**은 랩에서 가상 컴퓨터의 저장소 디스크 유형을 허용하는지 확인합니다.
1. **가상 컴퓨터 크기** 를 선택하고 만들려는 프로세서 코어, RAM 크기 및 VM의 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 선택합니다.
1. **아티팩트** 를 선택하고 아티팩트 목록에서 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다. 
    **참고:** DevTest Lab을 처음 접하거나 아티팩트를 구성 중인 경우 [VM에 기존 아티팩트 추가](./devtest-lab-add-vm-with-artifacts.md#add-an-existing-artifact-to-a-vm) 섹션을 참조한 다음 완료되면 여기로 돌아옵니다.
1. **고급 설정**을 선택하여 VM의 네트워크 옵션 및 만료 옵션을 구성합니다.
1. Azure Resource Manager 템플릿을 보거나 복사하려면 [Azure Resource Manager 템플릿 저장](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template) 섹션을 참조하여 원하는 작업을 마친 후에 여기로 다시 돌아옵니다.
1. **만들기** 를 누르고 지정된 VM을 랩에 추가합니다.
1. 랩 블레이드는 VM의 만들기 상태를 표시합니다. 처음에는 **생성 중**이 표시되고 VM이 시작하면 **실행 중**으로 표시됩니다.

## <a name="next-steps"></a>다음 단계
* VM을 만든 후에는 해당 VM의 블레이드에서 **연결** 을 선택하여 VM에 연결할 수 있습니다.
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) 탐색



