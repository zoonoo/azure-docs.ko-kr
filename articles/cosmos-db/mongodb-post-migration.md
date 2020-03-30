---
title: MongoDB용 Azure Cosmos DB의 API를 통해 마이그레이션 후 최적화 단계
description: 이 문서는 MongoDB에서 Azure 코스모스 DB의 몽고 DB용 APi에 마이그레이션 후 최적화 기술을 제공합니다.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063606"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 Azure Cosmos DB의 API를 사용할 때 마이그레이션 후 최적화 단계

MongoDB 데이터베이스에 저장된 데이터를 MongoDB용 Azure 코스모스 DB의 API로 마이그레이션한 후 Azure Cosmos DB에 연결하고 데이터를 관리할 수 있습니다. 이 가이드에서는 마이그레이션 후 고려해야 할 단계를 제공합니다. 마이그레이션 단계에 [대한 MongoDB 를 Azure 코스모스 DB의 API로 마이그레이션하십시오.](../dms/tutorial-mongodb-cosmos-db.md)

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

- [응용 프로그램 연결](#connect-your-application)
- [인덱싱 정책 최적화](#optimize-the-indexing-policy)
- [몽고DB용 Azure 코스모스 DB의 API에 대한 글로벌 배포 구성](#globally-distribute-your-data)
- [일관성 수준 설정](#set-consistency-level)

> [!NOTE]
> 응용 프로그램 수준에서 마이그레이션 후 의 유일한 필수 단계는 새 Azure Cosmos DB 계정을 가리키도록 응용 프로그램의 연결 문자열을 변경하는 것입니다. 다른 모든 마이그레이션 단계는 권장되는 최적화입니다.
>

## <a name="connect-your-application"></a>응용 프로그램 연결

1. [Azure 포털에](https://www.portal.azure.com/) 새 창 로그인
2. Azure [포털에서](https://www.portal.azure.com/)왼쪽 창에서 모든 **리소스** 메뉴를 열고 데이터를 마이그레이션한 Azure Cosmos DB 계정을 찾습니다.
3. 연결 **문자열** 블레이드를 엽니다. 오른쪽 창에는 계정에 성공적으로 연결하는 데 필요한 모든 정보가 포함되어 있습니다.
4. 응용 프로그램의 구성(또는 기타 관련 장소)의 연결 정보를 사용하여 앱에서 MongoDB 연결에 대한 Azure Cosmos DB의 API를 반영합니다.
![연결 문자열](./media/mongodb-post-migration/connection-string.png)

자세한 내용은 [MongoDB 응용 프로그램을 Azure Cosmos DB에 연결](connect-mongodb-account.md) 페이지를 참조하십시오.

## <a name="optimize-the-indexing-policy"></a>인덱싱 정책 최적화

Azure Cosmos DB로 데이터를 마이그레이션하는 동안 기본적으로 모든 데이터 필드가 자동으로 인덱싱됩니다. 대부분의 경우 이 기본 인덱싱 정책은 허용됩니다. 일반적으로 인덱스를 제거하면 쓰기 요청을 최적화하고 기본 인덱싱 정책(즉, 자동 인덱싱)을 사용하여 읽기 요청을 최적화합니다.

인덱싱에 대한 자세한 내용은 [MongoDB에 대한 Azure 코스모스 DB의 API및](mongodb-indexing.md) Azure Cosmos DB 문서의 [인덱싱에서 데이터 인덱싱을](index-overview.md) 참조하십시오.

## <a name="globally-distribute-your-data"></a>데이터를 전 세계에 배포

Azure Cosmos DB는 전 세계의 모든 [Azure 지역](https://azure.microsoft.com/regions/#services)에서 사용할 수 있습니다. Azure Cosmos DB 계정의 기본 일관성 수준을 선택한 후 전역 배포 요구 사항에 따라 하나 이상의 Azure 영역을 연결할 수 있습니다. 고가용성 및 비즈니스 연속성을 위해 항상 2개 이상의 리전에서 실행하는 것이 좋습니다. [Azure Cosmos DB에서 다중 지역 배포 비용을 최적화하기](optimize-cost-regions.md)위한 팁을 검토할 수 있습니다.

데이터를 전역적으로 배포하려면 [MongoDB에 대한 Azure Cosmos DB의 API에서 전역으로 데이터 배포를](tutorial-global-distribution-mongodb.md)참조하십시오.

## <a name="set-consistency-level"></a>일관성 수준 설정

Azure 코스모스 DB는 5개의 잘 정의된 [일관성 수준을](consistency-levels.md)제공합니다. MongoDB와 Azure Cosmos DB 일관성 수준 간의 매핑에 대해 읽으려면 [일관성 수준 및 Azure Cosmos DB API를](consistency-levels-across-apis.md)읽으십시오. 기본 일관성 수준은 세션 일관성 수준입니다. 일관성 수준을 변경하는 것은 선택 사항이며 앱에 맞게 최적화할 수 있습니다. Azure 포털을 사용하여 일관성 수준을 변경하려면 다음을 수행하십시오.

1. 설정에서 **기본 일관성** 블레이드로 이동합니다.
2. 일관성 [수준](consistency-levels.md) 선택

대부분의 사용자는 기본 세션 일관성 설정에서 일관성 수준을 유지합니다. 그러나 다양한 [일관성 수준에 대한 가용성 및 성능 절충이 있습니다.](consistency-levels-tradeoffs.md)

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md)
* [스튜디오 3T를 사용하여 Azure 코스모스 DB 계정에 연결](mongodb-mongochef.md)
* [Azure Cosmos DB의 MongoDB용 API를 사용하여 읽기를 전역 배포하는 방법](mongodb-readpreference.md)
* [Azure Cosmos DB의 API for MongoDB를 사용하여 데이터 만료](mongodb-time-to-live.md)
* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure 코스모스 DB의 요청 단위](request-units.md)