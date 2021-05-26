---
title: 포함 파일
description: 포함 파일
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5e83acf3b2c0762a6e6622edaa345215faa6192e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073767"
---
이제 Azure Portal에서 Data Explorer 도구를 사용하여 데이터베이스 및 컨테이너를 만들 수 있습니다. 

1. **Data Explorer** > **새 컨테이너** 를 선택합니다. 
    
    맨 오른쪽에 **컨테이너 추가** 영역이 표시되는데, 안 보이면 오른쪽으로 스크롤해야 합니다.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="Azure Portal Data Explorer, 컨테이너 추가 창":::

2. **컨테이너 추가** 페이지에서 새 컨테이너의 설정을 입력합니다.

    |설정|제안 값|설명
    |---|---|---|
    |**데이터베이스 ID**|ToDoList|새 데이터베이스의 이름으로 *작업* 을 입력합니다. 데이터베이스 이름은 1~255자여야 하며, `/, \\, #, ?` 또는 후행 공백은 포함할 수 없습니다. **컨테이너 간에 처리량 공유** 옵션을 선택합니다. 그러면 데이터베이스에 프로비저닝된 처리량을 데이터베이스 내 모든 컨테이너가 공유할 수 있습니다. 이 옵션은 비용 절감에도 도움이 됩니다. |
    | **데이터베이스 처리량**| **자동 스케일링** 또는 **수동** 처리량을 프로비저닝할 수 있습니다. 수동 처리량을 사용하면 RU/s를 직접 스케일링할 수 있지만 자동 스케일링을 사용하면 시스템에서 사용량에 따라 RU/s를 스케일링할 수 있습니다. 이 예제에서는 **수동** 을 선택합니다. <br><br> 처리량을 400 RU/s(초당 요청 단위)로 유지합니다. 대기 시간을 줄이려면 [용량 계산기](../articles/cosmos-db/estimate-ru-with-capacity-planner.md)를 사용하여 필요한 RU/s를 예측하여 나중에 처리량을 스케일 업할 수 있습니다.<br><br>**참고**: 서버리스 계정에서 새 컨테이너를 만들 때는 이 설정을 사용할 수 없습니다. |
    |**컨테이너 ID**|Items|새 컨테이너의 이름으로 *Items* 를 입력합니다. 컨테이너 ID에는 데이터베이스 이름과 동일한 문자 요구 사항이 적용됩니다.|
    |**파티션 키**| /category| 이 문서에 설명된 샘플은 파티션 키로 */category* 를 사용합니다.|

    이 예제에서는 **고유 키** 를 추가하거나 **분석 저장소** 를 설정하지 마세요. 고유 키를 사용하면 분할 키당 하나 이상의 값의 고유성을 보장하여 데이터베이스에 데이터 무결성 레이어를 추가할 수 있습니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](../articles/cosmos-db/unique-keys.md)를 참조하세요. [분석 저장소](../articles/cosmos-db/analytical-store-introduction.md)는 트랜잭션 워크로드에 영향을 주지 않으면서 운영 데이터에 대한 대규모 분석을 사용하도록 설정하는 데 사용됩니다.
    
    **확인** 을 선택합니다. Data Explorer에 새 데이터베이스와 컨테이너가 표시됩니다.