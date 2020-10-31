---
title: Cosmos DB 고급 구성에 대 한 Azure Functions 트리거
description: Cosmos DB에 대 한 Azure Functions 트리거에서 사용 하는 로깅 및 연결 정책을 구성 하는 방법에 대해 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: c47d18726d9581b1b03aa2e676a71d6ca1bc1b7d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086469"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB에 대 한 Azure Functions 트리거를 사용 하 여 로깅 및 연결을 구성 하는 방법
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 문서에서는 Cosmos DB에 대해 Azure Functions 트리거를 사용할 때 설정할 수 있는 고급 구성 옵션을 설명 합니다.

## <a name="enabling-trigger-specific-logs"></a>트리거 특정 로그 사용

Cosmos DB용 Azure Functions 트리거는 내부적으로 [변경 피드 프로세서 라이브러리](./change-feed-processor.md)를 사용하며, 라이브러리는 [문제 해결을 위해](./troubleshoot-changefeed-functions.md) 내부 작업을 모니터링하는 데 사용할 수 있는 상태 로그 세트를 생성합니다.

상태 로그는 부하 분산 시나리오 또는 초기화 중에 작업을 시도할 때 Cosmos DB용 Azure Functions 트리거의 작동 방식을 설명합니다.

### <a name="enabling-logging"></a>로깅을 사용하도록 설정

Cosmos DB용 Azure Functions 트리거를 사용할 때 로깅을 설정하려면 Azure Functions 프로젝트 또는 Azure Functions App에서 `host.json` 파일을 찾아서 [필요한 로깅 수준을 구성](../azure-functions/functions-monitoring.md#log-levels-and-categories)합니다. 다음 샘플과 같이에 대해 추적을 사용 하도록 설정 합니다  `Host.Triggers.CosmosDB` .

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

업데이트된 구성을 사용하여 Azure Function이 배포되면 추적의 일부로 Cosmos DB용 Azure Functions 트리거 로그가 나타납니다. *범주* `Host.Triggers.CosmosDB` 아래에서 구성된 로깅 공급 기업에서 로그를 볼 수 있습니다.

### <a name="query-the-logs"></a>로그 쿼리

다음 쿼리를 실행하여 [Azure Application Insights의 Analytics](../azure-monitor/log-query/log-query-overview.md)에 있는 Cosmos DB용 Azure Functions 트리거에서 생성된 로그를 쿼리합니다.

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>연결 정책 구성

직접 모드와 게이트웨이 모드의 두 가지 연결 모드가 있습니다. 이러한 연결 모드에 대 한 자세한 내용은 [연결 모드](sql-sdk-connection-modes.md) 문서를 참조 하세요. 기본적으로 **게이트웨이** 는 Cosmos DB용 Azure Functions 트리거에서 모든 연결을 설정하는 데 사용됩니다. 그러나 성능 중심 시나리오에는 최상의 옵션이 아닐 수 있습니다.

### <a name="changing-the-connection-mode-and-protocol"></a>연결 모드 및 프로토콜 변경

클라이언트 연결 정책, 즉, **연결 모드** 와 **연결 프로토콜** 을 구성하는 데 사용할 수 있는 두 가지 주요 구성 설정이 있습니다. Cosmos DB용 Azure Functions 트리거와 모든 [Azure Cosmos DB 바인딩](../azure-functions/functions-bindings-cosmosdb-v2-output.md))에서 사용하는 기본 연결 모드 및 프로토콜을 변경할 수 있습니다. 기본 설정을 변경하려면 Azure Functions 프로젝트 또는 Azure Functions 앱에서 `host.json` 파일을 찾아 다음과 같은 [추가 설정](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)에 추가해야 합니다.

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

이때 `connectionMode`에는 원하는 연결 모드(직접 또는 게이트웨이)가 있어야 하고 `protocol`에는 원하는 연결 프로토콜(Tcp 또는 Https)이 있어야 합니다. 

Azure Functions 프로젝트가 Azure Functions V1 런타임과 함께 작동하는 경우 구성의 이름이 약간 다릅니다. 다음과 같이 `cosmosDB` 대신 `documentDB`를 사용해야 합니다.

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Azure Functions 사용량 계획 호스팅 계획을 사용하면 각 인스턴스가 유지할 수 있는 소켓 연결의 양에 제한이 있습니다. 직접/TCP 모드를 사용하면 기본적으로 더 많은 연결이 생성되며 [사용량 계획 제한](../azure-functions/manage-connections.md#connection-limit)에 도달할 수 있습니다. 이 경우 게이트웨이 모드를 사용하거나 [앱 서비스 모드](../azure-functions/functions-scale.md#app-service-plan)에서 Azure Functions를 실행하면 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Functions의 연결 제한](../azure-functions/manage-connections.md#connection-limit)
* Azure Functions 애플리케이션에서 [모니터링을 사용하도록 설정](../azure-functions/functions-monitoring.md)합니다.
* Cosmos DB용 Azure Functions 트리거를 사용할 때 [일반적인 문제 진단 및 해결](./troubleshoot-changefeed-functions.md) 방법을 알아봅니다.