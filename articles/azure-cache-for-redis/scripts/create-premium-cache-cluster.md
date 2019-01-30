---
title: Azure CLI 스크립트 샘플 - 클러스터링이 있는 프리미엄 Azure Cache for Redis 만들기 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 클러스터링이 있는 프리미엄 계층 Azure Cache for Redis를 만듭니다.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: abd69e27cf95a882ae7c299cbe19e2c30c35a918
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53096828"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>클러스터링이 있는 프리미엄 Azure Cache for Redis 만들기

이 시나리오에서는 사용하도록 설정된 클러스터링 및 두 개의 분할이 있는 6GB 프리미엄 계층 Azure Cache for Redis를 만드는 방법을 알아봅니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹 및 사용하도록 설정된 클러스터링이 있는 프리미엄 계층 Azure Cache for Redis를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Azure Cache for Redis 인스턴스를 만듭니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Azure Cache for Redis CLI 스크립트 샘플은 [Azure Cache for Redis 설명서](../cli-samples.md)에서 찾을 수 있습니다.