---
title: Azure 네트워크 보안 그룹 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 네트워크 보안 그룹을 만들거나, 변경하거나, 삭제하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 38fe9582595969ac92d3468b3b7e8c0a9d793c0c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708283"
---
# <a name="create-change-or-delete-a-network-security-group"></a>네트워크 보안 그룹을 만들기, 변경 또는 삭제

네트워크 보안 그룹의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md)를 참조하세요. 네트워크 보안 그룹에 대 한 몇 가지 경험을 얻기 위해 [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md) 자습서를 완료 합니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

계정이 없으면 활성 구독으로 Azure 계정을 설정 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 이 문서의 나머지 부분을 시작 하기 전에 다음 작업 중 하나를 완료 합니다.

- **포털 사용자**: Azure 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

- **Powershell 사용자**: [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행 하거나 컴퓨터에서 powershell을 실행 합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Azure Cloud Shell 브라우저 탭에서 **환경 선택** 드롭다운 목록을 찾은 다음 아직 선택 하지 않은 경우 **PowerShell** 을 선택 합니다.

    PowerShell을 로컬로 실행 하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상을 사용 합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az.Network`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

- **AZURE cli (명령줄 인터페이스) 사용자**: [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행 하거나 컴퓨터에서 CLI를 실행 합니다. Azure CLI를 로컬로 실행 하는 경우 Azure CLI 버전 2.0.28 이상을 사용 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. `az login`를 실행하여 Azure와 연결합니다.

에 로그인 하거나를 사용 하 여 Azure에 연결 하는 계정은 [네트워크 참가자 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 또는 [사용 권한](#permissions)에 나열 된 적절 한 작업이 할당 된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 에 할당 되어야 합니다.

## <a name="work-with-network-security-groups"></a>네트워크 보안 그룹으로 작업

네트워크 보안 그룹의 만들기, [모두 보기](#view-all-network-security-groups), [세부 정보 보기](#view-details-of-a-network-security-group), [변경](#change-a-network-security-group) 및 [삭제](#delete-a-network-security-group)를 수행할 수 있습니다. 네트워크 인터페이스 또는 서브넷에서 네트워크 보안 그룹을 [연결 또는 분리](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)할 수 있습니다.

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

각 Azure 위치 및 구독에 대해 만들 수 있는 네트워크 보안 그룹 수에는 제한이 있습니다. 자세히 알아보려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조 하세요.

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.

2. **네트워킹**을 선택 하 고 **네트워크 보안 그룹**을 선택 합니다.

3. **네트워크 보안 그룹 만들기** 페이지의 **기본 사항** 탭에서 다음 설정에 대 한 값을 설정 합니다.

    | 설정 | 작업 |
    | --- | --- |
    | **구독** | 구독을 선택합니다. |
    | **리소스 그룹** | 기존 리소스 그룹을 선택 하거나 **새로 만들기** 를 선택 하 여 새 리소스 그룹을 만듭니다. |
    | **이름** | 리소스 그룹 내에 고유한 텍스트 문자열을 입력 합니다. |
    | **지역** | 원하는 위치를 선택 합니다. |

4. **검토 + 만들기**를 선택합니다.

5. **유효성 검사 통과** 메시지가 표시 되 면 **만들기**를 선택 합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>모든 네트워크 보안 그룹 보기

[Azure Portal](https://portal.azure.com) 로 이동 하 여 네트워크 보안 그룹을 확인 합니다. **네트워크 보안 그룹**을 검색 하 고 선택 합니다. 구독에 대 한 네트워크 보안 그룹 목록이 표시 됩니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>네트워크 보안 그룹 세부 정보 보기

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 네트워크 보안 그룹을 확인 합니다. **네트워크 보안 그룹**을 검색 하 고 선택 합니다.

2. 네트워크 보안 그룹의 이름을 선택 합니다.

네트워크 보안 그룹의 메뉴 모음에 있는 **설정**에서 네트워크 보안 그룹에 연결 된 **인바운드 보안 규칙**, **아웃 바운드 보안 규칙**, **네트워크 인터페이스**및 **서브넷** 을 볼 수 있습니다.

**모니터링**아래에서 **진단 설정을**사용 하거나 사용 하지 않도록 설정할 수 있습니다. **지원 + 문제 해결**에서 **효과적인 보안 규칙**을 볼 수 있습니다. 자세히 알아보려면 [네트워크 보안 그룹에 대 한 진단 로깅](virtual-network-nsg-manage-log.md) 및 [VM 네트워크 트래픽 필터 문제 진단](diagnose-network-traffic-filter-problem.md)을 참조 하세요.

나열된 일반적인 Azure 설정에 대한 자세한 내용은 다음 문서를 참조하세요.

- [활동 로그](../azure-monitor/platform/platform-logs-overview.md)
- [액세스 제어(IAM)](../role-based-access-control/overview.md)
- [태그](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [중지](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [자동화 스크립트](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>네트워크 보안 그룹 변경

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 네트워크 보안 그룹을 확인 합니다. **네트워크 보안 그룹**을 검색 하 고 선택 합니다.

2. 변경 하려는 네트워크 보안 그룹의 이름을 선택 합니다.

가장 일반적인 변경 사항은 [보안 규칙을 추가](#create-a-security-rule)하 고, [규칙을 제거](#delete-a-security-rule)하 고, [네트워크 보안 그룹을 서브넷 또는 네트워크 인터페이스와 연결 하거나](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)분리 하는 것입니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg 업데이트](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>네트워크 인터페이스 또는 서브넷에서 네트워크 보안 그룹 연결 또는 분리

네트워크 보안 그룹을 네트워크 인터페이스에 연결하거나 분리하려면 [네트워크 인터페이스에 네트워크 보안 그룹 연결 또는 분리](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)를 참조합니다. 네트워크 보안 그룹을 서브넷에 연결하거나 분리하려면 [서브넷 설정 변경](virtual-network-manage-subnet.md#change-subnet-settings)을 참조합니다.

### <a name="delete-a-network-security-group"></a>네트워크 보안 그룹 삭제

네트워크 보안 그룹이 서브넷 또는 네트워크 인터페이스에 연결 된 경우에는 삭제할 수 없습니다. 네트워크 보안 그룹을 삭제하기 전에 모든 서브넷 및 네트워크 인터페이스에서 분리합니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 네트워크 보안 그룹을 확인 합니다. **네트워크 보안 그룹**을 검색 하 고 선택 합니다.

2. 삭제할 네트워크 보안 그룹의 이름을 선택 합니다.

3. 네트워크 보안 그룹의 도구 모음에서 **삭제**를 선택 합니다. 그런 다음, 확인 대화 상자에서 **예**를 선택합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>보안 규칙으로 작업

네트워크 보안 그룹은 0개 이상의 보안 규칙을 포함합니다. 보안 규칙의 만들기, [모두 보기](#view-all-security-rules), [세부 정보 보기](#view-details-of-a-security-rule), [변경](#change-a-security-rule) 및 [삭제](#delete-a-security-rule)를 수행할 수 있습니다.

### <a name="create-a-security-rule"></a>보안 규칙 만들기

각 Azure 위치 및 구독에 대해 만들 수 있는 네트워크 보안 그룹당 규칙 수에 제한이 있습니다. 자세히 알아보려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조 하세요.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 네트워크 보안 그룹을 확인 합니다. **네트워크 보안 그룹**을 검색 하 고 선택 합니다.

2. 보안 규칙을 추가 하려는 네트워크 보안 그룹의 이름을 선택 합니다.

3. 네트워크 보안 그룹의 메뉴 모음에서 **인바운드 보안 규칙** 또는 **아웃 바운드 보안 규칙**을 선택 합니다.

    추가 하지 않을 수 있는 몇 가지 기존 규칙이 나열 됩니다. 네트워크 보안 그룹을 만들 때 몇 가지 기본 보안 규칙이 만들어집니다. 자세한 내용은 [기본 보안 규칙](security-overview.md#default-security-rules)을 참조하세요.  기본 보안 규칙을 삭제할 수 없으나 더 높은 우선 순위의 규칙으로 재정의할 수 있습니다.

4. <a name="security-rule-settings"></a>**추가**를 선택 합니다. 다음 설정에 대 한 값을 선택 하거나 추가 하 고 **확인**을 선택 합니다.

    | Setting | 값 | 세부 정보 |
    | ------- | ----- | ------- |
    | **소스** | 다음 중 하나:<ul><li>**임의**</li><li>**IP 주소**</li><li>**서비스 태그** (인바운드 보안 규칙) 또는 **VirtualNetwork** (아웃 바운드 보안 규칙)</li><li>**응용 프로그램 &nbsp; 보안 &nbsp; 그룹**</li></ul> | <p>**Ip 주소**를 선택 하는 경우 **원본 ip 주소/c i d 범위**도 지정 해야 합니다.</p><p>**서비스 태그**를 선택 하는 경우 **원본 서비스 태그**를 선택할 수도 있습니다.</p><p>**응용 프로그램 보안 그룹**을 선택 하는 경우 기존 응용 프로그램 보안 그룹도 선택 해야 합니다. **원본** 및 **대상**모두에 대해 **응용 프로그램 보안 그룹** 을 선택 하는 경우 두 응용 프로그램 보안 그룹 내의 네트워크 인터페이스는 동일한 가상 네트워크에 있어야 합니다.</p> |
    | **원본 IP 주소/CIDR 범위** | 쉼표로 구분 된 IP 주소 목록 및 CIDR (클래스 간 라우팅) 범위 | <p>이 설정은 **원본** 에서 **IP 주소로**변경 하는 경우 나타납니다. 단일 값 또는 쉼표로 구분 된 여러 값 목록을 지정 해야 합니다. 여러 값의 예는 `10.0.0.0/16, 192.188.1.1` 입니다. 지정할 수 있는 값의 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조 하세요.</p><p>지정 하는 IP 주소를 Azure VM에 할당 하는 경우 해당 개인 IP 주소는 공용 IP 주소가 아닌 해당 개인 IP 주소를 지정 합니다. Azure는 공용 IP 주소를 인바운드 보안 규칙에 대 한 개인 IP 주소로 변환한 후 개인 IP 주소를 아웃 바운드 규칙에 대 한 공용 IP 주소로 변환 하기 전에 보안 규칙을 처리 합니다. Azure에서 공용 및 개인 IP 주소에 대한 자세히 알려면 [IP 주소 형식](virtual-network-ip-addresses-overview-arm.md)을 참조합니다.</p> |
    | **원본 서비스 태그** | 드롭다운 목록의 서비스 태그 | 인바운드 보안 규칙에 대 한 **원본** **서비스 태그** 를 설정 하는 경우이 선택적 설정이 표시 됩니다. 서비스 태그는 IP 주소 범주에 대한 사전 정의된 식별자입니다. 사용 가능한 서비스 태그와 각 태그가 나타내는 항목에 대 한 자세한 내용은 [서비스 태그](security-overview.md#service-tags)를 참조 하세요. |
    | **원본 응용 프로그램 보안 그룹** | 기존 응용 프로그램 보안 그룹 | 이 설정은 **원본** 에서 **응용 프로그램 보안 그룹**으로 설정 하는 경우 나타납니다. 네트워크 인터페이스와 동일한 지역에 있는 응용 프로그램 보안 그룹을 선택 합니다. [애플리케이션 보안 그룹을 만드는](#create-an-application-security-group) 방법을 알아봅니다. |
    | **원본 포트 범위** | 다음 중 하나:<ul><li>단일 포트 (예:)`80`</li><li>와 같은 포트 범위`1024-65535`</li><li>단일 포트 및/또는 포트 범위 (예:)의 쉼표로 구분 된 목록입니다.`80, 1024-65535`</li><li>`*`모든 포트에서 트래픽을 허용 하는 별표 ()</li></ul> | 이 설정은 규칙에서 트래픽을 허용 하거나 거부 하는 포트를 지정 합니다. 지정할 수 있는 포트의 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조 하세요. |
    | **대상이** | 다음 중 하나:<ul><li>**임의**</li><li>**IP 주소**</li><li>**서비스 태그** (아웃 바운드 보안 규칙) 또는 **VirtualNetwork** (인바운드 보안 규칙)</li><li>**응용 프로그램 &nbsp; 보안 &nbsp; 그룹**</li></ul> | <p>**Ip 주소**를 선택 하는 경우 **대상 ip 주소/c i d 범위**도 지정 합니다.</p><p>**VirtualNetwork**를 선택 하는 경우 가상 네트워크의 주소 공간 내의 모든 IP 주소에 대 한 트래픽이 허용 됩니다. **VirtualNetwork** 는 서비스 태그입니다.</p><p>**응용 프로그램 보안 그룹**을 선택 하는 경우 기존 응용 프로그램 보안 그룹을 선택 해야 합니다. [애플리케이션 보안 그룹을 만드는](#create-an-application-security-group) 방법을 알아봅니다.</p> |
    | **대상 IP 주소/c i d/CIDR 범위** | 쉼표로 구분 된 IP 주소 목록 및 CIDR 범위 | <p>**대상** **IP 주소**를 변경 하는 경우이 설정이 표시 됩니다. **원본** 및 **원본 IP 주소/c i d/CIDR 범위**와 마찬가지로 단일 또는 여러 개의 주소 또는 범위를 지정할 수 있습니다. 지정할 수 있는 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조 하세요.</p><p>지정 하는 IP 주소를 Azure VM에 할당 하는 경우 공용 IP 주소가 아니라 개인 IP를 지정 해야 합니다. Azure는 공용 IP 주소를 인바운드 보안 규칙에 대 한 개인 IP 주소로 변환 하 고 나 서 Azure가 개인 IP 주소를 아웃 바운드 규칙에 대 한 공용 IP 주소로 변환 하기 전에 보안 규칙을 처리 합니다. Azure에서 공용 및 개인 IP 주소에 대한 자세히 알려면 [IP 주소 형식](virtual-network-ip-addresses-overview-arm.md)을 참조합니다.</p> |
    | **대상 서비스 태그** | 드롭다운 목록의 서비스 태그 | 이 선택적 설정은 아웃 바운드 보안 규칙에 대 한 **대상** **서비스 태그** 를 변경 하는 경우에 나타납니다. 서비스 태그는 IP 주소 범주에 대한 사전 정의된 식별자입니다. 사용 가능한 서비스 태그와 각 태그가 나타내는 항목에 대 한 자세한 내용은 [서비스 태그](security-overview.md#service-tags)를 참조 하세요. |
    | **대상 응용 프로그램 보안 그룹** | 기존 응용 프로그램 보안 그룹 | 이 설정은 **대상** 을 **응용 프로그램 보안 그룹**으로 설정 하는 경우 나타납니다. 네트워크 인터페이스와 동일한 지역에 있는 응용 프로그램 보안 그룹을 선택 합니다. [애플리케이션 보안 그룹을 만드는](#create-an-application-security-group) 방법을 알아봅니다. |
    | **대상 포트 범위** | 다음 중 하나:<ul><li>단일 포트 (예:)`80`</li><li>와 같은 포트 범위`1024-65535`</li><li>단일 포트 및/또는 포트 범위 (예:)의 쉼표로 구분 된 목록입니다.`80, 1024-65535`</li><li>`*`모든 포트에서 트래픽을 허용 하는 별표 ()</li></ul> | **원본 포트 범위**와 마찬가지로 단일 또는 여러 포트와 범위를 지정할 수 있습니다. 지정할 수 있는 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조 하세요. |
    | **프로토콜** | **모든**, **TCP**, **UDP**또는 **ICMP** | 규칙을 TCP (전송 제어 프로토콜), UDP (사용자 데이터 그램 프로토콜) 또는 ICMP (Internet Control Message Protocol)로 제한할 수 있습니다. 모든 프로토콜에 적용 되는 규칙의 기본값은입니다. |
    | **동작** | **허용** 또는 **거부** | 이 설정은이 규칙이 제공 된 원본 및 대상 구성에 대 한 액세스를 허용 하거나 거부 하는지 여부를 지정 합니다. |
    | **Priority** | 네트워크 보안 그룹 내의 모든 보안 규칙에 대해 고유한 100에서 4096 사이의 값 | Azure는 보안 규칙을 우선 순위에 따라 처리 합니다. 번호가 낮을수록 우선 순위가 높습니다. 100, 200 및 300와 같이 규칙을 만들 때 우선 순위 번호 사이에 간격을 두는 것이 좋습니다. 간격을 벗어나면 나중에 규칙을 더 쉽게 추가할 수 있으므로 기존 규칙 보다 높거나 낮은 우선 순위를 지정할 수 있습니다. |
    | **이름** | 네트워크 보안 그룹 내의 규칙에 대 한 고유 이름 | 이름은 최대 80자까지 가능합니다. 문자 또는 숫자로 시작 해야 하며 문자, 숫자 또는 밑줄로 끝나야 합니다. 이름에는 문자, 숫자, 밑줄, 마침표 또는 하이픈만 사용할 수 있습니다. |
    | **설명** | 텍스트 설명 | 보안 규칙에 대 한 텍스트 설명을 선택적으로 지정할 수 있습니다. |

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>모든 보안 규칙 보기

네트워크 보안 그룹에는 0 개 이상의 규칙이 포함 되어 있습니다. 경로를 볼 때 나열되는 정보에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md)를 참조합니다.

1. 네트워크 보안 그룹의 규칙을 보려면 [Azure Portal](https://portal.azure.com) 으로 이동 합니다. **네트워크 보안 그룹**을 검색 하 고 선택 합니다.

2. 규칙을 보려는 네트워크 보안 그룹의 이름을 선택 합니다.

3. 네트워크 보안 그룹의 메뉴 모음에서 **인바운드 보안 규칙** 또는 **아웃 바운드 보안 규칙**을 선택 합니다.

이 목록에는 사용자가 만든 모든 규칙과 네트워크 보안 그룹의 [기본 보안 규칙이](security-overview.md#default-security-rules)포함 되어 있습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>보안 규칙의 세부 정보 보기

1. 네트워크 보안 그룹의 규칙을 보려면 [Azure Portal](https://portal.azure.com) 으로 이동 합니다. **네트워크 보안 그룹**을 검색 하 고 선택 합니다.

2. 규칙에 대 한 세부 정보를 보려는 네트워크 보안 그룹의 이름을 선택 합니다.

3. 네트워크 보안 그룹의 메뉴 모음에서 **인바운드 보안 규칙** 또는 **아웃 바운드 보안 규칙**을 선택 합니다.

4. 세부 정보를 보려는 규칙을 선택합니다. 모든 설정에 대 한 설명은 [보안 규칙 설정](#security-rule-settings)을 참조 하세요.

    > [!NOTE]
    > 이 절차는 사용자 지정 보안 규칙에만 적용 됩니다. 기본 보안 규칙을 선택 하는 경우에는 작동 하지 않습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>보안 규칙 변경

1. [보안 규칙 세부 정보 보기](#view-details-of-a-security-rule)에서 단계를 완료합니다.

2. 필요에 따라 설정을 변경 하 고 **저장**을 선택 합니다. 모든 설정에 대 한 설명은 [보안 규칙 설정](#security-rule-settings)을 참조 하세요.

    > [!NOTE]
    > 이 절차는 사용자 지정 보안 규칙에만 적용 됩니다. 기본 보안 규칙은 변경할 수 없습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>보안 규칙 삭제

1. [보안 규칙 세부 정보 보기](#view-details-of-a-security-rule)에서 단계를 완료합니다.

2. **삭제**를 선택한 후 **예**를 선택합니다.

    > [!NOTE]
    > 이 절차는 사용자 지정 보안 규칙에만 적용 됩니다. 기본 보안 규칙을 삭제할 수 없습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>애플리케이션 보안 그룹으로 작업

애플리케이션 보안 그룹에는 0개 이상의 네트워크 인터페이스가 포함되어 있습니다. 자세한 내용은 [애플리케이션 보안 그룹](security-overview.md#application-security-groups)을 참조하세요. 애플리케이션 보안 그룹의 모든 네트워크 인터페이스는 동일한 가상 네트워크에 있어야 합니다. 애플리케이션 보안 그룹에 네트워크 인터페이스를 추가하는 방법을 알아보려면 [애플리케이션 보안 그룹에 네트워크 인터페이스 추가](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)를 참조합니다.

### <a name="create-an-application-security-group"></a>애플리케이션 보안 그룹 만들기

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.

2. 검색 상자에 *응용 프로그램 보안 그룹*을 입력 합니다.

3. **응용 프로그램 보안 그룹** 페이지에서 **만들기**를 선택 합니다.

4. **응용 프로그램 보안 그룹 만들기** 페이지의 **기본 사항** 탭에서 다음 설정에 대 한 값을 설정 합니다.

    | 설정 | 작업 |
    | --- | --- |
    | **구독** | 구독을 선택합니다. |
    | **리소스 그룹** | 기존 리소스 그룹을 선택 하거나 **새로 만들기** 를 선택 하 여 새 리소스 그룹을 만듭니다. |
    | **이름** | 리소스 그룹 내에 고유한 텍스트 문자열을 입력 합니다. |
    | **지역** | 원하는 위치를 선택 합니다. |

5. **검토 + 만들기**를 선택합니다.

6. **검토 + 만들기** 탭에서 **유효성 검사 통과** 메시지가 표시 되 면 **만들기**를 선택 합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>모든 애플리케이션 보안 그룹 보기

[Azure Portal](https://portal.azure.com) 로 이동 하 여 응용 프로그램 보안 그룹을 확인 합니다. **응용 프로그램 보안 그룹**을 검색 하 고 선택 합니다. Azure Portal는 응용 프로그램 보안 그룹의 목록을 표시 합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg list](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>특정 애플리케이션 보안 그룹의 세부 정보 보기

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 응용 프로그램 보안 그룹을 확인 합니다. **응용 프로그램 보안 그룹**을 검색 하 고 선택 합니다.

2. 세부 정보를 보려는 응용 프로그램 보안 그룹의 이름을 선택 합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>애플리케이션 보안 그룹 변경

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 응용 프로그램 보안 그룹을 확인 합니다. **응용 프로그램 보안 그룹**을 검색 하 고 선택 합니다.

2. 변경 하려는 응용 프로그램 보안 그룹의 이름을 선택 합니다.

3. 수정 하려는 설정 옆의 **변경** 을 선택 합니다. 예를 들어 **태그**를 추가 또는 제거 하거나 **리소스 그룹** 또는 **구독**을 변경할 수 있습니다.

    > [!NOTE]
    > 위치를 변경할 수 없습니다.

    메뉴 모음에서 **액세스 제어 (IAM)** 를 선택할 수도 있습니다. **액세스 제어 (IAM)** 페이지에서 응용 프로그램 보안 그룹에 대 한 사용 권한을 할당 하거나 제거할 수 있습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg 업데이트](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | PowerShell cmdlet 없음 |

### <a name="delete-an-application-security-group"></a>애플리케이션 보안 그룹 삭제

응용 프로그램 보안 그룹에 네트워크 인터페이스가 포함 되어 있는 경우에는 응용 프로그램 보안 그룹을 삭제할 수 없습니다. 응용 프로그램 보안 그룹에서 모든 네트워크 인터페이스를 제거 하려면 네트워크 인터페이스 설정을 변경 하거나 네트워크 인터페이스를 삭제 합니다. 자세히 알아보려면 [응용 프로그램 보안 그룹에서 추가 또는 제거](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) 또는 [네트워크 인터페이스 삭제](virtual-network-network-interface.md#delete-a-network-interface)를 참조 하세요.

1. 응용 프로그램 보안 그룹을 관리 하려면 [Azure Portal](https://portal.azure.com) 로 이동 합니다. **응용 프로그램 보안 그룹**을 검색 하 고 선택 합니다.

2. 삭제 하려는 응용 프로그램 보안 그룹의 이름을 선택 합니다.

3. **삭제**를 선택하고 **예**를 선택하여 애플리케이션 보안 그룹을 삭제합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>사용 권한

네트워크 보안 그룹, 보안 규칙 및 응용 프로그램 보안 그룹에 대 한 작업을 수행 하려면 다음 표에 나열 된 적절 한 사용 권한이 할당 된 [네트워크 참여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 에 계정을 할당 해야 합니다.

### <a name="network-security-group"></a>네트워크 보안 그룹

| 작업                                                        |   이름                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   네트워크 보안 그룹 가져오기                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   네트워크 보안 그룹 만들기 또는 업데이트                             |
| Microsoft.Network/networkSecurityGroups/delete                |   네트워크 보안 그룹 삭제                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   네트워크 보안 그룹을 서브넷 또는 네트워크 인터페이스에 연결 

### <a name="network-security-group-rule"></a>네트워크 보안 그룹 규칙

| 작업                                                        |   이름                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   규칙 가져오기                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   규칙 만들기 또는 업데이트                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   규칙 삭제                                                         |

### <a name="application-security-group"></a>애플리케이션 보안 그룹

| 작업                                                                     | 이름                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP 구성을 애플리케이션 보안 그룹에 조인|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 보안 규칙을 애플리케이션 보안 그룹에 조인    |
| Microsoft.Network/applicationSecurityGroups/read                           | 애플리케이션 보안 그룹 가져오기                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 애플리케이션 보안 그룹 만들기 또는 업데이트           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 애플리케이션 보안 그룹 삭제                     |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트 또는 Azure [리소스 관리자 템플릿을](template-samples.md) 사용 하 여 네트워크 또는 응용 프로그램 보안 그룹 만들기
- 가상 네트워크에 대 한 [정의 Azure Policy](policy-samples.md) 만들기 및 할당
