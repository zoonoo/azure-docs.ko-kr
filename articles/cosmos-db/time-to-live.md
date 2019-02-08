---
title: TTL(Time To Live)을 사용한 Azure Cosmos DB의 데이터 만료
description: TTL을 사용하여 Microsoft Azure Cosmos DB는 일정 기간 후에 시스템에서 문서를 자동으로 삭제하는 기능을 제공합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 9b5054da56bdc6a88642ea7860702b280a76dbb1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461171"
---
# <a name="time-to-live-in-azure-cosmos-db"></a>Azure Cosmos DB의 TTL(Time to Live) 

Azure Cosmos DB는 “Time to Live” 또는 TTL을 통해 일정 기간이 지난 후 컨테이너에서 항목을 자동으로 삭제하는 기능을 제공합니다. 기본적으로 컨테이너 수준에서 TTL(Time to Live)을 설정하거나 항목별 기준으로 값을 재정의할 수 있습니다. 컨테이너 또는 항목 수준에서 TTL을 설정하면 Azure Cosmos DB는 마지막으로 수정한 시간으로부터 해당 시간이 지나면 이러한 항목이 자동으로 제거합니다. TTL(Time to Live) 값은 초 단위로 구성됩니다. TTL을 구성하면 시스템에서는 클라이언트 애플리케이션에서 명시적으로 발급된 삭제 작업과 달리 TTL 값을 기준으로 만료된 항목을 자동으로 삭제합니다.

## <a name="time-to-live-for-containers-and-items"></a>컨테이너 및 항목에 대한 TTL(Time to live)

TTL(Time to Live) 값은 초 단위로 설정되며 항목이 마지막으로 수정된 시간의 델타로 해석됩니다. TTL(Time to Live)은 컨테이너 또는 컨테이너 내 항목에서 설정할 수 있습니다.

1. **컨테이너에서 TTL(Time to Live)**(`DefaultTimeToLive` 사용하여 설정):

   - 누락(또는 null로 설정)되는 경우 항목이 자동으로 삭제되지 않습니다.

   - 표시되고 값이 “-1”로 설정되면 무한과 동일하며 항목은 기본적으로 만료되지 않습니다.

   - 표시되고 값이 숫자(“n”)로 설정된 경우 항목은 마지막으로 수정된 시간으로부터 “n”초 후에 만료됩니다.

2. **항목에서 TTL(Time to Live)**(`TimeToLive` 사용하여 설정):

   - 이 속성은 `DefaultTimeToLive`가 표시되고 부모 컨테이너에 대해 null로 설정되지 않은 경우에만 적용할 수 있습니다.

   - 표시되면 부모 컨테이너의 `DefaultTimeToLive` 값을 재정의합니다.

## <a name="time-to-live-configurations"></a>TTL(Time to Live) 구성

* TTL이 컨테이너에서 ‘n’으로 설정되면 해당 컨테이너의 항목은 n초 후에 만료됩니다.  동일한 컨테이너에 고유한 TTL(Time to Live)이 있는 항목이 있는 경우 -1(만료되지 않음을 나타냄)로 설정합니다. 또는 일부 항목이 TTL(Time to Live) 설정을 다른 숫자로 정의한 경우에는 이러한 항목은 구성된 TTL 값을 기준으로 만료됩니다. 

* 컨테이너에서 TTL을 설정하지 않으면, 이 컨테이너의 항목에서 TTL(Time to Live)은 효과가 없습니다. 

* 컨테이너에서 TTL이 -1로 설정된 경우 TTL(Time to Live)이 n으로 설정된 이 컨테이너의 항목은 n초 후에 만료되고 나머지 항목은 만료되지 않습니다. 

TTL을 기준으로 항목을 삭제하는 것은 무료입니다. TTL 만료의 결과로 항목을 삭제하는 경우 추가 비용은 청구되지 않습니다(즉, 추가 RU 없음).

## <a name="next-steps"></a>다음 단계

다음 문서에서 TTL(Time to Live)을 구성하는 방법에 대해 알아봅니다.

* [TTL(Time to Live) 구성 방법](how-to-time-to-live.md)
