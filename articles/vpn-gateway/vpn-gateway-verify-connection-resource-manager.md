---
title: 'Azure VPN Gateway: 게이트웨이 연결 확인'
description: 이 문서에서는 가상 네트워크 VPN Gateway 연결을 확인하는 방법을 보여 줍니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: 98b7923728616473186eeeeb2a2f03bfece9fae1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398548"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway 연결 확인

이 문서에서는 클래식 및 Resource Manager 배포 모델에 대한 VPN Gateway 연결을 확인하는 방법을 보여 줍니다.

## <a name="azure-portal"></a>Azure portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

PowerShell을 사용하여 Resource Manager 배포 모델에 대한 VPN Gateway 연결을 확인하려면 최신 버전의 [Azure Resource Manager PowerShell cmdlet](/powershell/azure/)을 설치합니다.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 Resource Manager 배포 모델에 대한 VPN Gateway 연결을 확인하려면 최신 버전의 [CLI 명령](https://docs.microsoft.com/cli/azure/install-azure-cli)(2.0 이상)을 설치합니다.

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Azure Portal(클래식)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell(클래식)

PowerShell을 사용하여 클래식 배포 모델에 대한 VPN Gateway 연결을 확인하려면 최신 버전의 Azure PowerShell cmdlet을 설치합니다. [Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) 모듈을 다운로드하여 설치해야 합니다. 'Add-AzureAccount'를 사용하여 클래식 배포 모델에 로그인합니다.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/windows/quick-create-portal.md) 를 참조하세요.
