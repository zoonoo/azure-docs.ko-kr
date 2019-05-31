---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c54aa861a47b11756f05e003e9b944df6c5b0e28
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132447"
---
Azure Cosmos DB 바인딩은 SQL API에서만 사용할 수 있습니다. 다른 모든 Azure Cosmos DB API의 경우 [Azure Cosmos DB의 MongoDB용 API](../articles/cosmos-db/mongodb-introduction.md), [Cassandra API](../articles/cosmos-db/cassandra-introduction.md), [Gremlin API](../articles/cosmos-db/graph-introduction.md) 및 [Table API](../articles/cosmos-db/table-introduction.md)를 비롯한 API에 정적 클라이언트를 사용하여 함수에서 데이터베이스에 액세스해야 합니다.
