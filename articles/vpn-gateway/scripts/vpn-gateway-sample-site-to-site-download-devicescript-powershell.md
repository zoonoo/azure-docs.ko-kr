---
title: Azure PowerShell 스크립트 샘플 - 디바이스 구성 템플릿 다운로드 | Microsoft Docs
description: 디바이스 구성 템플릿을 다운로드합니다.
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: 53f7adcfbaaf84f50a6b724ef143b833a5f9bf78
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887132"
---
# <a name="download-vpn-device-template-using-powershell"></a>PowerShell을 사용한 VPN 디바이스 템플릿 다운로드

이 스크립트는 연결을 위해 VPN 디바이스 템플릿을 다운로드합니다.


```azurepowershell-interactive
# Declare variables
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration template for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG `
-DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="clean-up-resources"></a>리소스 정리

만든 리소스가 더 이상 필요하지 않으면 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹을 삭제합니다. 그러면 리소스 그룹 및 포함된 모든 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice) | 사용 가능한 모든 VPN 디바이스 모델 및 버전을 나열합니다. |
| [Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript) | 연결에 대한 구성 템플릿을 다운로드합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.
