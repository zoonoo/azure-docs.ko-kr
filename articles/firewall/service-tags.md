---
title: Azure Firewall 서비스 태그 개요
description: 서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d32d5daf67fe7ab30f3ac1b132bd74edd78e8712
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110700492"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall 서비스 태그

서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다. 고유한 서비스 태그를 직접 만들거나 태그 내에 포함된 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

Azure Firewall 서비스 태그는 네트워크 규칙 대상 필드에 사용할 수 있습니다. 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다.

## <a name="supported-service-tags"></a>지원되는 서비스 태그

Azure Firewall 네트워크 규칙에서 사용할 수 있는 서비스 태그 목록은 [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)를 참조하세요.

## <a name="configuration"></a>Configuration

Azure Firewall은 PowerShell, Azure CLI 또는 Azure Portal을 통해 서비스 태그 구성을 지원합니다.

### <a name="configure-via-azure-powershell"></a>Azure PowerShell을 통해 구성

이 예제에서는 먼저 이전에 만든 Azure Firewall 인스턴스에 대한 컨텍스트를 가져와야 합니다.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

그런 다음, 새 규칙을 만들어야 합니다.  원본 또는 대상의 경우 이 문서의 앞부분에서 설명한 대로 사용할 서비스 태그의 텍스트 값을 지정할 수 있습니다.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

다음으로, Azure Firewall 정의가 포함된 변수를 만든 새 네트워크 규칙으로 업데이트해야 합니다.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

마지막으로, 실행 중인 Azure Firewall 인스턴스에 네트워크 규칙 변경 사항을 커밋해야 합니다.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>다음 단계

Azure Firewall 규칙에 대한 자세한 내용은 [Azure Firewall 규칙 처리 논리](rule-processing.md)를 참조하세요.
