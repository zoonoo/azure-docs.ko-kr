---
title: Azure CLI 스크립트 샘플 - Azure Cache for Redis에 대한 호스트 이름, 포트 및 키 가져오기 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - Azure Cache for Redis 인스턴스에 대한 호스트 이름, 포트 및 키를 가져옵니다.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fd69c3182f026996618fea5e26466e8434ea36f6
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754458"
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
