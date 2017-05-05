---
title: "VPN Gateway 연결 확인 | Microsoft Docs"
description: "이 문서에서는 가상 네트워크 VPN Gateway 연결을 확인하는 방법을 보여 줍니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a67ea630cc8b5a3e9deab7733aa5cd2055949ec0
ms.lasthandoff: 04/27/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway 연결 확인

이 문서에서는 Resource Manager 및 클래식 배포 모델에 대한 VPN Gateway 연결을 확인하는 방법을 보여 줍니다.

## <a name="verify-using-the-azure-portal"></a>Azure Portal을 사용하여 확인

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>PowerShell을 사용하여 확인

PowerShell을 사용하여 확인하려면 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview) 을 참조하세요. Resource Manager cmdlet을 사용하는 방법에 대한 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### <a name="log-in-to-your-azure-account"></a>Azure 계정에 로그인
1. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다.

  ```powershell
  Login-AzureRmAccount
  ```
2. 계정에 대한 구독을 확인합니다.

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. 사용할 구독을 지정합니다.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>연결 확인

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>Azure CLI를 사용하여 확인

Azure CLI를 사용하여 확인하려면 CLI 명령의 최신 버전(2.0 이상)을 설치합니다. CLI 명령을 설치하는 방법에 대한 자세한 내용은 [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)(Azure CLI 2.0 설치)를 참조하세요.

### <a name="log-in-to-your-azure-account"></a>Azure 계정에 로그인합니다.

1. [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

  ```azurecli
  az login
  ```
2. Azure 구독이 두 개 이상인 경우 계정의 구독을 나열합니다.

  ```azurecli
  Az account list --all
  ```
3. 사용할 구독을 지정합니다.

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>연결 확인

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Azure Portal을 사용하여 확인(클래식)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>PowerShell을 사용하여 확인(클래식)
PowerShell을 사용하여 확인하려면 최신 버전의 Azure PowerShell cmdlet을 설치합니다. Resource Manage 및 SM(서비스 관리) 버전 둘 다를 다운로드하고 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview) 을 참조하세요. 

### <a name="log-in-to-your-azure-account"></a>Azure 계정에 로그인
1. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다.

  ```powershell
  Login-AzureRmAccount
  ```
2. 계정에 대한 구독을 확인합니다.

  ```powershell
  Get-AzureRmSubscription 
  ```
3. 사용할 구독을 지정합니다.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. 로그인하여 클래식 배포 모델에 대한 서비스 관리 cmdlet을 사용합니다.

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>연결 확인
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>다음 단계
* 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 참조하세요.


