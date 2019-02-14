---
title: Azure 공용 IP 주소 접두사 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 공용 IP 주소 접두사를 만들거나 변경하거나 삭제하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: f4da0f992914037f5c95050324af5762e90a2ca4
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696833"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기, 변경 또는 삭제

공용 IP 주소 접두사 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소 접두사는 사용자가 지정하는 공용 IP 주소 수에 기반을 둔 연속 주소 범위입니다. 주소는 구독에 할당됩니다. 공용 IP 주소 리소스를 만들 때 접두사에서 고정 공용 IP 주소를 만들고 가상 머신, 부하 분산 장치 또는 기타 리소스에 연결하여 인터넷 연결을 지원할 수 있습니다. 공용 IP 주소 접두사에 익숙하지 않다면 [공용 IP 주소 접두사 개요](public-ip-address-prefix.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

> [!IMPORTANT]
> 공용 IP 접두사는 제한된 지역에서 공개 미리 보기 상태입니다. [미리 보기 상태의 의미](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)를 알아볼 수 있습니다. 공용 IP 접두사는 현재 미국 중서부, 미국 서부, 미국 서부 2, 미국 중부, 북유럽, 유럽 서부 및 동남 아시아에서 사용할 수 있습니다. 업데이트된 지역 목록은 [Azure 업데이트](https://azure.microsoft.com/updates/?product=virtual-network)에서 확인하세요.

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://aka.ms/publicipprefixportal을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 AzureRm.Network PowerShell 모듈 버전 6.3.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM.Network`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://github.com/Azure/azure-powershell/releases/tag/AzureRm.Network.6.3.1)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.41 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

공용 IP 주소 접두사에는 요금이 부과됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses)을 참조하세요.

## <a name="create-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기

1. 포털의 왼쪽 위 모서리에서 **+ 리소스 만들기**를 선택합니다.
2. *마켓플레이스 검색* 상자에 *공용 IP 주소 접두사*를 입력합니다. 검색 결과에 표시된 **공용 IP 주소 접두사**를 선택합니다.
3. **공용 IP 주소 접두사** 아래에서 **만들기**를 선택합니다.
4. **공용 IP 주소 접두사 만들기** 아래에서 다음 설정의 값을 입력하거나 선택한 다음, 만들기**를 선택합니다**:

   |설정|Required?|세부 정보|
   |---|---|---|
   |구독|예|공용 IP 주소를 연결하려는 리소스와 동일한 [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)에 있어야 합니다.|
   |리소스 그룹|예|공용 IP 주소를 연결하려는 리소스와 동일하거나 다른 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)에 있을 수 있습니다.|
   |Name|예|이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
   |지역|예|범위에서 주소를 할당할 공용 IP 주소와 동일한 [지역](https://azure.microsoft.com/regions)에 있어야 합니다. 접두사는 현재 미국 중서부, 미국 서부, 미국 서부 2, 미국 중부, 북유럽, 유럽 서부 및 동남 아시아에서 미리 보기로 사용할 수 있습니다.|
   |접두사 크기|예| 필요한 접두사의 크기입니다. /28 또는 16개의 IP 주소가 기본값입니다. 

**명령**


|도구|명령|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/new-azurermpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>접두사로 고정 공용 IP 주소 만들기
접두사를 만들었으면 접두사로 고정 IP 주소를 만들어야 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *공용 IP 주소 접두사*를 입력합니다. 검색 결과에 표시된 **공용 IP 주소 접두사**를 선택합니다.
2. 공용 IP를 만들려는 접두사를 선택합니다.
3. 해당 항목이 검색 결과에 표시되면 선택하고 개요 섹션에서 **+IP 주소 추가**를 클릭합니다. 이 항목이 보이지 않으면 미리 보기에 대한 올바른 링크(https://aka.ms/publicipprefixportal)를 사용 중인지 확인하세요.
4. **공용 IP 주소 만들기** 아래에서 다음 설정의 값을 입력하거나 선택합니다. 접두사는 Standard SKU, IPv4 및 고정 주소용이므로 다음과 같은 정보만 제공해야 합니다.

   |설정|Required?|세부 정보|
    |---|---|---|
    |Name|예|공용 IP 주소의 이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
   |유휴 제한 시간(분)|아니요|연결 유지 메시지를 보내는 데 클라이언트를 사용하지 않고 TCP 또는 HTTP 연결을 유지하는 데 걸리는 시간(분)입니다. |
   |DNS 이름 레이블|아니요|이름을 만드는 Azure 지역 내에서(모든 구독 및 모든 고객에서) 고유해야 합니다. Azure는 해당 DNS에서 이름과 IP 주소를 자동으로 등록하므로 해당 이름을 사용하는 리소스에 연결할 수 있습니다. Azure에서는 정규화된 DNS 이름을 만드는 데 제공하는 이름에 *location.cloudapp.azure.com*(여기서 location은 선택한 위치임)과 같은 기본 서브넷을 추가합니다. 자세한 내용은 [Azure 공용 IP 주소와 Azure DNS 사용](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)을 참조하세요.|

## <a name="view-or-delete-a-prefix"></a>접두사 보기 또는 삭제

1. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *공용 IP 주소 접두사*를 입력합니다. 검색 결과에 표시된 **공용 IP 주소 접두사**를 선택합니다.
2. 목록에서 보거나, 설정을 변경하거나, 삭제하려는 공용 IP 주소 접두사의 이름을 선택합니다.
3. 공용 IP 주소 접두사에 대해 수행하려는 작업(보기/삭제/변경)에 따라 다음 옵션 중 하나를 수행합니다.
    - **보기**: **개요** 섹션에서는 공용 IP 주소 접두사의 주요 설정을 보여줍니다(예: 접두사).
    - **삭제**: 공용 IP 주소 접두사를 삭제하려면 **개요** 섹션에서 **삭제**를 선택합니다. 접두사 내 주소가 공용 IP 주소 리소스에 연결되어 있으면 공용 IP 주소 리소스를 먼저 삭제해야 합니다. [공용 IP 주소 삭제](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address)를 참조하세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list): 공용 IP 주소를 나열함, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show): 설정을 표시함, [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update): 업데이트함, [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete): 삭제함|
|PowerShell|[Get-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/get-azurermpublicipprefix): 공용 IP 주소 개체를 검색하고 해당 설정을 확인함, [Set-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/set-azurermpublicipprefix): 설정을 업데이트함, [Remove-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/remove-azurermpublicipprefix): 삭제함|

## <a name="permissions"></a>권한

공용 IP 주소 접두사에 대한 작업을 수행하려면 다음 표에 나열된 적절한 작업이 할당된 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할 또는 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 계정이 할당되어야 합니다.

| 조치                                                                   | Name                                                           |
| ---------                                                                | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | 공용 IP 주소 접두사 읽기                                |
| Microsoft.Network/publicIPPrefixes/write                          | 공용 IP 주소 접두사 만들기 또는 업데이트                    |
| Microsoft.Network/publicIPPrefixes/delete                         | 공용 IP 주소 접두사 삭제                              |
|Microsoft.Network/publicIPPrefixes/join/action | 접두사로 공용 IP 주소 만들기 |

## <a name="next-steps"></a>다음 단계

- [공용 IP 접두사](public-ip-address-prefix.md)를 사용하는 경우의 시나리오 및 이점에 대해 알아보기
