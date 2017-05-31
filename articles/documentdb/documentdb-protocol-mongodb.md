---
title: "Azure Cosmos DB 소개: MongoDB API | Microsoft Docs"
description: "인기 있는 OSS MongoDB API를 사용하여 짧은 대기 시간으로 엄청난 양의 JSON 문서를 저장하고 쿼리하기 위해 Azure Cosmos DB를 사용하는 방법을 알아봅니다."
keywords: "MongoDB 정의"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7dc474f9f940132dd363740e8288a70ee2f2b970
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB 소개: MongoDB API

[Azure Cosmos DB](../cosmos-db/introduction.md)는 업무에 중요한 응용 프로그램에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB는 [업계 최고의 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)로 지원되는 [턴키 전역 분산](../documentdb/documentdb-distribute-data-globally.md), 전 세계적으로 [처리량 및 저장소의 탄력적인 확장](../documentdb/documentdb-partition-data.md), 99번째 백분위수의 1자리 수 밀리초 크기 대기 시간, [잘 정의된 5개 일관성 수준](../documentdb/documentdb-consistency-levels.md), 보장된 고가용성을 제공합니다. Azure Cosmos DB는 사용자가 스키마 및 인덱스 관리를 처리하지 않아도 되도록 [데이터를 자동으로 인덱싱](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)합니다. 또한 다중 모델 방식이며, 문서, 키-값, 그래프 및 열 형식 데이터 모델을 지원합니다. 

![Azure MongoDB API](./media/documentdb-protocol-mongodb/cosmosdb-mongodb.png) 

