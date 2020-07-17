---
title: '알려진 문제: MongoDB에서 Azure CosmosDB로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용 하 여 MongoDB에서 Azure Cosmos DB로의 마이그레이션에 대 한 알려진 문제 및 마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: ae5d5f2d282c546f5172ca1c8cb0e420d3b6e96b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80878054"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에서 MongoDB 용 API Azure Cosmos DB로의 마이그레이션에 대 한 알려진 문제/마이그레이션 제한 사항

MongoDB에서 Cosmos DB의 MongoDB API로의 마이그레이션과 관련 된 알려진 문제 및 제한 사항은 다음 섹션에 설명 되어 있습니다.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>잘못 된 SSL 인증서를 사용한 결과로 마이그레이션이 실패 합니다.

* **증상**: 사용자가 MongoDB 원본 서버에 연결할 수 없는 경우이 문제가 분명 합니다. 모든 방화벽 포트가 열려 있더라도 사용자는 여전히 연결할 수 없습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| Azure Database Migration Service에서 자체 서명 된 인증서를 사용 하면 잘못 된 SSL 인증서로 인해 마이그레이션이 실패할 수 있습니다. 오류 메시지에는 "유효성 검사 절차에 따르면 원격 인증서가 유효 하지 않습니다."가 포함 될 수 있습니다. | CA의 정품 인증서를 사용 합니다.  자체 서명 된 인증서는 일반적으로 내부 테스트 에서만 사용 됩니다. CA 기관에서 정품 인증서를 설치 하는 경우에는 문제가 없는 Azure Database Migration Service에서 SSL을 사용할 수 있습니다 (Cosmos DB에 연결 하 여 Mongo API를 통해 SSL 사용).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>DMS에서 매핑할 데이터베이스 목록을 가져올 수 없습니다.

* **증상**: **원본 선택** 블레이드에서 **Azure Storage 모드의 데이터** 를 사용 하는 경우 **데이터베이스 설정** 블레이드에서 DB 목록을 가져올 수 없습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 저장소 계정 연결 문자열에 SAS 정보가 없으므로 인증할 수 없습니다. | Storage 탐색기의 blob 컨테이너에서 SAS를 만들고 컨테이너 SAS 정보를 포함 하는 URL을 원본 정보 연결 문자열로 사용 합니다.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>지원 되지 않는 버전의 데이터베이스 사용

* **증상**: 마이그레이션이 실패 합니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 지원 되지 않는 버전의 MongoDB에서 Azure Cosmos DB로 마이그레이션해야 합니다. | MongoDB의 새 버전이 출시 되 면 Azure Database Migration Service와의 호환성을 보장 하는 테스트를 거쳤습니다. 최신 버전을 허용 하도록 서비스를 정기적으로 업데이트 합니다. 마이그레이션을 즉시 수행 해야 하는 경우에는 데이터베이스/컬렉션을 Azure Storage로 내보낸 다음 원본에서 결과 덤프를 가리키도록 할 수 있습니다. Storage 탐색기의 blob 컨테이너에서 SAS를 만든 다음, 원본 세부 정보 연결 문자열로 컨테이너 SAS 정보를 사용 하 여 URL을 사용 합니다.<br><br> |

## <a name="next-steps"></a>다음 단계

* [DMS를 사용 하 여 Azure Cosmos DB MongoDB에 대 한 MongoDB 용 API 온라인 마이그레이션](tutorial-mongodb-cosmos-db-online.md)자습서를 참조 하세요.
* [DMS를 사용 하 여 MongoDB에서 MONGODB API로 오프 라인 Azure Cosmos DB으로 마이그레이션](tutorial-mongodb-cosmos-db.md)자습서를 참조 하세요.