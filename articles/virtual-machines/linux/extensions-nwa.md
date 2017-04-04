---
title: "Linux용 Azure Network Watcher 에이전트 가상 컴퓨터 확장 | Microsoft Docs"
description: "가상 컴퓨터 확장을 사용하여 Linux 가상 컴퓨터에 Network Watcher를 배포합니다."
services: virtual-machines-linux
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
translationtype: Human Translation
ms.sourcegitcommit: 2de3e6d205dd126e1c3d9606ea1419669056d7f1
ms.openlocfilehash: eaadd531b9e05a54446e61f98584ae9d75470a5f
ms.lasthandoff: 02/22/2017


---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Linux용 Network Watcher 에이전트 가상 컴퓨터 확장

## <a name="overview"></a>개요

[Azure Network Watcher](https://review.docs.microsoft.com/en-us/azure/network-watcher/)는 Azure 네트워크에 대한 모니터링을 허용하는 네트워크 성능 모니터링, 진단 및 분석 서비스입니다. Azure Virtual Machines의 Network Watcher 기능 중 일부에는 Azure Network Watcher 에이전트 가상 컴퓨터 확장이 필요합니다. 주문형 네트워크 트래픽을 캡처하는 기능 및 기타 고급 기능이 포함됩니다.

이 문서에서는 Linux용 Network Watcher 에이전트 가상 컴퓨터 확장에 대해 지원되는 플랫폼 및 배포 옵션을 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

Network Watcher 에이전트 확장은 다음 Linux 배포판에 대해 실행될 수 있습니다.

| 배포 | 버전 |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS 및 12.04 LTS |
| Debian | 7 및 8 |
| RedHat | 6.x 및 7.x |
| Oracle Linux | 7x |
| Suse | 11 및 12 |
| OpenSuse | 7.0 |
| CentOS | 7.0 |

CoreOS는 현재 지원되지 않습니다.

### <a name="internet-connectivity"></a>인터넷 연결

일부 Network Watcher 에이전트 기능에서는 대상 가상 컴퓨터를 인터넷에 연결해야 합니다. 일부 Network Watcher 에이전트 기능에 나가는 연결을 설정하는 기능이 없는 경우 오작동하거나 사용할 수 없게 됩니다. 자세한 내용은 [Network Watcher 설명서](https://review.docs.microsoft.com/en-us/azure/network-watcher/)를 참조하세요.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Network Watcher 에이전트 확장에 대한 스키마를 보여 줍니다. 현재 확장은 사용자 제공 설정을 필요로 하거나 지원하지 않고 기본 구성을 사용합니다.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명한 JSON 스키마를 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 Network Watcher 에이전트 확장을 실행할 수 있습니다.

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 Network Watcher 에이전트 VM 확장을 기존 가상 컴퓨터에 배포할 수 있습니다.

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>문제 해결 및 지원

### <a name="troubleshooting"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 Network Watcher 설명서를 참조하거나 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/en-us/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/en-us/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/en-us/support/faq/)를 참조하세요.

