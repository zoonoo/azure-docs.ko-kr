---
title: 금지 Azure Cosmos DB 문제 해결
description: 사용할 수 없는 예외를 진단 하 고 해결 하는 방법을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99971911"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>사용할 수 없는 예외 Azure Cosmos DB 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 상태 코드 403은 요청을 완료할 수 없다는 것을 나타냅니다.

## <a name="firewall-blocking-requests"></a>방화벽 차단 요청
이 시나리오에서는 아래와 같은 오류를 확인 하는 것이 일반적입니다.

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>해결 방법
현재 [방화벽 설정이](how-to-configure-firewall.md) 올바른지 확인 하 고 연결 하려는 ip 또는 네트워크를 포함 합니다.
최근 업데이트 한 경우 변경 내용을 **적용 하는 데 최대 15 분** 이 걸릴 수 있다는 점에 유의 하세요.

## <a name="next-steps"></a>다음 단계
* [IP 방화벽](how-to-configure-firewall.md)을 구성 합니다.
* [가상 네트워크](how-to-configure-vnet-service-endpoint.md)에서 액세스를 구성 합니다.
* [개인 끝점](how-to-configure-private-endpoints.md)에서 액세스를 구성 합니다.
