---
title: Azure PowerShell 스크립트 샘플 - Azure Firewall 테스트 환경 만들기
description: Azure PowerShell 스크립트 샘플 - Azure Firewall 테스트 환경을 만듭니다.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: ffc9d3c15f045079585ea2aeceab278cf0349041
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991911"
---
# <a name="create-an-azure-firewall-test-environment"></a>Azure 방화벽 테스트 환경 만들기

[!INCLUDE [firewall-preview-notice](../../../includes/firewall-preview-notice.md)]

Azure Firewall 문서의 예제에서는 이미 Azure Firewall 공개 미리 보기를 사용하도록 설정했다고 가정합니다. 자세한 내용은 [Azure Firewall 공개 미리 보기 사용하도록 설정](../public-preview.md)을 참조합니다.

이 스크립트 샘플은 방화벽 및 테스트 네트워크 환경을 만듭니다. 네트워크에는 *AzureFirewallSubnet*, *ServersSubnet* 및 *JumpboxSubnet* 등 세 개의 서브넷이 포함된 하나의 VNet이 있습니다. ServersSubnet 및 JumpboxSubnet 각각에는 2코어 Windows Server 하나씩 있습니다.

방화벽은 AzureFirewallSubnet에 있으며, www.microsoft.com에 대한 액세스를 허용하는 단일 규칙이 포함된 응용 프로그램 규칙 컬렉션으로 구성됩니다.

방화벽 규칙이 적용된 방화벽을 통해 ServersSubnet에서 네트워크 트래픽을 가리키는 사용자 정의 경로가 만들어집니다.

Azure [Cloud Shell](https://shell.azure.com/powershell) 또는 로컬 PowerShell 설치에서 스크립트를 실행할 수 있습니다. 

PowerShell을 로컬로 실행하는 경우 이 스크립트에는 AzureRM PowerShell 모듈 버전 6.4.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 

업그레이드해야 할 경우 Windows 10 및 Windows Server 2016에 빌드되는 `PowerShellGet`을 사용할 수 있습니다.

> [!NOTE]
>다른 Windows 버전은 `PowerShellGet`을 설치해야 사용할 수 있습니다. `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path`를 실행하여 시스템에 설치됐는지 확인할 수 있습니다. 출력이 빈 경우 최신 [Windows 관리 프레임워크](https://www.microsoft.com/download/details.aspx?id=54616)를 설치해야 합니다.

자세한 내용은 [PowerShellGet으로 Windows에 Azure PowerShell 설치](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0) 참조

웹 플랫폼 설치 관리자를 사용하여 모든 기존 Azure PowerShell 설치를 완료한 경우 PowerShellGet 설치와 충돌을 일으키므로 제거해야 합니다.

또한 AzureRM.Network(버전 6.4.0) 미리 보기 버전을 설치해야 합니다. 이전 모듈이 있는 경우 제거하려면 `Uninstall-Module AzureRM.Network -Force`을 실행합니다. 그런 후 다음을 실행합니다.

 `Install-Module -Name AzureRM.Network -Repository PSGallery -RequiredVersion 6.4.0-preview -AllowPrerelease -Force`

버전 6.4.0을 설치합니다.

또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 네트워크 및 네트워크 보안 그룹을 만듭니다. 다음 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 서브넷 구성 개체를 만듭니다. |
| [새-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Azure 가상 네트워크 및 프런트 엔드 서브넷을 만듭니다. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | 네트워크 보안 그룹에 할당될 보안 규칙을 만듭니다. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |특정 포트를 특정 서브넷에 허용하거나 차단하는 NSG 규칙을 만듭니다. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | NSG를 서브넷에 연결합니다. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 인터넷에서 VM에 액세스하기 위한 공용 IP 주소를 만듭니다. |
| [새-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | 가상 네트워크 인터페이스를 만들고 가상 네트워크의 프런트 엔드 및 백 엔드 서브넷에 연결합니다. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | VM 구성을 만듭니다. 이 구성은 VM 이름, 운영 체제 및 관리자 자격 증명 등의 정보를 포함합니다. 이 구성은 VM을 만드는 중에 사용됩니다. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 가상 머신을 만듭니다. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

