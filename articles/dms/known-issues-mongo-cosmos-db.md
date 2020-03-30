---
title: '알려진 문제: 몽고DB에서 Azure CosmosDB로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure 데이터베이스 마이그레이션 서비스를 사용하여 MongoDB에서 Azure Cosmos DB로 마이그레이션하는 알려진 문제 및 마이그레이션 제한 사항에 대해 알아봅니다.
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
ms.openlocfilehash: 194da036260a78b27748dfc7f755212ab4f30b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78256024"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에서 MongoDB에 대한 Azure 코스모스 DB의 API로 마이그레이션하는 알려진 문제/마이그레이션 제한

MongoDB에서 MongoDB에 대한 코스모스 DB의 API로의 마이그레이션과 관련된 알려진 문제와 제한 사항은 다음 섹션에 설명되어 있습니다.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>잘못된 SSL 인증서를 사용하면 마이그레이션이 실패합니다.

* **증상**: 사용자가 MongoDB 원본 서버에 연결할 수 없는 경우 이 문제가 분명합니다. 모든 방화벽 포트가 열려 있음에도 불구하고 사용자는 여전히 연결할 수 없습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| Azure 데이터베이스 마이그레이션 서비스에서 자체 서명된 인증서를 사용하면 잘못된 SSL Cert로 인해 마이그레이션이 실패할 수 있습니다. 오류 메시지에는 "유효성 검사 절차에 따라 원격 인증서가 유효하지 않습니다." | CA의 정품 인증서를 사용합니다.  자체 서명된 인증서는 일반적으로 내부 테스트에서만 사용됩니다. CA 기관에서 정품 인증서를 설치하면 Azure 데이터베이스 마이그레이션 서비스에서 SSL을 문제 없이 사용할 수 있습니다(Cosmos DB에 대한 연결은 Mongo API를 통해 SSL을 사용합니다).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>DMS에 매핑할 데이터베이스 목록을 얻을 수 없습니다.

* **증상**: **소스 선택** 블레이드에서 **Azure 저장소** 모드의 데이터를 사용할 때 **데이터베이스 설정** 블레이드에서 DB 목록을 얻을 수 없습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 저장소 계정 연결 문자열에 SAS 정보가 없으므로 인증할 수 없습니다. | 저장소 탐색기에서 Blob 컨테이너에 SAS를 만들고 소스 세부 정보 연결 문자열로 컨테이너 SAS 정보가 있는 URL을 사용합니다.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>지원되지 않는 버전의 데이터베이스 사용

* **증상**: 마이그레이션이 실패합니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 지원되지 않는 버전의 MongoDB에서 Azure 코스모스 DB로 마이그레이션하려고 합니다. | 새 버전의 MongoDB가 릴리스되면 Azure 데이터베이스 마이그레이션 서비스와의 호환성을 보장하기 위해 테스트되고 서비스가 주기적으로 업데이트되어 최신 버전을 수락합니다. 즉시 마이그레이션해야 하는 경우 해결 해결 으로 데이터베이스/컬렉션을 Azure Storage및 소스의 결과 덤프에 대한 지점을 내보낼 수 있습니다. 저장소 탐색기에서 Blob 컨테이너에 SAS를 만든 다음 컨테이너 SAS 정보가 있는 URL을 소스 세부 정보 연결 문자열로 사용합니다.<br><br> |

## <a name="next-steps"></a>다음 단계

* [DMS를 사용하여 MongoDB 를 Azure 코스모스 DB의 API로 마이그레이션하는](tutorial-mongodb-cosmos-db-online.md)자습서를 봅니다.
* [DMS를 사용하여 MongoDB를 오프라인으로 MongoDB에 대한 Azure 코스모스 DB의 API로 마이그레이션하는](tutorial-mongodb-cosmos-db.md)자습서를 봅니다.