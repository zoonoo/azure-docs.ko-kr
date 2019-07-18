---
title: Azure CLI 스크립트 샘플 - Azure Cache for Redis에 대한 세부 정보 가져오기 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - Azure Cache for Redis에 대한 세부 정보를 가져옵니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 8b4c0311f935fa141170206f51e01b90eb4b98ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240290"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 세부 정보 가져오기

이 시나리오에서는 프로비전 상태를 포함하여 Azure Cache for Redis 인스턴스에 대한 세부 정보를 검색하는 방법을 알아봅니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Azure Cache for Redis 인스턴스에 대한 세부 정보를 검색합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Azure Redis Cache 인스턴스에 대한 세부 정보를 검색합니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 Azure Cache for Redis CLI 스크립트 샘플은 [Azure Cache for Redis 설명서](../cli-samples.md)에서 찾을 수 있습니다.
