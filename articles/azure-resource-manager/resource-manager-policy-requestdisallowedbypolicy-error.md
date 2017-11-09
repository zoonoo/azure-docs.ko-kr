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
ms.topic: support-article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 2e821c0369c6f01a7f09361c1093259429a79fa6
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 리소스 정책의 RequestDisallowedByPolicy 오류

이 문서에서는 RequestDisallowedByPolicy 오류의 원인을 설명하고 해결 방법을 제공합니다.

## <a name="symptom"></a>증상

배포하는 동안 작업을 수행하려고 시도하면 작업이 완료되지 않게 방해하는 **RequestDisallowedByPolicy** 오류가 발생할 수 있습니다. 다음 예제에서는 오류가 발생합니다.

```json
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

PowerShell에서 해당 정책 식별자를 `Id` 매개 변수로 제공하여 배포를 차단한 정책에 대한 정보를 검색합니다.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>방법 2 

Azure CLI 2.0에서 정책 정의 이름을 제공합니다. 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>해결 방법

보안 또는 규정 준수를 위해 IT 부서는 공용 IP 주소, 네트워크 보안 그룹, 사용자 정의 경로 또는 경로 테이블 만들기를 금지하는 리소스 정책을 적용할 수 있습니다. **증상** 섹션의 오류 메시지에 **regionPolicyDefinition**이라는 정책이 표시됩니다. 정책 이름이 다를 수 있습니다.
이 문제를 해결하려면 IT 부서와 협의하여 리소스 정책을 검토하고 해당 정책에 따라 요청된 작업을 수행할 방법을 결정합니다.

자세한 내용은 다음 문서를 참조하세요.

- [리소스 정책 개요](resource-manager-policy.md)
- [포털을 통해 정책 할당 보기](resource-manager-policy-portal.md)
