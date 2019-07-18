---
title: Azure Cosmos DB 트리거 연결 정책
description: Azure Cosmos DB 트리거에 사용할 연결 정책을 구성하는 방법 알아보기
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755461"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Azure Cosmos DB 트리거에 사용할 연결 정책을 구성하는 방법

이 문서에서는 Azure Cosmos DB 트리거를 사용하여 Azure Cosmos 계정에 연결할 때 연결 정책을 구성하는 방법을 설명합니다.

## <a name="why-is-the-connection-policy-important"></a>연결 정책이 중요한 이유는 무엇인가요?

직접 모드와 게이트웨이 모드의 두 가지 연결 모드가 있습니다. 이러한 연결 모드에 대한 자세한 내용은 [성능 팁](./performance-tips.md#networking) 문서를 참조하세요. 기본적으로 **게이트웨이**는 Azure Cosmos DB 트리거에서 모든 연결을 설정하는 데 사용됩니다. 그러나 성능 중심 시나리오에는 최상의 옵션이 아닐 수 있습니다.

## <a name="changing-the-connection-mode-and-protocol"></a>연결 모드 및 프로토콜 변경

클라이언트 연결 정책, 즉, **연결 모드**와 **연결 프로토콜**을 구성하는 데 사용할 수 있는 두 가지 주요 구성 설정이 있습니다. Azure Cosmos DB 트리거와 모든 [Azure Cosmos DB 바인딩](../azure-functions/functions-bindings-cosmosdb-v2.md#output))에서 사용하는 기본 연결 모드 및 프로토콜을 변경할 수 있습니다. 기본 설정을 변경하려면 Azure Functions 프로젝트 또는 Azure Functions 앱에서 `host.json` 파일을 찾아 다음과 같은 [추가 설정](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings)에 추가해야 합니다.

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
* [Azure Cosmos DB 성능 팁](./performance-tips.md)
* [코드 샘플](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
