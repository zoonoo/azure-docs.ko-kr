---
title: Azure PowerShell 스크립트 샘플 - VM 네트워크 트래픽 필터링 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 인바운드 및 아웃바운드 VM 네트워크 트래픽을 필터링합니다.
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 168d374a070160eff158d6464d6e214880f486f0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156898"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>인바운드 및 아웃바운드 VM 네트워크 트래픽 필터링

이 스크립트 샘플은 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 인바운드 네트워크 트래픽은 HTTP 및 HTTPS로 제한되지만, 백 엔드 서브넷에서 인터넷으로의 아웃바운드 트래픽은 허용되지 않습니다. 스크립트를 실행한 후에는 두 개의 NIC가 있는 하나의 가상 머신이 있게 됩니다. 각 NIC는 서로 다른 서브넷에 연결됩니다.

필요한 경우 [Azure PowerShell 가이드](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/filter-network-traffic/filter-network-traffic.ps1  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 네트워크 및 네트워크 보안 그룹을 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 서브넷 구성 개체를 만듭니다. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Azure 가상 네트워크 및 프런트 엔드 서브넷을 만듭니다. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | 네트워크 보안 그룹에 할당될 보안 규칙을 만듭니다. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |특정 포트를 특정 서브넷에 허용하거나 차단하는 NSG 규칙을 만듭니다. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | NSG를 서브넷에 연결합니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 인터넷에서 VM에 액세스하기 위한 공용 IP 주소를 만듭니다. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 가상 네트워크 인터페이스를 만들고 가상 네트워크의 프런트 엔드 및 백 엔드 서브넷에 연결합니다. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | VM 구성을 만듭니다. 이 구성은 VM 이름, 운영 체제 및 관리자 자격 증명 등의 정보를 포함합니다. 이 구성은 VM을 만드는 중에 사용됩니다. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 가상 머신을 만듭니다. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azure/overview)를 참조하세요.

추가 네트워킹 PowerShell 스크립트 샘플은 [Azure 네트워킹 개요 설명서](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)에서 찾을 수 있습니다.
