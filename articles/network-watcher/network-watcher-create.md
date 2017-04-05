---
title: "Azure Network Watcher 인스턴스 만들기 | Microsoft Docs"
description: "이 페이지는 포털 및 Azure REST API를 사용하여 Network Watcher의 인스턴스를 만드는 단계를 제공합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: a39ce143796408f8e44b0d2c877e631e92473462
ms.lasthandoff: 03/28/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher 인스턴스 만들기

Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="register-the-preview-capability"></a>미리 보기 기능 등록

Network Watcher는 현재 미리 보기에서 제공됩니다. Network Watcher의 기능을 사용하려면 기능을 등록해야 합니다. 이 작업을 수행하려면 다음 PowerShell 샘플을 실행합니다.

```powershell
Register-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

등록이 성공했는지 확인하려면 다음 Powershell 샘플을 실행합니다.

```powershell
Get-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace  Microsoft.Network
```

기능이 올바르게 등록된 경우 출력은 다음과 일치해야 합니다.

```
FeatureName         ProviderName      RegistrationState
-----------         ------------      -----------------
AllowNetworkWatcher Microsoft.Network Registered
```

### <a name="instructions-for-cli-10"></a>CLI 1.0에 대한 지침

등록하려면

```CLI
azure feature register  Microsoft.Network AllowNetworkWatcher
azure provider register Microsoft.Network
```

등록이 성공했는지 확인하려면 다음 CLI 명령을 실행합니다.

```CLI
azure feature show Microsoft.Network AllowNetworkWatcher
```

기능이 올바르게 등록된 경우 출력은 다음과 일치해야 합니다.

```CLI
info:    Executing command feature show
data:    Feature Name:       AllowNetworkWatcher
data:    Provider Name:      Microsoft.Network
data:    Registration State: Registered
info:    feature show command OK
```

> [!NOTE]
> Network Watcher는 현재 지원 CLI 1.0만 지원하므로 새 Network Watcher 인스턴스 생성에 관한 지침은 CLI 1.0용으로 제공됩니다. CLI 2.0을 사용하여 공급자를 등록하려면 `az feature register -n AllowNetworkWatcher --namespace Microsoft.Network`를 사용합니다.

## <a name="create-a-network-watcher-in-the-portal"></a>포털에서 Network Watcher 만들기

**더 많은 서비스** > **네트워킹** > **Network Watcher**로 이동합니다. Network Watcher에 대해 사용하도록 설정하려는 모든 구독을 선택할 수 있습니다. 이 작업은 사용할 수 있는 모든 지역에서 Network Watcher를 만듭니다.

![Network Watcher 만들기][1]

## <a name="create-a-network-watcher-with-powershell"></a>PowerShell을 사용하여 Network Watcher 만들기

Network Watcher의 인스턴스를 만들려면 다음 예제를 실행합니다.

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>REST API를 사용하여 Network Watcher 만들기

PowerShell을 사용하여 REST API를 호출하는 데 ARMclient가 사용됩니다. ARMClient는 [Chocolatey의 ARMClient](https://chocolatey.org/packages/ARMClient)에서 chocolatey에 있습니다.

### <a name="log-in-with-armclient"></a>ARMClient로 로그인

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Network Watcher 만들기

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>다음 단계

이제 Network Watcher의 인스턴스가 있으니 제공되는 기능에 대해 알아봅니다.

* [토폴로지](network-watcher-topology-overview.md)
* [패킷 캡처](network-watcher-packet-capture-overview.md)
* [IP 흐름 확인](network-watcher-ip-flow-verify-overview.md)
* [다음 홉](network-watcher-next-hop-overview.md)
* [보안 그룹 보기](network-watcher-security-group-view-overview.md)
* [NSG 흐름 로깅](network-watcher-nsg-flow-logging-overview.md)
* [Virtual Network 게이트웨이 문제 해결](network-watcher-troubleshoot-overview.md)

Network Watcher 인스턴스를 만든 후 다음 문서 [경고 트리거 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 따라 패키지 캡처를 구성할 수 있습니다.

[1]: ./media/network-watcher-create/figure1.png












