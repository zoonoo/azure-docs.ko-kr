---
title: Azure PowerShell 샘플- 단일 영역 확장 집합 | Microsoft Docs
description: Azure PowerShell 샘플
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e4f6d26c119a113bab0220828c847f438e7bc7a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>PowerShell을 사용하여 단일 영역 가상 머신 확장 집합 만들기
이 스크립트는 단일 가용성 영역에서 Windows Server 2016을 실행하는 가상 머신 확장 집합을 만듭니다. 스크립트를 실행하면 RDP를 통해 가상 머신에 액세스할 수 있습니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>배포 정리
다음 명령을 실행하여 리소스 그룹, 확장 집합 및 관련된 모든 리소스를 제거합니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 가상 네트워크 서브넷을 정의하는 구성 개체를 만듭니다. |
| [새-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 가상 네트워크 및 서브넷을 만듭니다. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 고정 공용 IP 주소를 만듭니다. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | 공용 IP 주소를 포함하여 부하 분산 장치 프런트 엔드를 정의하는 구성 개체를 만듭니다. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | 부하 분산 장치 백 엔드를 정의하는 구성 개체를 만듭니다. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | 프런트 엔드 및 백 엔드 구성 개체를 기반으로 하는 부하 분산 장치를 만듭니다. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | 80 TCP 포트에서 트래픽을 모니터링하는 부하 분산 장치 상태 프로브를 만듭니다. 두 번의 실패가 15초 간격으로 연속적으로 발생하면 엔드포인트가 비정상 상태로 간주됩니다. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | 80 TCP 포트에서 트래픽을 프런트 엔드 풀에서 백 엔드 풀로 분산하는 부하 분산 장치 규칙을 정의하는 구성 개체를 만듭니다. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | 상태 프로브 및 부하 분산 장치 규칙을 사용하여 부하 분산 장치를 업데이트합니다. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | 80 TCP 포트에서 트래픽을 허용하는 네트워크 보안 그룹 규칙을 정의하는 구성 개체를 만듭니다. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | 네트워크 보안 그룹 및 규칙을 만듭니다. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | 네트워크 보안 그룹과 연결될 가상 네트워크 서브넷을 업데이트합니다. 서브넷에 연결된 모든 VM 인스턴스는 네트워크 보안 그룹 규칙을 상속합니다. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | 서브넷 및 네트워크 보안 그룹 구성을 사용하여 가상 네트워크를 업데이트합니다. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | VM 인스턴스를 부하 분산 장치 백 엔드 풀 및 인바운드 NAT 풀에 연결하기 위한 가상 머신 확장 집합 IP 주소 정보를 정의하는 구성 개체를 만듭니다.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | 확장 집합, VM 인스턴스 크기 및 업그레이드 정책 모드의 VM 인스턴스 수를 정의하는 구성 개체를 만듭니다. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | VM 인스턴스에 사용할 VM 이미지를 정의하는 구성 개체를 만듭니다. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | VM 인스턴스 이름과 사용자 자격 증명을 정의하는 구성 프로필을 만듭니다. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | 각 VM 인스턴스에 대한 가상 네트워크 인터페이스 카드를 정의하는 구성 개체를 만듭니다. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | 모든 구성 개체를 결합하여 가상 머신 확장 집합을 만듭니다. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | 가상 머신 확장 집합에 대한 정보를 가져옵니다. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | 사용자 지정 스크립트용 VM 확장을 추가하여 기본 웹 응용 프로그램을 설치합니다. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | VM 확장을 적용하기 위해 가상 머신 확장 집합 모델을 업데이트합니다. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | 부하 분산 장치에서 사용하는 할당된 공용 IP 주소에 대한 정보를 가져옵니다. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계
Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 확장 집합 PowerShell 스크립트 샘플은 [Azure 가상 머신 확장 집합 설명서](../powershell-samples.md)에서 찾을 수 있습니다.