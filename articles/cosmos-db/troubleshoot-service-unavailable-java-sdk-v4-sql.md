---
title: Java v4 SDK를 사용 하 여 서비스를 사용할 수 없는 예외 Azure Cosmos DB 문제 해결
description: Java v4 SDK를 사용 하 여 Azure Cosmos DB 서비스를 사용할 수 없는 예외를 진단 하 고 해결 하는 방법을 알아봅니다.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340089"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Java v4 SDK 서비스를 사용할 수 없는 예외 Azure Cosmos DB 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Java v4 SDK가 Azure Cosmos DB에 연결할 수 없습니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 서비스를 사용할 수 없는 예외에 대 한 알려진 원인과 해결 방법이 나와 있습니다.

### <a name="the-required-ports-are-being-blocked"></a>필요한 포트가 차단 되 고 있습니다.
[필요한 포트](sql-sdk-connection-modes.md#service-port-ranges) 를 모두 사용 하도록 설정 했는지 확인 합니다.

### <a name="client-side-transient-connectivity-issues"></a>클라이언트 쪽 임시 연결 문제
서비스를 사용할 수 없는 예외는 시간 초과를 유발 하는 일시적인 연결 문제가 있는 경우에 발생할 수 있습니다. 일반적으로이 시나리오와 관련 된 스택 추적에는 `ServiceUnavailableException` 진단 정보에 대 한 오류가 포함 됩니다. 예를 들어:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

[요청 시간 제한 문제 해결 단계](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) 에 따라 문제를 해결 합니다.

### <a name="service-outage"></a>서비스 중단
[Azure 상태](https://status.azure.com/status) 를 확인 하 여 진행 중인 문제가 있는지 확인 합니다.


## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB Java v4 SDK를 사용 하는 경우 문제를 [진단 하 고 해결](troubleshoot-java-sdk-v4-sql.md) 합니다.
* [Java V4 SDK](performance-tips-java-sdk-v4-sql.md)의 성능 지침에 대해 알아봅니다.