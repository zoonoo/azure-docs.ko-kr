---
title: Azure Stack 정책 모듈 사용 | Microsoft Docs
description: Azure Stack 구독 처럼 동작 하기 위해 Azure 구독을 제한 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: sethm
ms.openlocfilehash: b0236a790200feec7f1d16724f351882056b2cd5
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678528"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Azure Stack 정책 모듈을 사용 하 여 Azure policy 관리

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 정책 모듈을 사용 하면 동일한 버전 관리 및 서비스 가용성 Azure Stack을 사용 하 여 Azure 구독을 구성할 수 있습니다. 이 모듈에서는 사용 합니다 [New-azurermpolicydefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) 리소스 유형 및 구독에서 사용할 수 있는 서비스를 제한 하는 Azure 정책을 만든 cmdlet. 사용 하 여 적절 한 범위 내에서 정책 할당을 만든 합니다 [New-azurermpolicyassignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) cmdlet. 정책을 구성한 후 Azure Stack에 대 한 대상으로 하는 앱을 개발 하려면 Azure 구독을 사용할 수 있습니다.

## <a name="install-the-module"></a>모듈 설치

1. 1 단계에에서 설명 된 대로 필요한 버전의 AzureRM PowerShell 모듈을 설치 [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)합니다.
2. [GitHub에서 Azure Stack 도구 다운로드](azure-stack-powershell-download.md)합니다.
3. [Azure Stack과 함께 사용 하도록 PowerShell 구성](azure-stack-powershell-configure-user.md)합니다.
4. AzureStack.Policy.psm1 모듈을 가져옵니다.

    ```PowerShell
    Import-Module .\Policy\AzureStack.Policy.psm1
    ```

## <a name="apply-policy-to-azure-subscription"></a>Azure 구독에 정책 적용

Azure 구독에 대해 기본 Azure Stack 정책을 적용 하려면 다음 명령을 사용할 수 있습니다. 이 명령을 실행 하기 전에 대체 `Azure Subscription Name` Azure 구독의 이름입니다.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>리소스 그룹에 정책 적용

더 세분화 된 정책을 적용할 수 있습니다. 예를 들어, 동일한 구독에서 실행 중인 다른 리소스가 있을 수 있습니다. Azure 리소스를 사용 하 여 Azure Stack에 대 한 앱을 테스트할 수 있습니다 특정 리소스 그룹에 정책 적용을 범위를 지정할 수 있습니다. 다음 명령을 실행 하기 전에 대체 `Azure Subscription Name` Azure 구독의 이름입니다.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>실행 중인 정책

Azure 정책을 배포한 경우 정책에 의해 금지 된 리소스를 배포 하려고 할 때 오류가 표시 됩니다.

![결과 정책 제약 조건으로 인해 리소스 배포 실패](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 템플릿 배포](azure-stack-deploy-template-powershell.md)
* [Azure CLI을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)
* [Visual Studio 사용 하 여 템플릿 배포](azure-stack-deploy-template-visual-studio.md)
