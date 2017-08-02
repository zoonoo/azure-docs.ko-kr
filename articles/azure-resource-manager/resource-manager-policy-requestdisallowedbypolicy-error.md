---
title: "Azure 리소스 정책의 RequestDisallowedByPolicy 오류 | Microsoft Docs"
description: "RequestDisallowedByPolicy 오류의 원인을 설명합니다."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 182a27e444c2f5db66d518a1a0c608d3e319d553
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 리소스 정책의 RequestDisallowedByPolicy 오류

이 문서에서는 RequestDisallowedByPolicy 오류의 원인을 설명하고 해결 방법을 제공합니다.

## <a name="symptom"></a>증상

배포 동안 작업을 수행하려고 하면 작업이 수행되지 않도록 하는 **RequestDisallowedByPolicy** 오류가 발생할 수 있습니다. 다음은 오류의 샘플입니다.

```
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>문제 해결

배포를 차단한 정책에 대한 세부 정보를 검색하려면 다음 방법 중 하나를 사용합니다.

### <a name="method-1"></a>방법 1

PowerShell에서 해당 정책 식별자를 **Id** 매개 변수로 제공하여 배포를 차단한 정책에 대한 정보를 검색합니다.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>방법 2 

Azure CLI 2.0에서 정책 정의 이름을 제공합니다. 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>해결 방법

보안 또는 규정 준수를 위해 IT 부서는 공용 IP 주소, 네트워크 보안 그룹, 사용자 정의 경로 또는 경로 테이블 만들기를 금지하는 리소스 정책을 적용할 수 있습니다. "증상" 섹션에 설명된 오류 메시지의 샘플에서 정책 이름은 **regionPolicyDefinition**이지만 이름은 다를 수 있습니다.
이 문제를 해결하려면 IT 부서와 협의하여 리소스 정책을 검토하고 해당 정책에 따라 요청된 작업을 수행할 방법을 결정합니다.


자세한 내용은 다음 문서를 참조하세요.

- [리소스 정책 개요](resource-manager-policy.md)
- [일반적인 배포 오류 - RequestDisallowedByPolicy](resource-manager-common-deployment-errors.md#requestdisallowedbypolicy)

 



