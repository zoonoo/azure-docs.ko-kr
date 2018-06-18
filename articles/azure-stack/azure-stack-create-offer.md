---
title: Azure 스택에는 제안을 만듭니다. | Microsoft Docs
description: 클라우드 관리자 Azure 스택에서 사용자가 제공을 만드는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.openlocfilehash: e5b96a9464bf4d0e3b69d2f635da32c6648ce793
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247520"
---
# <a name="create-an-offer-in-azure-stack"></a>Azure Stack에서 제품 만들기

[제공](azure-stack-key-features.md) 은 공급자를 구입 하거나 구독 하려면 사용자에 게 제공 하는 하나 이상의 계획의 그룹입니다. 이 문서를 포함 하는 제품을 만드는 방법을 보여 줍니다.는 [만든 계획을](azure-stack-create-plan.md)합니다. 이 제품은 구독자가 가상 컴퓨터를 설정 하는 기능을 제공 합니다.

1. Azure 스택 관리자 포털에 로그인 (https://adminportal.local.azurestack.external) 선택 **새로** > **테 넌 트 제공 + 계획** > **제공**합니다.

   ![제품 만들기](media/azure-stack-create-offer/image01.png)
  
2. **새 제공**를 입력 한 **표시 이름** 및 **리소스 이름**, 그 다음 **리소스 그룹**을 선택 **만들기 새** 또는 **기존 항목 사용**합니다. 표시 이름은 제안에 대 한 친숙 한 이름입니다. 이 대화명은 제공 된 사용자가 제공 하는 서비스를 구독할 때 표시 하는 방법에 대 한 유일한 정보입니다. 사용자가 추가 제안 되는 내용 이해에 도움이 되는 직관적인 이름을 사용 합니다. 관리자만 리소스 이름을 볼 수 있습니다. 관리자가 Azure 리소스 관리자 리소스로 제품 작업을 하는 데 사용하는 이름입니다.

   ![새 제안](media/azure-stack-create-offer/image01a.png)
  
3. 선택 **계획 기본** 열려는 **계획**합니다. 에 제품을 포함 한 다음 원하는 계획을 선택 **선택**합니다. 제품 선택 만들려면 **만들기**합니다.

   ![요금제 선택](media/azure-stack-create-offer/image02.png)
  
4. 이 요청을 만든 후 해당 상태를 변경할 수 있습니다. 제안 이루어져야 합니다 *공용* 사용자가 구독 하는 경우 전체 보기를 얻을 수 있습니다. 제공 될 수 있습니다.

   - **공용**: 사용자에 게 표시 합니다.
   - **개인**: 클라우드 관리자에만 표시 합니다. 계획 또는 제품을 초안을 작성 하는 동안이 설정은 유용 클라우드 관리자가 또는 [사용자에 대 한 각 구독을 만들](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)합니다.
   - **역할 해제**: 새 구독자가 접근할 수 없습니다. 클라우드 관리자 צ ְ ײ 폐기 된 이후 구독 방지 두려면 현재 구독자 영향을 주지 않습니다.

   > [!TIP]  
   > 제품에 대 한 변경 내용을 사용자에 게 즉시 표시 되지 않습니다. 변경 내용을 보려면, 사용자가 로그 아웃 및 로그인을 다시 사용자 포털에 새 제안을 보려면 해야 할 수 있습니다.

   제품의 상태를 변경 합니다.

   - **버전 1803 이상**:  
     이 요청에 대 한 개요, 선택 **내게 필요한 옵션 상태**합니다. 사용 하려는 상태 선택 (예를 들어 *공용*)를 클릭 한 **저장**합니다.
 
     ![내게 필요한 옵션 상태를 선택 합니다.](media/azure-stack-create-offer/change-state.png)

     제공 하는 서비스에 액세스 한 후 이동할 수 있습니다 **설정을 제공**합니다. 선택 **내게 필요한 옵션 상태** 상태를 변경 합니다.

   - **이전 버전 1803**:  
     선택 **모든 리소스**, 자신의 새 구독을 검색 하 고 다음에 새 제품을 선택 합니다. 선택 **상태 변경**를 선택한 후 **공용**합니다.

   > [!NOTE]
   > PowerShell를 사용 하 여 기본 제안, 계획 및 할당량을 만들 수 있습니다. 자세한 내용은 참조 [Azure 스택 서비스 관리자 추가 정보](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin)합니다.

## <a name="next-steps"></a>다음 단계

- [구독 만들기](azure-stack-subscribe-plan-provision-vm.md)
- [가상 머신 프로비전](azure-stack-provision-vm.md)