DocumentDB 데이터베이스는 [MongoDB](https://docs.mongodb.com/manual/introduction/)용으로 작성된 앱의 데이터 저장소로 사용할 수 있습니다. 즉, 이제 MongoDB용으로 작성된 응용 프로그램이 기존 [드라이버](https://docs.mongodb.org/ecosystem/drivers/)를 사용하여 Cosmos DB와 통신하고 MongoDB 데이터베이스 대신 DocumentDB 데이터베이스를 사용할 수 있습니다. 대부분의 경우 연결 문자열을 변경하기만 하면 MongoDB에서 DocumentDB로 전환할 수 있습니다. 이 기능을 사용하면 친숙한 기술과 도구를 MongoDB에 계속 사용하면서 Azure Cosmos DB의 글로벌 배포 및 [포괄적인 업계 선도적 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)로 Azure 클라우드에서 MongoDB 데이터베이스 응용 프로그램을 쉽게 빌드하고 실행할 수 있습니다.


## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>MongoDB 응용 프로그램에 Azure Cosmos DB를 사용할 경우의 이점은 무엇인가요?

* **탄력적으로 확장 가능한 처리량 및 저장소:** 응용 프로그램 요구를 충족하기 위해 쉽게 MongoDB 데이터베이스를 강화하거나 규모를 축소합니다. 데이터는 예측 가능한 낮은 대기 시간을 위해 SSD(반도체 드라이브)에 저장됩니다. Cosmos DB는 거의 무제한 저장소 크기 및 프로비전된 처리량으로 확장될 수 있는 MongoDB 컬렉션을 지원합니다. 응용 프로그램 증가에 따라 탄력적으로 Cosmos DB를 확장하고 매끄럽게 예측 가능한 성능을 얻을 수 있습니다. 

* **다중 지역 복제:** Cosmos DB는 MongoDB 계정과 연결한 모든 지역에 투명하게 데이터를 복제하여 해당 보증을 포함한 일관성, 가용성과 성능 사이의 균형을 제공하는 한편 데이터에 대한 전역 액세스를 필요로 하는 응용 프로그램을 개발할 수 있도록 합니다. Cosmos DB는 multi-homing API를 사용하는 투명한 지역 장애 조치 및 전 세계에 걸쳐 처리량 및 저장소 크기를 탄력적으로 조정하는 기능을 제공합니다. [데이터를 글로벌 배포](documentdb-distribute-data-globally.md)에 대한 자세한 정보

**MongoDB 호환성**: 기존 MongoDB 전문 지식, 응용 프로그램 코드 및 도구를 사용할 수 있습니다. MongoDB를 사용하여 응용 프로그램을 개발하고 완전히 관리되는 전역으로 분산된 Cosmos DB 서비스를 사용하여 프로덕션에 배포할 수 있습니다.

**서버 관리 없음**: MongoDB 데이터베이스를 관리하고 크기를 조정할 필요가 없습니다. Cosmos DB는 완벽하게 관리되는 서비스이므로 인프라 또는 Virtual Machines를 직접 관리할 필요가 없습니다. Cosmos DB는 30개 이상의 [Azure 지역](https://azure.microsoft.com/regions/services/)에서 사용할 수 있습니다.

* **튜닝 가능한 일관성 수준:** 잘 정의된 5가지 일관성 수준에서 선택하여 일관성과 성능 간의 최적 절충을 실현합니다. 쿼리 및 읽기 작업에 대해 Cosmos DB는 강력, 제한된 부실, 세션, 일관된 접두사, 최종의 5가지 일관성 수준을 제공합니다. 이러한 잘 정의된 세부적인 일관성 수준을 통해 일관성, 가용성 및 대기 시간 간에 타당한 절충을 수행할 수 있습니다. [일관성 수준을 사용하여 가용성 및 성능 최대화](documentdb-consistency-levels.md)에서 자세히 알아보세요.

* **자동 인덱싱:** Cosmos DB는 기본적으로 MongoDB 데이터베이스에서 문서 내의 모든 속성을 자동으로 인덱싱하고 스키마 또는 보조 인덱스 생성을 예상하거나 요구하지 않습니다.

**엔터프라이즈급** - Azure Cosmos DB는 로컬 및 하위 지역 장애에 대비하여 99.99%의 가용성 및 데이터 보호 기능을 제공하는 다중 로컬 복제본을 지원합니다. Azure Cosmos DB에는 엔터프라이즈급 [규정 준수 인증서](https://www.microsoft.com/trustcenter) 및 보안 기능이 있습니다. 

Scott Hanselman과 Azure Cosmos DB 수석 엔지니어링 관리자 Kirill Gavrylyuk이 진행하는 이 Azure Friday 비디오를 통해 자세한 내용을 알아보세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 

## <a name="how-to-get-started"></a>시작하는 방법

[Azure Portal](https://portal.azure.com)에서 Azure Cosmos DB 계정을 만들고 MongoDB 연결 문자열을 새 계정으로 바꿉니다. 

*이것으로 끝입니다!*

자세한 지침에 대해서는 [계정 만들기](documentdb-create-mongodb-account.md) 및 [계정에 연결](documentdb-connect-mongodb-account.md)을 따르세요.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 MongoDB API에 대한 정보는 전체 Azure Cosmos DB 설명서에 통합되어 있지만 여기서는 시작하기 위한 몇 가지 사항을 다룹니다.

* [MongoDB 계정에 연결](documentdb-connect-mongodb-account.md) 자습서에 따라 계정 연결 문자열 정보를 가져오는 방법을 알아봅니다.
* [Azure Cosmos DB로 MongoChef 사용](documentdb-mongodb-mongochef.md) 자습서를 따라 MongoChef에서 Azure Cosmos DB 데이터베이스와 MongoDB 앱 간에 연결을 만드는 방법을 알아봅니다.
* [MongoDB에 대한 프로토콜 지원을 사용하여 데이터를 Azure Cosmos DB로 마이그레이션](documentdb-mongodb-migrate.md) 자습서를 따라 데이터를 MongoDB API 데이터베이스로 가져옵니다.
* [Node.js](documentdb-mongodb-samples.md)를 사용하여 첫 번째 MongoDB API 앱을 빌드합니다.
* [.NET](documentdb-mongodb-application.md)를 사용하여 첫 번째 MongoDB API 웹앱을 빌드합니다.
* [Robomongo](documentdb-mongodb-robomongo.md)를 사용하여 MongoDB API 계정에 연결합니다.
* [GetLastRequestStatistics 명령 및 Azure Portal 메트릭](documentdb-request-units.md#GetLastRequestStatistics)을 사용하여 작업에서 사용하는 RU 수에 대해 알아봅니다.
* [전역에 분산되는 앱에 대한 읽기 기본 설정을 구성](../cosmos-db/tutorial-global-distribution-mongodb.md)하는 방법을 알아봅니다.

