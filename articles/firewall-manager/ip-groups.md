---
title: Azure Firewall 정책의 IP 그룹
description: IP 그룹을 사용하면 Azure Firewall 정책 규칙에 대한 IP 주소를 그룹화하고 관리할 수 있습니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 54faa0bc7d414eb15a866b8e1e6d09e15b22b071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500855"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>Azure Firewall 정책의 IP 그룹

IP 그룹을 사용하면 다음과 같은 방법으로 Azure Firewall 정책에 대한 IP 주소를 그룹화하고 관리할 수 있습니다.

- DNAT 규칙의 원본 형식으로
- 네트워크 규칙의 원본 또는 대상 형식으로
- 애플리케이션 규칙의 원본 형식으로


IP 그룹은 단일 IP 주소, 여러 IP 주소 또는 하나 이상의 IP 주소 범위를 가질 수 있습니다.

IP 그룹은 Azure의 지역 및 구독에 걸쳐 여러 방화벽에 대한 Azure Firewall DNAT, 네트워크, 애플리케이션 규칙에서 다시 사용할 수 있습니다. 그룹 이름은 고유해야 합니다. Azure Portal, Azure CLI, REST API에서 IP 그룹을 구성할 수 있습니다. 시작하는 데 도움이 되는 샘플 템플릿이 제공됩니다.

## <a name="sample-format"></a>샘플 형식

다음은 IP 그룹에서 사용할 수 있는 IPv4 주소 형식 예입니다.

- 단일 주소: 10.0.0.0
- CIDR 표기법: 10.1.0.0/32
- 주소 범위: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>IP 그룹 만들기

IP 그룹은 Azure Portal, Azure CLI, REST API를 사용하여 만들 수 있습니다. 자세한 내용은 [IP 그룹 만들기](../firewall/create-ip-group.md)를 참조하세요.

## <a name="browse-ip-groups"></a>IP 그룹 찾아보기
1. Azure Portal 검색 창에서 **IP 그룹** 을 입력하고 선택합니다. IP 그룹 목록을 보거나 **추가** 를 선택하여 새 IP 그룹을 만들 수 있습니다.
2. IP 그룹을 선택하여 개요 페이지를 엽니다. IP 주소 또는 IP 그룹을 편집, 추가, 삭제할 수 있습니다.

   ![IP 그룹 개요](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>IP 그룹 관리

IP 그룹의 모든 IP 주소, 연결된 규칙 또는 리소스를 볼 수 있습니다. IP 그룹을 삭제하려면 먼저 IP 그룹을 사용하는 리소스에서 IP 그룹을 분리해야 합니다.

1. IP 주소를 보거나 편집하려면 왼쪽 창의 **설정** 에서 **IP 주소** 를 선택합니다.
2. 단일 IP 주소 또는 여러 IP 주소를 추가하려면 **IP 주소 추가** 를 선택합니다. 그러면 업로드를 위한 **끌기 또는 찾아보기** 페이지가 열리거나 수동으로 주소를 입력할 수 있습니다.
3.    오른쪽에 있는 생략 부호( **...** )를 선택하여 IP 주소를 편집하거나 삭제합니다. 여러 IP 주소를 편집하거나 삭제하려면 상자를 선택하고 상단에서 **편집** 또는 **삭제** 를 선택합니다.
4. 이제 CSV 파일 형식으로 파일을 내보낼 수 있습니다.

> [!NOTE]
> 규칙에서 아직 사용 중인 IP 그룹의 모든 IP 주소를 삭제하면 해당 규칙을 건너뜁니다.


## <a name="use-an-ip-group"></a>IP 그룹 사용

이제 DNAT 관련 정책, 애플리케이션, 네트워크 규칙을 만들 때 IP 주소에 대한 **원본 유형** 또는 **대상 유형** 으로 **IP 그룹** 을 선택할 수 있습니다.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="Firewall의 IP 그룹":::

## <a name="ip-address-limits"></a>IP 주소 한도

각 IP 그룹당 최대 5,000개의 개별 IP 주소 또는 IP 접두사를 사용하여 방화벽당 최대 100개의 IP 그룹을 사용할 수 있습니다.

## <a name="related-azure-powershell-cmdlets"></a>관련 Azure PowerShell cmdlet

다음 Azure PowerShell cmdlet을 사용하여 IP 그룹을 만들고 관리할 수 있습니다.

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [New-AzFirewallPolicyNetworkRule](/powershell/module/az.network/new-azfirewallpolicynetworkrule)
- [New-AzFirewallPolicyApplicationRule](/powershell/module/az.network/new-azfirewallpolicyapplicationrule)
- [New-AzFirewallPolicyNatRule](/powershell/module/az.network/new-azfirewallpolicynatrule)

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Firewall Manager를 사용하여 가상 WAN 보호](secure-cloud-network.md)