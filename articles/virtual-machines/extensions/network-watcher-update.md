---
title: Network Watcher 확장을 최신 버전으로 업데이트 합니다.
description: Azure Network Watcher 확장을 최신 버전으로 업데이트 하는 방법을 알아봅니다.
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
ms.openlocfilehash: e367c348364d03cec6914c99e7ff112803fc58f6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132434"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Network Watcher 확장을 최신 버전으로 업데이트 합니다.

## <a name="overview"></a>개요

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) 는 azure 네트워크를 모니터링 하는 네트워크 성능 모니터링, 진단 및 분석 서비스입니다. Network Watcher 에이전트 VM (가상 컴퓨터) 확장은 요청 시 네트워크 트래픽을 캡처하고 Azure Vm에서 다른 고급 기능을 사용 하기 위한 요구 사항입니다. Network Watcher 확장은 연결 모니터, 연결 모니터 (미리 보기), 연결 문제 해결 및 패킷 캡처와 같은 기능에서 사용 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 VM에 Network Watcher 확장 프로그램이 설치 되어 있다고 가정 합니다.

## <a name="latest-version"></a>최신 버전

Network Watcher 확장의 최신 버전은 현재 `1.4.1654.1` 입니다.

## <a name="update-your-extension"></a>확장 업데이트

확장을 업데이트 하려면 확장 버전을 알아야 합니다.

### <a name="check-your-extension-version"></a>확장 버전 확인

Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여 확장 버전을 확인할 수 있습니다.

#### <a name="usetheazureportal"></a>Azure Portal 사용

1. Azure Portal에서 VM의 **확장** 창으로 이동 합니다.
1. **AzureNetworkWatcher** 확장을 선택 하 여 세부 정보 창을 표시 합니다.
1. **버전** 필드에서 버전 번호를 찾습니다.  

#### <a name="use-the-azure-cli"></a>Azure CLI 사용

Azure CLI 프롬프트에서 다음 명령을 실행 합니다.

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

출력에서 AzureNetworkWatcher 확장을 찾습니다. 출력에서 "TypeHandlerVersion" 필드의 버전 번호를 확인 합니다.  

#### <a name="usepowershell"></a>PowerShell 사용

PowerShell 프롬프트에서 다음 명령을 실행 합니다.

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

출력에서 AzureNetworkWatcher 확장을 찾습니다. 출력에서 "TypeHandlerVersion" 필드의 버전 번호를 확인 합니다.

### <a name="update-your-extension"></a>확장 업데이트

버전이 최신 버전인 보다 이전인 경우 `1.4.1654.1` 다음 옵션 중 하나를 사용 하 여 확장을 업데이트 합니다.

#### <a name="option-1-use-powershell"></a>옵션 1: PowerShell 사용

다음 명령을 실행 합니다.

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```

#### <a name="option-2-use-the-azure-cli"></a>옵션 2: Azure CLI 사용

강제로 업그레이드 합니다.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

그래도 해결 되지 않으면 확장을 제거 하 고 다시 설치 하 고 다음 단계를 수행 하 여 최신 버전을 자동으로 추가 합니다.

확장을 제거 합니다.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

확장을 다시 설치 합니다.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

#### <a name="option-3-reboot-your-vms"></a>옵션 3: Vm 다시 부팅

Network Watcher 확장에 대해 자동 업그레이드를 true로 설정한 경우 VM 설치를 최신 확장으로 다시 부팅 합니다.

## <a name="support"></a>지원

이 문서의 어느 시점에서 든 도움이 필요한 경우 [Linux](./network-watcher-linux.md) 또는 [Windows](./network-watcher-windows.md)에 대 한 Network Watcher 확장 설명서를 참조 하세요. [MSDN azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 azure 전문가에 게 문의할 수도 있습니다. 또는 Azure 지원 인시던트를 파일에 제공 합니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동 하 여 **지원 받기**를 선택 합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.