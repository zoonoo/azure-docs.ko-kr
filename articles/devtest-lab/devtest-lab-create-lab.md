---
title: "Azure DevTest Labs에서 랩 만들기 | Microsoft Docs"
description: "가상 컴퓨터에 대한 Azure DevTest Labs에서 랩 만들기"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/30/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 265a968f902f53c7561c8c7e937f8eacfdb37167
ms.contentlocale: ko-kr
ms.lasthandoff: 08/01/2017

---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 만들기
Azure DevTest Labs에서 랩은 VM(Virtual Machines)과 같은 리소스 그룹을 포함하는 인프라로서, 이를 통해 한도 및 할당량을 지정하여 해당 리소스를 더 잘 관리할 수 있습니다. 이 문서는 Azure Portal을 사용하여 랩을 만드는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 조건
랩을 만들려면 다음이 필요합니다.

* Azure 구독. Azure 구입 옵션에 대해 알아보려면 [Azure 구입 방법](https://azure.microsoft.com/pricing/purchase-options/) 또는 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 랩을 만들려면 구독 소유자여야 합니다.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩을 만드는 단계
다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs에서 랩을 만드는 방법을 설명합니다. 

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. 왼쪽에서 주 메뉴에서 **추가 서비스**(목록 맨 위)를 선택합니다.

    ![추가 서비스 메뉴 옵션](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. 사용 가능한 서비스 목록에서 **DevTest Labs**를 선택합니다.
1. **DevTest Labs** 블레이드에서 **추가**를 선택합니다.
   
    ![랩 추가](./media/devtest-lab-create-lab/add-lab-button.png)

1. **DevTest Lab 만들기** 블레이드에서
   
    1. 새 랩에 대한 **랩 이름** 을 입력합니다.
    2. **구독** 을 선택하여 랩과 연결합니다.
    3. 랩을 저장할 **위치** 를 선택합니다.
    4. **자동 종료** 를 선택하여 사용하려는지를 지정하고 랩의 VM을 모두 자동 종료하는 매개 변수를 정의할 수 있습니다. 자동 종료 기능은 주로 VM을 자동으로 종료하려는 경우를 지정할 수 있는 비용 절감 기능입니다. [Azure DevTest Labs에서 랩에 대한 모든 정책 관리](./devtest-lab-set-lab-policy.md#set-auto-shutdown) 문서에 간략히 나와 있는 단계에 따라 랩을 만든 후 자동 종료 설정을 변경할 수 있습니다.
    5. 랩 바로 가기를 포털 대시보드에 표시하려면 **대시보드에 고정**을 선택합니다.
    6. 구성 자동화를 위한 Azure Resource Manager 템플릿을 가져오려면 **자동화 옵션**을 선택합니다. 
    7. **만들기**를 선택합니다. **만들기**를 선택하면 **DevTest Labs** 블레이드가 표시됩니다. **알림** 영역을 확인하여 랩 만들기 프로세스 상태를 모니터링할 수 있습니다. 완료되면 페이지를 새로 고쳐 랩 목록에서 새로 만들어진 랩을 볼 수 있습니다.  
    
    ![랩 블레이드 만들기](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
랩을 만들었으면 다음 단계를 고려 합니다.

* [랩에 안전하게 액세스](devtest-lab-add-devtest-user.md).
* [랩 정책 설정](devtest-lab-set-lab-policy.md).
* [랩 템플릿 만들기](devtest-lab-create-template.md).
* [VM에 대한 사용자 지정 아티팩트  만들기](devtest-lab-artifact-author.md).
* [아티팩트가 지정된 VM을 랩에 추가](https://azure.microsoft.com/resources/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab/).


