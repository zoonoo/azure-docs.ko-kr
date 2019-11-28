---
title: 도메인 이름에 대한 DNS 영역 및 레코드 만들기 - Azure CLI - Azure DNS
description: Azure CLI 스크립트 예제에서는 도메인 이름에 대한 DNS 영역 및 레코드를 만드는 방법을 보여줍니다.
services: dns
author: asudbring
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: 8f5dde0a35f31d2c33ab2857659efc88339c2ead
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210391"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI 스크립트 예제: DNS 영역 및 레코드 만들기

이 Azure CLI 스크립트 예제에서는 도메인 이름에 대한 DNS 영역 및 레코드를 만듭니다. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, DNS 영역 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Azure DNS 영역을 만듭니다. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | *A* 레코드를 DNS 영역에 추가합니다. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | DNS 영역에서 모든*A* 레코드 집합을 나열합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

