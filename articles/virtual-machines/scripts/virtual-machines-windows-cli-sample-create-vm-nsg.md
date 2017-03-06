---
title: "Azure CLI 스크립트 샘플 - 내부 및 외부 NSG를 사용하여 두 개의 VM 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 내부 및 외부 NSG를 사용하여 두 개의 VM 만들기"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 9ef17544582e944fda2ab8ea8b8098bb83e2ab57
ms.lasthandoff: 03/01/2017

---

# <a name="secure-network-traffic-between-virtual-machines"></a>가상 컴퓨터 간의 네트워크 트래픽 보안

이 스크립트는 두 개의 가상 컴퓨터를 만들고 해당 컴퓨터에 들어오는 트래픽의 보안을 유지합니다. 첫 번째 가상 컴퓨터는 인터넷에 액세스할 수 있고 포트 3389 및 포트 80에서 트래픽을 허용하도록 네트워크 보안 그룹(NSG)을 구성합니다. 두 번째 가상 컴퓨터는 인터넷에 액세스할 수 없고 NSG가 첫 번째 가상 컴퓨터의 트래픽을 허용하도록 구성합니다. 

이 스크립트를 실행하기 전에 `az login` 명령을 사용하여 Azure와의 연결이 생성되었는지 확인합니다. 또한 스크립트의 시작 부분에 있는 $AdminPassword 변수를 고유하고 적절한 암호 복잡성 요구 사항에 맞게 변경해야 합니다.

이 샘플은 Bash 셸에서 작동합니다. Windows에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="create-vm-sample-with-a-network-security-group"></a>네트워크 보안 그룹을 사용하여 VM 샘플 만들기

[!code-azurecli[기본](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "NSG를 사용하여 VM 만들기")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 컴퓨터 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Azure Virtual Network 및 서브넷을 만듭니다. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) | 서브넷을 만듭니다. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 가상 컴퓨터를 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 NSG에 연결합니다. 또한 이 명령은 사용할 가상 컴퓨터 이미지와 관리 자격 증명을 지정합니다.  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#update) | NSG 규칙을 업데이트합니다. 이 샘플에서 백 엔드 규칙은 프런트 엔드 서브넷의 트래픽을 통과하도록 업데이트됩니다. |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#list) | 네트워크 보안 그룹 규칙에 대한 정보를 반환합니다. 이 샘플에서 규칙 이름은 스크립트에서 나중에 사용하기 위해 변수에 저장됩니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 가상 컴퓨터 CLI 스크립트 샘플은 [Azure Windows VM 설명서](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.

