---
title: 포함 파일
description: 포함 파일
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 8d12645bd302a7ea808f13c07e2a72f66846bad7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85121417"
---
이제 Azure Portal에서 Data Explorer 도구를 사용하여 Azure Cosmos DB의 API for MongoDB 데이터베이스 및 컨테이너를 만들 수 있습니다. 

1. **Data Explorer** > **새 컨테이너**를 선택합니다. 
    
    맨 오른쪽에 **컨테이너 추가** 영역이 표시되는데, 안 보이면 오른쪽으로 스크롤해야 합니다.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Azure Portal Data Explorer, 컨테이너 추가 창":::

2. **컨테이너 추가** 페이지에서 새 컨테이너의 설정을 입력합니다.

    |설정|제안 값|Description
    |---|---|---|
    |**데이터베이스 ID**|db|새 데이터베이스의 이름으로 *db*를 입력합니다. 데이터베이스 이름은 1~255자여야 하며, `/, \\, #, ?` 또는 후행 공백은 포함할 수 없습니다. **데이터베이스 처리량 프로비전** 옵션을 선택합니다. 그러면 데이터베이스에 프로비저닝된 처리량을 데이터베이스 내 모든 컨테이너가 공유할 수 있습니다. 이 옵션은 비용 절감에도 도움이 됩니다. |
    |**처리량**|400|처리량을 400 RU/s(초당 요청 단위)로 유지합니다. 대기 시간을 줄이면 나중에 처리량을 늘릴 수 있습니다. 필요에 따라 동적으로 증가하고 감소하는 RU/s 범위를 제공하는 [자동 크기 조정 모드](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale)를 선택할 수도 있습니다.| 
    |**컬렉션 ID**|coll|새 컨테이너의 이름으로 `coll`을 입력합니다. 컨테이너 ID에는 데이터베이스 이름과 동일한 문자 요구 사항이 적용됩니다.|
    |**스토리지 용량**|고정(10GB)|이 애플리케이션에 *고정(10GB)* 을 입력합니다. *무제한*을 선택하면 삽입된 모든 항목에 필요한 `Shard Key`를 만들어야 합니다.|
    |**분할 키**| /_id| 이 문서에서 설명하는 샘플은 분할 키를 사용하지 않으므로 */_id*로 설정하면 자동으로 생성된 ID 필드가 분할 키로 사용됩니다. [Azure Cosmos DB에서 분할(파티셔닝)](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)에서 분할(파티셔닝이라고도 함)에 대해 자세히 알아봅니다.|
        
    **확인**을 선택합니다. Data Explorer에 새 데이터베이스와 컨테이너가 표시됩니다.
