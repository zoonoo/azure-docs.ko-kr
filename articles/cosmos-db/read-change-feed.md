---
title: Azure Cosmos DB Azure Cosmos DB에서 변경 피드 액세스
description: 이 문서에서는 Azure Cosmos DB에서 변경 피드를 읽고 액세스 하는 데 사용할 수 있는 다양 한 옵션을 설명 합니다.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982499"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Cosmos DB 변경 피드 읽기

다음 옵션 중 하나를 사용하여 Azure Cosmos DB 변경 피드로 작업할 수 있습니다.

* Azure Functions 사용
* 변경 피드 프로세서 사용
* Azure Cosmos DB SQL API SDK 사용
* 변경 피드 끌어오기 모델 사용 (미리 보기)

## <a name="using-azure-functions"></a>Azure Functions 사용

Azure Functions는 가장 간단하고 권장되는 옵션입니다. Cosmos DB에 대 한 Azure Functions 트리거를 만들 때 연결할 컨테이너를 선택할 수 있으며, 컨테이너가 변경 될 때마다 Azure Function이 트리거됩니다. 트리거는 Azure Functions 포털, Azure Cosmos DB 포털을 사용하거나 SDK를 사용하여 프로그래밍 방식으로 만들 수 있습니다. Visual Studio 및 VS Code는 Azure Functions를 작성하는 지원을 제공하며 플랫폼 간 개발을 위해 Azure Functions CLI를 사용할 수도 있습니다. 데스크톱에서 코드를 작성하고 디버그한 다음, 클릭 한 번으로 함수를 배포할 수 있습니다. 자세한 내용은 [Azure Functions를 사용한 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md) 및 [Azure Functions와 함께 변경 피드 사용](change-feed-functions.md)) 문서를 참조하세요.

## <a name="using-the-change-feed-processor"></a>변경 피드 프로세서 사용

변경 피드 프로세서는 복잡성을 숨기고 변경 피드에 대 한 완전 한 제어를 제공 합니다. 라이브러리는 관찰자 패턴을 따릅니다. 여기서 처리 함수는 라이브러리를 통해 호출됩니다. 높은 처리량 변경 피드가 있는 경우 변경 피드를 읽을 여러 클라이언트를 인스턴스화할 수 있습니다. 변경 피드 프로세서 라이브러리를 사용하므로 이 논리를 구현할 필요 없이 다른 클라이언트 간에 로드가 자동으로 분할됩니다. 모든 복잡성은 라이브러리가 처리합니다. 자세히 알아보려면 [변경 피드 프로세서 사용](change-feed-processor.md)을 참조 하세요. 변경 피드 프로세서는 [AZURE COSMOS DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)의 일부입니다.

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Azure Cosmos DB SQL API SDK 사용

SDK를 사용하면 변경 피드의 하위 수준 컨트롤을 가져올 수 있습니다. 검사점을 관리 하 고 특정 논리적 파티션 키 등에 액세스할 수 있습니다. 여러 판독기가 있는 경우를 사용 `ChangeFeedOptions` 하 여 다른 스레드나 다른 클라이언트에 읽기 부하를 분산할 수 있습니다.

## <a name="using-the-change-feed-pull-model"></a>변경 피드 끌어오기 모델 사용

[변경 피드 끌어오기 모델](change-feed-pull-model.md) 을 사용 하면 변경 피드를 자신만의 속도로 사용 하 고 FeedRanges를 사용 하 여 변경 내용 처리를 병렬화 할 수 있습니다. FeedRange는 파티션 키 값의 범위를 확장 합니다. 변경 피드 끌어오기 모델을 사용 하면 특정 파티션 키에 대 한 변경 내용을 쉽게 처리할 수 있습니다.

> [!NOTE]
> 변경 피드 끌어오기 모델은 현재 [Azure Cosmos DB .NET SDK 에서만 미리 보기로](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) 제공 됩니다. 다른 SDK 버전에서는 미리 보기를 아직 사용할 수 없습니다.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra 및 MongoDB에 대 한 Api의 변경 피드

변경 피드 기능은 MongoDB API에서 변경 스트림으로 표시 되 고 Cassandra API 조건자를 사용 하 여 쿼리 됩니다. MongoDB API에 대 한 구현 세부 정보에 대 한 자세한 내용은 [MongoDB에 대 한 AZURE COSMOS DB api의 변경 스트림](mongodb-change-streams.md)을 참조 하세요.

기본 Apache Cassandra는 cdc (변경 데이터 캡처)를 제공 합니다 .이 메커니즘은 보관을 위해 특정 테이블에 플래그를 지정 하 고 CDC 로그의 구성 가능한 크기에 도달 하면 해당 테이블에 대 한 쓰기를 거부 하는 메커니즘입니다. Cassandra에 대 한 Azure Cosmos DB API의 변경 피드 기능은 CQL을 통해 조건자를 사용 하 여 변경 내용을 쿼리 하는 기능을 향상 시킵니다. 구현 세부 정보에 대 한 자세한 내용은 [Cassandra에 대 한 AZURE COSMOS DB API에서 변경 피드](cassandra-change-feed.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [Azure Functions로 변경 피드 사용](change-feed-functions.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
