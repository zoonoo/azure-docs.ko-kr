---
title: Azure Stack에서 제품 만들기 | Microsoft Docs
description: 클라우드 관리자는 Azure Stack에서 사용자에 게 제품을 만드는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 230415b84f06beac549693d49055d7cb998163d8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251455"
---
# <a name="create-an-offer-in-azure-stack"></a>Azure Stack에서 제안 만들기

[제공](azure-stack-key-features.md) 공급자 사용자 구입 하거나 구독할 수 있는 사용자에 게 제공 하는 하나 이상의 계획의 그룹입니다. 이 문서에서는 포함 된 제품을 만드는 방법을 설명 합니다 [사용자가 만든 계획](azure-stack-create-plan.md)합니다. 이 제품은 구독자가 가상 컴퓨터 (Vm)를 설정 하는 기능을 제공 합니다.

1. 에 로그인 합니다 [Azure Stack 관리자 포털](https://adminportal.local.azurestack.external) 선택한 **+ 리소스 만들기**, 다음 **테 넌 트 제공 + 계획**를 차례로 **제공**합니다.

   ![제품 만들기](media/azure-stack-create-offer/image01.png)
  
2. 아래 **새 제품**, 입력을 **표시 이름** 및 **리소스 이름**를 선택한 후 **리소스 그룹**을 선택 **만들기 새** 나 **기존 항목 사용**합니다. 표시 이름에는 제품에 대 한 친숙 한 이름이입니다. 친숙 한 이름은이 제품을 구독할 때 사용자에 게 표시 하는 제품에 대 한 정보만 보여 줍니다. 사용자가 제품을 사용 하 여 무엇이 이해할 수 있도록 직관적인 이름을 사용 합니다. 관리자만 리소스 이름을 볼 수 있습니다. 관리자가 Azure Resource Manager 리소스로 제품 작업을 하는 데 사용하는 이름입니다.

   ![새 제품](media/azure-stack-create-offer/image01a.png)
  
3. 선택 **기본 계획** 열려는 합니다 **계획**합니다. 제품을 포함 하 고 선택한 요금제를 선택 합니다 **선택**합니다. 제품 선택 키를 만들려면 **만들기**합니다.

   ![계획 선택](media/azure-stack-create-offer/image02.png)
  
4. 제품을 만든 후 해당 상태를 변경할 수 있습니다. 제품을 만들어야 **공용** 사용자가 구독할 때 전체 보기를 볼 수 있습니다. 제공 될 수 있습니다.

   - **공용**: 사용자에 게 표시 합니다.
   - **개인**: 클라우드 관리자에만 표시 됩니다. 계획 또는 제품 초안을 작성 하는 동안이 설정은 유용 클라우드 관리자가 하려는 경우 또는 [사용자에 대 한 각 구독을 만드는](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)합니다.
   - **서비스 해제**: 새로운 구독자에 게 닫힙니다. 클라우드 관리자에는 향후 구독을 차단할 수 있지만 현재 구독자가 영향을 받지 않은 문제를 서비스 해제할 수 있습니다.

   > [!TIP]  
   > 제품에 대 한 변경 내용을 사용자에 게 즉시 표시 되지 않습니다. 변경 내용을 보려면, 로그 아웃 했다가 다시 로그온 사용자 포털에 새 제품을 확인 하려면 사용자가 해야 할 수 있습니다.

   제품에 대 한 개요 화면에서 선택 **접근성 상태**합니다. 사용 하려는 상태 선택 (예를 들어 **공용**)를 선택한 후 **저장**합니다.

     ![상태 선택](media/azure-stack-create-offer/change-stage-1807.png)

     대신 선택 **상태 변경** 상태를 선택 합니다.

    ![내게 필요한 옵션 상태를 선택 합니다.](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > 또한 기본 제공, 계획 및 할당량을 만들려면 PowerShell을 사용할 수 있습니다. 자세한 내용은 [Azure Stack Powershell 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0)합니다.

## <a name="next-steps"></a>다음 단계

- [구독 만들기](azure-stack-subscribe-plan-provision-vm.md)
- [가상 머신 프로비전](azure-stack-provision-vm.md)
