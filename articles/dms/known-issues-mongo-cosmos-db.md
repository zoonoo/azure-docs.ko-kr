---
title: '알려진 문제: MongoDB에서 Azure Cosmos DB로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 MongoDB에서 Azure Cosmos DB로 마이그레이션할 때의 알려진 문제와 마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/27/2020
ms.openlocfilehash: be6d9d3b8a20e11c874234baae26629ce0187e4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91291813"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에서 Azure Cosmos DB의 MongoDB API로의 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항

MongoDB에서 Cosmos DB의 MongoDB API로의 마이그레이션과 관련된 알려진 문제 및 제한 사항은 다음 섹션에 설명되어 있습니다.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>잘못된 SSL 인증서를 사용했기 때문에 마이그레이션이 실패합니다.

* **증상**: 사용자가 MongoDB 원본 서버에 연결할 수 없는 경우 이 문제가 분명합니다. 모든 방화벽 포트가 열려 있더라도 사용자는 여전히 연결할 수 없습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| Azure Database Migration Service에서 자체 서명된 인증서를 사용하면 잘못된 SSL 인증서로 인해 마이그레이션이 실패할 수 있습니다. 오류 메시지에는 "유효성 검사 절차에 따르면 원격 인증서가 유효하지 않습니다."가 포함될 수 있습니다. | CA의 정품 인증서를 사용합니다.  자체 서명된 인증서는 일반적으로 내부 테스트에서만 사용됩니다. CA 기관에서 정품 인증서를 설치하는 경우에는 문제 없이 Azure Database Migration Service에서 SSL을 사용할 수 있습니다(Cosmos DB에 연결할 경우 Mongo API를 통해 SSL 사용).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>DMS에 매핑할 데이터베이스 목록을 가져올 수 없음

* **증상**: **원본 선택** 블레이드에서 **Azure Storage의 데이터** 모드를 사용하는 경우 **데이터베이스 설정** 블레이드에서 DB 목록을 가져올 수 없습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 스토리지 계정 연결 설정에 SAS 정보가 누락되어 인증할 수 없습니다. | Storage Explorer를 사용하여 Blob 컨테이너에 SAS를 만들고, 컨테이너 SAS 정보를 원본 세부 연결 문자열로 사용하여 URL을 사용합니다.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>지원되지 않는 데이터베이스 버전 사용

* **증상**: 마이그레이션이 실패합니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 지원되지 않는 MongoDB 버전에서 Azure Cosmos DB로 마이그레이션하려고 합니다. | MongoDB의 새 버전이 출시되면 Azure Database Migration Service와의 호환성을 확인하기 위해 테스트되며 해당 서비스는 최신 버전을 허용하도록 정기적으로 업데이트됩니다. 마이그레이션을 즉시 수행해야 하는 경우에는 데이터베이스/컬렉션을 Azure Storage로 내보낸 다음, 원본이 결과 덤프를 가리키도록 할 수 있습니다. Storage Explorer를 사용하여 Blob 컨테이너에 SAS를 만들고, 컨테이너 SAS 정보를 원본 세부 연결 문자열로 사용하여 URL을 사용합니다.<br><br> |

## <a name="next-steps"></a>다음 단계

* 자습서 [DMS를 사용하여 온라인 방식으로 MongoDB를 Azure Cosmos DB의 MongoDB API로 마이그레이션](tutorial-mongodb-cosmos-db-online.md)을 참조하세요.
* 자습서 [DMS를 사용하여 오프라인 방식으로 MongoDB를 Azure Cosmos DB의 MongoDB API로 마이그레이션](tutorial-mongodb-cosmos-db.md)을 참조하세요.