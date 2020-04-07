---
title: Azure 방화벽의 IP 그룹
description: IP 그룹을 사용하면 Azure 방화벽 규칙에 대한 IP 주소를 그룹화하고 관리할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: e0638cbccd5e3bc282dbdd7d3b5918e29081a12b
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757169"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Azure 방화벽의 IP 그룹(미리 보기)

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IP 그룹을 사용하면 다음과 같은 방법으로 Azure 방화벽 규칙에 대한 IP 주소를 그룹화하고 관리할 수 있습니다.

- DNAT 규칙의 소스 주소로
- 네트워크 규칙의 원본 또는 대상 주소로
- 응용 프로그램 규칙의 원본 주소로


IP 그룹에는 단일 IP 주소, 여러 IP 주소 또는 하나 이상의 IP 주소 범위를 가질 수 있습니다.

IP 그룹은 Azure의 리전 및 구독에 걸쳐 여러 방화벽에 대한 Azure 방화벽 DNAT, 네트워크 및 응용 프로그램 규칙에서 다시 사용할 수 있습니다. 그룹 이름은 고유해야 합니다. Azure 포털, Azure CLI 또는 REST API에서 IP 그룹을 구성할 수 있습니다. 시작하는 데 도움이 되는 샘플 템플릿이 제공됩니다.

## <a name="sample-format"></a>샘플 형식

다음 IPv4 주소 형식 예제는 IP 그룹에서 사용할 수 있습니다.

- 단일 주소: 10.0.0.0
- CIDR 표기: 10.1.0.0/32
- 주소 범위: 10.2.0.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>IP 그룹 만들기

IP 그룹은 Azure 포털, Azure CLI 또는 REST API를 사용하여 만들 수 있습니다. 자세한 내용은 [IP 그룹 만들기(미리 보기)를](create-ip-group.md)참조하십시오.

## <a name="browse-ip-groups"></a>IP 그룹 찾아보기
1. Azure 포털 검색 창에서 **IP 그룹을** 입력하고 선택합니다. IP 그룹 목록을 보거나 **추가를** 선택하여 새 IP 그룹을 만들 수 있습니다.
2. IP 그룹을 선택하여 개요 페이지를 엽니다. IP 주소 또는 IP 그룹을 편집, 추가 또는 삭제할 수 있습니다.

   ![IP 그룹 개요](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>IP 그룹 관리

IP 그룹의 모든 IP 주소와 연결된 규칙 또는 리소스를 볼 수 있습니다. IP 그룹을 삭제하려면 먼저 IP 그룹을 사용하는 리소스에서 분리해야 합니다.

1. IP 주소를 보거나 편집하려면 왼쪽 창의 **설정에서** **IP 주소를** 선택합니다.
2. 단일 또는 다중 IP 주소(들)를 추가하려면 **IP 주소 추가를 선택합니다.** 이렇게 하면 업로드를 위해 **끌어서거나 찾아보기** 페이지가 열리거나 주소를 수동으로 입력할 수 있습니다.
3.    IP 주소를 편집하거나 **…** 삭제할 수 있는 오른쪽으로 타원(... ) 을 선택합니다. 여러 IP 주소를 편집하거나 삭제하려면 상자를 선택하고 맨 위에서 **편집** 또는 **삭제를** 선택합니다.
4. 마지막으로 CSV 파일 형식으로 파일을 내보낼 수 있습니다.

> [!NOTE]
> IP 그룹이 규칙에서 계속 사용 중인 동안 IP 그룹의 모든 IP 주소를 삭제하면 해당 규칙이 건너뜁니다.


## <a name="use-an-ip-group"></a>IP 그룹 사용

이제 Azure 방화벽 DNAT, 응용 프로그램 또는 네트워크 규칙을 만들 때 IP 그룹에 대한 **소스 유형** 또는 IP 주소(es)의 **대상 유형으로** IP **그룹을** 선택할 수 있습니다.

> [!NOTE]
> IP 그룹은 방화벽 정책에서 지원되지 않습니다. 현재는 기존 방화벽 규칙에서만 지원됩니다.

![방화벽의 IP 그룹](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>지역 가용성

IP 그룹은 모든 퍼블릭 클라우드 리전에서 사용할 수 있습니다.

## <a name="ip-address-limits"></a>IP 주소 제한

IP 그룹 50개 이하의 경우 방화벽 인스턴스당 각각 최대 5,000개의 개별 IP 주소를 가질 수 있습니다. 51~100개의 IP 그룹의 경우 방화벽 인스턴스당 각각 500개의 개별 IP 주소를 가질 수 있습니다.

### <a name="examples"></a>예

#### <a name="example-1-supported"></a>예 1: 지원

|IP 그룹  |# IP 주소  |Notation  |규칙  |
|---------|---------|---------|---------|
|IP그룹1 |4096     |10.0.0.0/20  |규칙 1|
|IP그룹2     |3|196.0.0.0 - 196.0.0.2|규칙 1|
|IP그룹3     |1|1.2.3.4|규칙 1|
|     |**합계 4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>예 2: 지원

|IP 그룹  |# IP 주소  |Notation  |규칙  |
|---------|---------|---------|---------|
|IP그룹1 |4096     |10.0.0.0/20  |규칙 1|
|IP그룹2     |4096|11.0.0.0/20|규칙 1|
|     |**합 계 8192**|         |         |

#### <a name="example-3-not-supported"></a>예 3: 지원되지 않음

|IP 그룹  |# IP 주소  |Notation  |규칙  |
|---------|---------|---------|---------|
|IP그룹1 |8192     |10.0.0.0/20, 11.0.0.0/20  |규칙 1|
|     |**합 계 8192**|||

#### <a name="example-4-supported"></a>예 4: 지원

|IP 그룹  |# IP 주소  |Notation  |규칙  |
|---------|---------|---------|---------|
|IP그룹1 |4096     |10.0.0.0/20  |규칙 1|
|IP그룹2     |4096|11.0.0.0/20|규칙 2|
|     |**합 계 8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>관련 Azure PowerShell cmdlet

다음 Azure PowerShell cmdlet을 사용하여 IP 그룹을 만들고 관리할 수 있습니다.

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [제거-아즈IP 그룹](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [새 아즈방화벽네트워크룰](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [뉴 아즈방화벽나트룰](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.