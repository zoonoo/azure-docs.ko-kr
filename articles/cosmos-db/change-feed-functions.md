---
title: Azure Functions에서 Azure Cosmos DB 변경 피드를 사용하는 방법
description: Azure 함수를 사용하여 Azure Cosmos DB 변경 피드에 연결합니다. 나중에 모든 새 이벤트에서 트리거되는 반응성 Azure 함수를 만들 수 있습니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851377"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Azure 코스모스 DB 및 Azure 함수를 갖춘 서버리스 이벤트 기반 아키텍처

Azure Functions는 [변경 피드에](change-feed.md)연결하는 가장 간단한 방법을 제공합니다. Azure Cosmos 컨테이너의 변경 피드에서 각 새 이벤트에서 자동으로 트리거되는 작은 반응형 Azure 함수를 만들 수 있습니다.

![Cosmos DB에 대한 Azure 함수 트리거로 작동하는 서버리스 이벤트 기반 함수](./media/change-feed-functions/functions.png)

[Cosmos DB에 대한 Azure Functions 트리거를](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)사용하면 [작업자 인프라를](./change-feed-processor.md)유지 관리할 필요 없이 변경 [피드 프로세서의](./change-feed-processor.md)크기 조정 및 신뢰할 수 있는 이벤트 검색 기능을 활용할 수 있습니다. 이벤트 소싱 파이프라인의 나머지 부분에 대해 걱정하지 않고 Azure Function의 논리에 집중하기만 하면 됩니다. 트리거를 다른 [Azure Functions 바인딩과](../azure-functions/functions-triggers-bindings.md#supported-bindings)혼합할 수도 있습니다.

> [!NOTE]
> 현재 Cosmos DB에 대한 Azure Functions 트리거는 코어(SQL) API에서만 사용할 수 있습니다.

## <a name="requirements"></a>요구 사항

서버리스 이벤트 기반 흐름을 구현하려면 다음이 필요합니다.

* **모니터링되는 컨테이너:** 모니터링되는 컨테이너는 모니터링중인 Azure Cosmos 컨테이너이며 변경 피드가 생성되는 데이터를 저장합니다. 모니터링되는 컨테이너에 대한 모든 삽입, 업데이트는 컨테이너의 변경 피드에 반영됩니다.
* **임대 컨테이너**: 임대 컨테이너는 여러 동적 서버가 없는 Azure Function 인스턴스에서 상태를 유지하고 동적 크기 조정을 가능하게 합니다. 이 임대 컨테이너는 Cosmos DB에 대한 Azure Functions 트리거에 의해 수동으로 또는 자동으로 만들 수 있습니다. 임대 컨테이너를 자동으로 만들려면 [구성에서](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) *CreateLeaseCollectionIfExists* 플래그를 설정합니다. 분할된 임대 컨테이너는 파티션 키 정의가 `/id` 있어야 합니다.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>코스모스 DB에 대한 Azure 함수 트리거 만들기

Cosmos DB에 대한 Azure Functions 트리거를 통해 Azure 함수를 만드는 것이 이제 모든 Azure Functions IDE 및 CLI 통합에서 지원됩니다.

* 비주얼 스튜디오 사용자를 위한 [비주얼 스튜디오 확장.](../azure-functions/functions-develop-vs.md)
* 비주얼 스튜디오 코드 사용자를 위한 [시각적 스튜디오 코드 확장.](/azure/javascript/tutorial-vscode-serverless-node-01)
* 그리고 마지막으로 크로스 플랫폼 IDE 에 대한 [코어 CLI 툴링.](../azure-functions/functions-run-local.md#create-func)

## <a name="run-your-trigger-locally"></a>트리거를 로컬로 실행

[Azure Cosmos DB 에뮬레이터를](./local-emulator.md) 사용하여 [로컬로 Azure Function을](../azure-functions/functions-develop-local.md) 실행하여 Azure 구독이나 비용 발생 없이 서버없는 이벤트 기반 흐름을 만들고 개발할 수 있습니다.

클라우드에서 라이브 시나리오를 테스트하려는 경우 신용 카드 또는 Azure 구독 없이 [Cosmos DB를 무료로 사용해 볼](https://azure.microsoft.com/try/cosmosdb/) 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 계속 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [변경 피드를 읽는 방법](read-change-feed.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
* [변경 피드 프로세서 라이브러리를 사용하는 방법](change-feed-processor.md)
* [Azure Cosmos DB 및 Azure Functions를 사용하는 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md)
