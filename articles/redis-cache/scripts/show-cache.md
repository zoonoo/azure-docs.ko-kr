---
title: "Azure CLI 스크립트 샘플 - Azure Redis Cache의 세부 정보 가져오기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Azure Redis Cache의 세부 정보 가져오기"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 9f4eb32227bd8a68837eabd58b9d058bc4995d17
ms.contentlocale: ko-kr
ms.lasthandoff: 04/15/2017

---

# <a name="get-details-of-an-azure-redis-cache"></a>Azure Redis Cache의 세부 정보 가져오기

이 시나리오에서는 Azure Redis Cache 인스턴스의 프로비저닝 상태를 포함하여 세부 정보를 검색하는 방법을 학습합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Azure Redis Cache 인스턴스의 세부 정보를 검색합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#show) | Azure Redis Cache 인스턴스에 대한 세부 정보를 검색합니다. |


## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 Azure Redis Cache CLI 스크립트 샘플은 [Azure Redis Cache 설명서](../cli-samples.md)에서 확인할 수 있습니다.
