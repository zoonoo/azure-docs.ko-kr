---
title: Azure Cosmos DB 사용할 수 없음 예외 문제 해결
description: 사용할 수 없음 예외를 진단하고 해결하는 방법을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99971911"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Azure Cosmos DB 사용할 수 없음 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 상태 코드 403은 요청을 완료할 수 없음을 나타냅니다.

## <a name="firewall-blocking-requests"></a>방화벽 차단 요청
이 시나리오에서는 아래와 같은 오류가 발생하는 것이 일반적입니다.

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>솔루션
현재 [방화벽 설정](how-to-configure-firewall.md)이 올바른지 확인하고 연결하려는 IP 또는 네트워크를 포함합니다.
최근에 업데이트한 경우 변경 내용을 **적용하는 데 최대 15분** 이 걸릴 수 있다는 점에 유의하세요.

## <a name="next-steps"></a>다음 단계
* [IP 방화벽](how-to-configure-firewall.md) 구성
* [가상 네트워크](how-to-configure-vnet-service-endpoint.md)에서 액세스 구성
* [프라이빗 엔드포인트](how-to-configure-private-endpoints.md)에서 액세스 구성
