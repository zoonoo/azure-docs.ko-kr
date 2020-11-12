---
title: Azure CLI 스크립트 샘플 - 웹 트래픽 관리 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 애플리케이션 게이트웨이 및 가상 머신 확장 집합을 사용하여 웹 트래픽을 관리합니다.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 81c936acc00801f8473c1ec74aa50dbf2abb4936
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397028"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Azure CLI를 사용하여 웹 트래픽 관리

이 스크립트는 백 엔드 서버에 가상 머신 확장 집합을 사용하는 애플리케이션 게이트웨이를 만듭니다. 그런 다음, 웹 트래픽을 관리하도록 애플리케이션 게이트웨이를 구성할 수 있습니다. 스크립트를 실행한 후에는 공용 IP 주소를 사용하여 애플리케이션 게이트웨이를 테스트할 수 있습니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](/cli/azure/network/vnet) | 가상 네트워크를 만듭니다. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | 가상 네트워크에 서브넷을 만듭니다. |
| [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest) | 애플리케이션 게이트웨이의 공용 IP 주소를 만듭니다. |
| [az network application-gateway create](/cli/azure/network/application-gateway?view=azure-cli-latest) | 애플리케이션 게이트웨이를 만듭니다. |
| [az vmss create](/cli/azure/vmss) | 가상 머신 확장 집합을 만듭니다. |
| [az network public-ip show](/cli/azure/network/public-ip) | 애플리케이션 게이트웨이의 공용 IP 주소를 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure/overview)를 참조하세요.

추가 애플리케이션 게이트웨이 CLI 스크립트 샘플은 [Azure Windows VM 설명서](../cli-samples.md)에서 찾을 수 있습니다.