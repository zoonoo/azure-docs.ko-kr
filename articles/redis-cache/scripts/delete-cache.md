---
title: "Azure CLI 스크립트 샘플 - Azure Redis Cache 삭제 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure Redis Cache 삭제"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 11d228cc633fee8c6a2984e2eb23690a43d4c0fc
ms.contentlocale: ko-kr
ms.lasthandoff: 09/09/2017

---

# <a name="delete-an-azure-redis-cache"></a>Azure Redis Cache 삭제

이 시나리오에서는 Azure Redis Cache를 삭제하는 방법을 배웁니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Azure Redis Cache 인스턴스를 삭제합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis#az_redis_delete) | Redis Cache 인스턴스를 삭제합니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Redis Cache CLI 스크립트 샘플은 [Azure Redis Cache 설명서](../cli-samples.md)에서 확인할 수 있습니다.
