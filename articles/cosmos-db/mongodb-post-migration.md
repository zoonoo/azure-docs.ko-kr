---
title: MongoDB용 Azure Cosmos DB의 API를 사용한 마이그레이션 후 최적화 단계
description: 이 문서는 MongoDB에서 Mongo DB용 Azure Cosmos DB의 API로 마이그레이션 후 최적화 기술을 제공합니다.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 02/14/2021
ms.author: chrande
ms.openlocfilehash: ce0facaba43e9cc8e37b19fbd3dfc840067d278f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560398"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB용 Azure Cosmos DB의 API 사용 시 마이그레이션 후 최적화 단계
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB 데이터베이스에 저장된 데이터를 MongoDB용 Azure Cosmos DB의 API로 마이그레이션한 후 Azure Cosmos DB에 연결하고 데이터를 관리할 수 있습니다. 이 가이드는 마이그레이션 후에 고려해야 하는 단계를 제공합니다. 마이그레이션 단계는 [MongoDB에서 MongoDB용 Azure Cosmos DB의 API로 마이그레이션 자습서](../dms/tutorial-mongodb-cosmos-db.md)를 참조하세요.

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

- [애플리케이션 연결](#connect-your-application)
- [인덱싱 정책 최적화](#optimize-the-indexing-policy)
- [MongoDB용 Azure Cosmos DB의 API에 대한 글로벌 배포 구성](#globally-distribute-your-data)
- [일관성 수준 설정](#set-consistency-level)

> [!NOTE]
> 애플리케이션 수준에서 마이그레이션 후 필수 단계는 애플리케이션의 연결 문자열을 새 Azure Cosmos DB 계정을 가리키도록 변경하는 것입니다. 다른 모든 마이그레이션 단계를 최적화하는 것이 좋습니다.
>

## <a name="connect-your-application"></a>애플리케이션 연결

1. 새 창에서 [Azure Portal](https://www.portal.azure.com/)에 로그인합니다.
2. [Azure Portal](https://www.portal.azure.com/)의 왼쪽 창에서 **모든 리소스** 메뉴를 열고 데이터를 마이그레이션한 Azure Cosmos DB 계정을 찾습니다.
3. **연결 문자열** 블레이드를 엽니다. 오른쪽 창에는 계정에 성공적으로 연결하는 데 필요한 모든 정보가 포함되어 있습니다.
4. 애플리케이션의 구성(또는 기타 관련 장소)에 있는 연결 정보를 사용하여 앱에 MongoDB용 Azure Cosmos DB의 API 연결을 반영합니다.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="연결 문자열에 대한 설정을 보여주는 스크린샷.":::

자세한 내용은 [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md) 페이지를 참조하세요.

## <a name="optimize-the-indexing-policy"></a>인덱싱 정책 최적화

기본적으로 모든 데이터 필드는 Azure Cosmos DB로 데이터를 마이그레이션하는 동안 자동으로 인덱싱됩니다. 대부분의 경우 이 기본 인덱싱 정책을 사용할 수 있습니다. 일반적으로 인덱스를 제거하면 쓰기 요청이 최적화되고 기본 인덱싱 정책(즉, 자동 인덱싱)이 읽기 요청을 최적화합니다.

인덱싱에 대한 자세한 내용은 [MongoDB용 Azure Cosmos DB의 API에서 데이터 인덱싱](mongodb-indexing.md) 및 [Azure Cosmos DB에서 인덱싱](index-overview.md) 문서를 참조하세요.

## <a name="globally-distribute-your-data"></a>데이터 전역 배포

Azure Cosmos DB는 전 세계의 모든 [Azure 지역](https://azure.microsoft.com/regions/#services)에서 사용할 수 있습니다. Azure Cosmos DB 계정의 기본 일관성 수준을 선택한 후 글로벌 배포 요구 사항에 따라 하나 이상의 Azure 영역을 연결할 수 있습니다. 고가용성 및 비즈니스 연속성을 위해 항상 둘 이상의 지역에서 실행하는 것이 좋습니다. [Azure Cosmos DB에서 다중 지역 배포의 비용을 최적화](optimize-cost-regions.md)하기 위한 팁을 검토할 수 있습니다.

데이터를 전역 배포하려면 [MongoDB용 Azure Cosmos DB의 API에서 데이터 전역 배포](tutorial-global-distribution-mongodb.md)를 참조하세요.

## <a name="set-consistency-level"></a>일관성 수준 설정

Azure Cosmos DB는 올바르게 정의된 5가지 [일관성 수준](consistency-levels.md)을 제공합니다. MongoDB와 Azure Cosmos DB 일관성 수준 간의 매핑에 대한 자세한 내용은 [일관성 수준 및 Azure Cosmos DB API](./consistency-levels.md)를 참조하세요. 기본 일관성 수준은 세션 일관성 수준입니다. 일관성 수준 변경은 선택 사항이며 앱에 맞게 최적화할 수 있습니다. Azure Portal을 사용하여 일관성 수준을 변경하는 방법은 다음과 같습니다.

1. 설정에서 **기본 일관성** 블레이드로 이동합니다.
2. [일관성 수준](consistency-levels.md)을 선택합니다.

대부분의 사용자는 기본 세션 일관성 설정에서 일관성 수준을 유지합니다. 하지만 [다양한 일관성 수준에 대한 가용성 및 성능 절충안](./consistency-levels.md)이 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md)
* [Studio 3T를 사용하여 Azure Cosmos DB 계정에 연결](mongodb-mongochef.md)
* [Azure Cosmos DB의 MongoDB용 API를 사용하여 읽기를 전역 배포하는 방법](mongodb-readpreference.md)
* [Azure Cosmos DB의 API for MongoDB를 사용하여 데이터 만료](mongodb-time-to-live.md)
* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)