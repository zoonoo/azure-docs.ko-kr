---
title: Azure PowerShell 샘플 - 전체 가상 머신 확장 집합 만들기 | Microsoft Docs
description: Azure PowerShell 샘플
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 20bf851cc9b2965a355286699e1ef255887d7650
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697091"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>PowerShell을 사용하여 전체 가상 머신 확장 집합 만들기
이 스크립트는 Windows Server 2016을 실행하는 가상 머신 확장 집합을 만듭니다. [New-AzureRmVmss에서 여기에 사용 가능한 기본 제공 리소스 생성 옵션](powershell-sample-create-simple-scale-set.md)을 사용하는 대신 개별 리소스가 구성되고 생성됩니다. 스크립트를 실행한 후에는 RDP를 통해 VM 인스턴스에 액세스할 수 있습니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>배포 정리
다음 명령을 실행하여 리소스 그룹, 확장 집합 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 서브넷 구성을 만듭니다. 이 구성은 가상 네트워크 만들기 프로세스에서 사용됩니다. |
| [새-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 가상 네트워크를 만듭니다. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 공용 IP 주소를 만듭니다. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | 부하 분산 장치에 대한 프런트 엔드 IP 구성을 만듭니다. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | 부하 분산 장치에 대한 백 엔드 주소 풀 구성을 만듭니다. |
| [New-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | 부하 분산 장치에 대한 인바운드 NAT 규칙 구성을 만듭니다. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | 부하 분산 장치를 만듭니다. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | 부하 분산 장치에 대한 프로브 구성을 만듭니다. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | 부하 분산 장치에 대한 규칙 구성을 만듭니다. |
| [Set-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Set-AzureRmLoadBalancer) | 제공된 정보로 부하 분산 장치를 업데이트합니다. |
| [New-AzureRmVmssIpConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssIpConfig) | 확장 집합 VM 인스턴스에 대한 IP 구성을 만듭니다. VM 인스턴스는 부하 분산 장치 백 엔드 풀, NAT 풀 및 가상 네트워크 서브넷에 연결됩니다. |
| [New-AzureRmVmssConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssConfig) | 확장 집합 구성을 만듭니다. 이 구성에는 만들 VM 인스턴스 수, VM SKU(크기) 및 업그레이드 정책 모드와 같은 정보가 포함됩니다. 구성은 추가 cmdlet에 의해 추가되고, 확장 집합 생성 중에 사용됩니다. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | VM 인스턴스에 사용할 이미지를 정의하고 확장 집합 구성에 추가합니다. |
| [Set-AzureRmVmssOsProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | 관리 사용자 이름과 암호 자격 증명 및 VM 이름 지정 접두사를 정의합니다. 확장 집합 구성에 이러한 값을 추가합니다. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/AzureRM.Compute/Add-AzureRmVmssNetworkInterfaceConfiguration) | IP 구성에 따라 가상 네트워크 인터페이스를 VM 인스턴스에 추가합니다. 확장 집합 구성에 이러한 값을 추가합니다. |
| [New-AzureRmVmss](/powershell/module/AzureRM.Compute/New-AzureRmVmss) | 확장 집합 구성에서 제공된 정보에 따라 확장 집합을 만듭니다. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계
Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 확장 집합 PowerShell 스크립트 샘플은 [Azure 가상 머신 확장 집합 설명서](../powershell-samples.md)에서 찾을 수 있습니다.
