---
title: Azure 방화벽의 IP 그룹
description: IP 그룹을 사용 하 여 Azure 방화벽 규칙에 대 한 IP 주소를 그룹화 및 관리할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444486"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Azure 방화벽의 IP 그룹 (미리 보기)

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IP 그룹을 사용 하 여 다음과 같은 방법으로 Azure 방화벽 규칙에 대 한 IP 주소를 그룹화 하 고 관리할 수 있습니다.

- DNAT 규칙의 원본 주소로
- 네트워크 규칙에서 원본 또는 대상 주소로
- 응용 프로그램 규칙의 원본 주소로


IP 그룹은 단일 IP 주소, 여러 IP 주소 또는 하나 이상의 IP 주소 범위를 가질 수 있습니다.

Azure의 지역 및 구독에서 여러 방화벽에 대 한 Azure 방화벽 DNAT, 네트워크 및 응용 프로그램 규칙에서 IP 그룹을 다시 사용할 수 있습니다. 그룹 이름은 고유 해야 합니다. Azure Portal, Azure CLI 또는 REST API에서 IP 그룹을 구성할 수 있습니다. 시작 하는 데 도움이 되는 샘플 템플릿이 제공 됩니다.

## <a name="sample-format"></a>샘플 형식

IP 그룹에서 사용할 수 있는 IPv4 주소 형식 예는 다음과 같습니다.

- 단일 주소: 10.0.0.0
- CIDR 표기법: 10.1.0.0/32
- 주소 범위: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>IP 그룹 만들기

IP 그룹은 Azure Portal, Azure CLI 또는 REST API를 사용 하 여 만들 수 있습니다. 자세한 내용은 [IP 그룹 만들기 (미리 보기)](create-ip-group.md)를 참조 하세요.

## <a name="browse-ip-groups"></a>IP 그룹 찾아보기
1. Azure Portal 검색 창에서 **IP 그룹** 을 입력 하 고 선택 합니다. IP 그룹 목록을 보거나 **추가** 를 선택 하 여 새 ip 그룹을 만들 수 있습니다.
2. IP 그룹을 선택 하 여 개요 페이지를 엽니다. IP 주소 또는 IP 그룹을 편집, 추가 또는 삭제할 수 있습니다.

   ![IP 그룹 개요](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>IP 그룹 관리

IP 그룹의 모든 IP 주소와 연결 된 규칙 또는 리소스를 볼 수 있습니다. IP 그룹을 삭제 하려면 먼저 ip 그룹을 사용 하는 리소스에서 IP 그룹을 분리 해야 합니다.

1. IP 주소를 보거나 편집 하려면 왼쪽 창의 **설정** 에서 **ip 주소** 를 선택 합니다.
2. 단일 또는 여러 IP 주소를 추가 하려면 **ip 주소 추가**를 선택 합니다. 업로드에 대 한 **끌기 또는 찾아보기** 페이지가 열리거나 수동으로 주소를 입력할 수 있습니다.
3.  오른쪽에 있는 줄임표 ( **...** )를 선택 하 여 IP 주소를 편집 하거나 삭제 합니다. 여러 IP 주소를 편집 하거나 삭제 하려면 상자를 선택 하 고 위쪽에서 **편집** 또는 **삭제** 를 선택 합니다.
4. 마지막으로는 CSV 파일 형식으로 파일을 내보낼 수 있습니다.

> [!NOTE]
> 규칙에서 아직 사용 중인 IP 그룹의 모든 IP 주소를 삭제 하면 해당 규칙을 건너뜁니다.


## <a name="use-an-ip-group"></a>IP 그룹 사용

이제, 응용 프로그램 또는 네트워크 규칙에 따라 Azure 방화벽 DNAT 만들 **때 ip 주소** 를 **원본 유형** 또는 ip 주소에 대 한 **대상 유형** 으로 선택할 수 있습니다.

> [!NOTE]
> 방화벽 정책에서는 IP 그룹이 지원 되지 않습니다. 현재 기존 방화벽 규칙 에서만 지원 됩니다.

![방화벽의 IP 그룹](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>지역 가용성

IP 그룹은 현재 다음 지역에서 제공 됩니다.

- 미국 서부
- 미국 서부 2
- 미국 동부
- 미국 동부 2
- 미국 중부
- 미국 중북부
- 미국 중서부
- 미국 중남부
- 캐나다 중부
- 북유럽
- 서유럽
- 프랑스 중부
- 영국 남부
- 오스트레일리아 동부
- 오스트레일리아 중부
- 오스트레일리아 남동부

## <a name="related-azure-powershell-cmdlets"></a>관련 Azure PowerShell cmdlet

다음 Azure PowerShell cmdlet을 사용 하 여 IP 그룹을 만들고 관리할 수 있습니다.

- [AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.