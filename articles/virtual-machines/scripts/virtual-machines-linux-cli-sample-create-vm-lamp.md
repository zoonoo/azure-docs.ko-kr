---
title: "Azure CLI 스크립트 샘플 - 부하가 분산된 가상 컴퓨터 확장 집합에서 LAMP 스택 배포 | Microsoft Docs"
description: "사용자 지정 스크립트 확장을 사용하여 Azure의 부하가 분산된 가상 컴퓨터 확장 집합에서 LAMP 스택을 배포합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 23170923d7c05c9b7230cf331725250b2a3c0f09
ms.contentlocale: ko-kr
ms.lasthandoff: 09/09/2017

---

# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>부하가 분산된 가상 컴퓨터 확장 집합에서 LAMP 스택 배포

이 예제에서는 가상 컴퓨터 확장 집합을 만들고 사용자 지정 스크립트를 실행하는 확장을 적용하여 LAMP 스택을 확장 집합의 각 가상 컴퓨터에 배포합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[주](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "LAMP 스택을 사용하여 가상 컴퓨터 확장 집합 만들기")]

## <a name="connect"></a>연결

이 코드를 사용하여 VM 및 확장 집합에 연결하는 방법을 확인합니다.

[!code-azurecli[주](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "가상 컴퓨터 확장 집합에 액세스")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 확장 집합, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 컴퓨터, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | 가상 컴퓨터 확장 집합을 만듭니다. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | 부하가 분산된 끝점을 추가합니다. |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#az_vmss_extension_set) | VM 배포에서 사용자 지정 스크립트를 실행하는 확장을 만듭니다. |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#az_vmss_update_instances) | 확장이 확장 집합에 적용되기 전에 배포된 VM 인스턴스에서 사용자 지정 스크립트를 실행합니다. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) | 더 많은 VM 인스턴스를 추가하여 확장 집합의 규모를 확장합니다. VM 인스턴스가 배포되면 사용자 지정 스크립트가 해당 VM 인스턴스에서 실행됩니다. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list) | 샘플에서 만든 VM의 IP 주소를 가져옵니다. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_show) | 부하 분산 장치에서 사용하는 프런트 엔드 포트와 백 엔드 포트를 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 가상 컴퓨터 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.

