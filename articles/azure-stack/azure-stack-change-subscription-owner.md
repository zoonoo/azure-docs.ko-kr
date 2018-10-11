---
title: Azure Stack 사용자 구독의 소유자를 업데이트 합니다. | Microsoft Docs
description: Azure STack 사용자 구독의 청구 소유자를 변경 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: a784f169bfdf23255b27d50f0e135384db6b2b88
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077633"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Azure Stack 사용자 구독에 대 한 소유자를 변경 합니다.

Azure Stack 운영자 PowerShell을 사용 하 여 사용자 구독의 청구 소유자를 변경할 수 있습니다. 소유자를 변경할 이유 중 하나는 사용자가 조직을 떠나는 바꾸는 것입니다.   

두 가지가 *소유자* 구독에 할당 되는:

- **청구 소유자** – 기본적으로 청구 소유자는 제품에서 구독을 가져와서 해당 구독에 대 한 청구 관계를 소유 하 고 다음 사용자 계정입니다. 이 계정은 구독 관리자 이기도합니다.  하나의 사용자 계정 구독에서이 지정을 가질 수 있습니다. 청구 소유자는 조직 또는 팀 리더는 경우가 많습니다. 

  PowerShell cmdlet을 사용 하면 합니다 **집합 AzsUserSubscription** 청구 소유자를 변경할 수 있습니다.  

- **RBAC 역할을 통해 추가한 소유자** – 추가 사용자에 게 부여할 수 있습니다 사용 하 여 소유자 역할을 [역할 기반 Access Control](azure-stack-manage-permissions.md) (RBAC) 시스템입니다.  청구 소유자를 보완 하는 소유자와 임의 개수의 추가 사용자 계정 추가할 수 있습니다. 추가 소유자 구독의 관리자도 및 청구 소유자를 삭제 하는 권한 제외 하 고 구독에 대 한 모든 권한이 있어야 합니다. 

  PowerShell을 사용 하 여 추가 소유자 관리, 참조 [역할 기반 액세스 제어를 관리 하려면 Azure PowerShell]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)합니다.


## <a name="change-the-billing-owner"></a>청구 소유자를 변경 합니다.
사용자 구독의 청구 소유자를 변경 하려면 다음 스크립트를 실행 합니다.  스크립트를 실행 하는 데 사용 하는 컴퓨터가 Azure Stack에 연결 하 고 Azure Stack PowerShell 모듈 1.3.0 실행 이상. 자세한 내용은 [Azure Stack PowerShell 설치](azure-stack-powershell-install.md)합니다. 

> [!Note]  
>  다중 테 넌 트 Azure stack에서 새 소유자 기존 소유자와 같은 디렉터리에 있어야 합니다. 다른 디렉터리에 있는 사용자는 구독의 소유권을 제공할 수 있습니다, 전에 먼저 [디렉터리로 해당 사용자를 게스트로 초대](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)합니다. 


실행 하기 전에 스크립트에서 다음 값을 바꿉니다. 
 
- **$ArmEndpoint** – 사용자 환경에 대 한 Resource Manager 끝점을 지정 합니다.  
- **$TenantId** -테 넌 트 ID를 지정 합니다. 
- **$SubscriptionId** – 구독 ID를 지정 합니다.
- **$OwnerUpn** -으로 계정을 지정 *user@example.com* 새 청구 소유자로 추가 합니다.  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>다음 단계
[역할 기반 Access Control 관리](azure-stack-manage-permissions.md)
