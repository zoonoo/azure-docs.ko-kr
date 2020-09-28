---
title: Network Watcher 확장을 최신 버전으로 업데이트 합니다.
description: Network Watcher 확장을 최신 버전으로 업데이트 하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410343"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Network Watcher 확장을 최신 버전으로 업데이트 하는 방법 

## <a name="overview"></a>개요

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)는 Azure 네트워크에 대한 모니터링을 허용하는 네트워크 성능 모니터링, 진단 및 분석 서비스입니다. Network Watcher 에이전트 가상 머신 확장은 Azure 가상 머신에서 요청 시 네트워크 트래픽 캡처 및 기타 고급 기능을 위한 요구 사항입니다. Network Watcher 확장은 연결 모니터, 연결 모니터 (미리 보기), 연결 문제 해결 및 패킷 캡처와 같은 기능에서 사용 됩니다.   

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 가상 머신에 Network Watcher 확장 프로그램이 설치 되어 있는 것으로 가정 하 고 최신 버전으로 업데이트 하는 지침을 제공 합니다. 

## <a name="latest-version"></a>최신 버전
Network Watcher 확장의 최신 버전은 현재 `1.4.1654.1` 입니다.

## <a name="updating-your-extension"></a>확장 업데이트 

### <a name="check-your-extension-version"></a>확장 버전 확인  

**Azure Portal 사용**

1. Azure Portal VM의 ' 확장 ' 블레이드로 이동 합니다.   
2. ' AzureNetworkWatcher ' 확장을 클릭 하 여 세부 정보 창을 표시 합니다.  
3. ' 버전 ' 필드에서 버전 번호를 찾습니다.  

**Azure CLI 사용** Azure CLI 프롬프트에서 아래 명령을 실행 합니다.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

출력에서 AzureNetworkWatcher 확장을 찾아 출력의 "TypeHandlerVersion" 필드에서 버전 번호를 확인 합니다.  


**PowerShell 사용** PowerShell 프롬프트에서 다음 명령을 실행 합니다.   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

출력에서 AzureNetworkWatcher 확장을 찾아 출력의 "TypeHandlerVersion" 필드에서 버전 번호를 확인 합니다.   


### <a name="update-your-extension"></a>확장 업데이트

버전이 `1.4.1654.1` (최신 버전) 보다 낮은 경우 다음 옵션 중 하나를 사용 하 여 확장을 업데이트 합니다. 

**옵션 1: PowerShell 사용**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**옵션 2: Azure CLI 사용**  

강제 업그레이드 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

작동 하지 않는 경우입니다. 아래 단계를 사용 하 여 확장을 제거 하 고 다시 설치 합니다. 그러면 최신 버전이 자동으로 추가 됩니다. 

확장 제거 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

확장을 다시 설치 하는 중

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**옵션 3: Vm 다시 부팅**

NetworkWatcher 확장에 대해 자동 업그레이드가 true로 설정 된 경우 VM을 다시 부팅 하면 최신 확장이 설치 됩니다.


## <a name="support"></a>지원

이 문서의 어느 시점에서 든 도움이 필요한 경우 Network Watcher 확장 설명서 ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux), [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows))를 참조 하거나 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에 게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
