---
title: 호스트 이름, 포트 및 키 가져오기 - Azure Cache for Redis - Azure CLI
description: 이 Azure CLI 코드 샘플에서는 Azure Cache for Redis 인스턴스에 대한 호스트 이름, 포트 및 키를 가져오는 방법을 보여줍니다.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9fef834e550f144075ec9cc72cac3b11cc1e99e4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504214"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 호스트 이름, 포트 및 키 가져오기

이 시나리오에서는 Azure Cache for Redis 인스턴스에 연결하는 데 사용되는 호스트 이름, 포트 및 키를 검색하는 방법에 대해 알아봅니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Azure Cache for Redis 인스턴스에 대한 호스트 이름, 포트 및 키를 검색합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Azure Redis Cache 인스턴스에 대한 세부 정보를 검색합니다. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Azure Cache for Redis 인스턴스에 대한 액세스 키를 검색합니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Azure Cache for Redis CLI 스크립트 샘플은 [Azure Cache for Redis 설명서](../cli-samples.md)에서 찾을 수 있습니다.
