---
title: Azure Cosmos DB 계정을 다른 지역으로 이동
description: Azure Cosmos DB 계정을 다른 지역으로 이동 하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: b34bc81f48b806b1016fbbd19d3ebc8bfef908c2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090536"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Azure Cosmos DB 계정을 다른 지역으로 이동

이 문서에서는 다음 중 하나를 수행 하는 방법을 설명 합니다.

- 데이터가 복제 되는 영역을 Azure Cosmos DB로 이동 합니다.
- 한 지역에서 다른 지역으로 계정 (Azure Resource Manager) 메타 데이터 및 데이터를 마이그레이션합니다.

## <a name="move-data-from-one-region-to-another"></a>한 지역에서 다른 지역으로 데이터 이동

Azure Cosmos DB은 데이터 복제를 기본적으로 지원 하므로 한 지역에서 다른 지역으로 데이터를 이동 하는 것은 간단 합니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여이를 수행할 수 있습니다. 이 작업에는 다음 단계가 포함 됩니다.

1. 계정에 새 지역을 추가 합니다.

    Azure Cosmos DB 계정에 새 지역을 추가 하려면 [Azure Cosmos DB 계정에 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)를 참조 하세요.

1. 새 지역으로 수동 장애 조치 (failover)를 수행 합니다.

    제거할 지역이 현재 계정의 쓰기 영역인 경우 이전 단계에서 추가 된 새 지역으로 장애 조치 (failover)를 시작 해야 합니다. 가동 중지 시간이 0 인 작업입니다. 여러 지역 계정에서 읽기 영역을 이동 하는 경우이 단계를 건너뛸 수 있습니다. 
    
    장애 조치 (failover)를 시작 하려면 [Azure Cosmos 계정에서 수동 장애 조치 (failover) 수행](how-to-manage-database-account.md#manual-failover)을 참조 하세요.

1. 원래 영역을 제거 합니다.

    Azure Cosmos DB 계정에서 영역을 제거 하려면 [Azure Cosmos DB 계정에서 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)를 참조 하세요.

## <a name="migrate-azure-cosmos-db-account-metadata"></a>계정 메타 데이터 마이그레이션 Azure Cosmos DB

Azure Cosmos DB은 기본적으로 한 지역에서 다른 지역으로 계정 메타 데이터 마이그레이션을 지원 하지 않습니다. 계정 메타 데이터와 고객 데이터를 한 지역에서 다른 지역으로 마이그레이션하려면 원하는 지역에 새 계정을 만든 다음 데이터를 수동으로 복사 해야 합니다. 

SQL API에 대 한 가동 중지 시간이 0에 가까운 마이그레이션에는 [변경 피드](change-feed.md) 또는이를 사용 하는 도구를 사용 해야 합니다. MongoDB API, Cassandra API 또는 다른 API를 마이그레이션하거나 계정 간에 데이터를 마이그레이션하는 옵션에 대해 자세히 알아보려면 [온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB로 마이그레이션하는 옵션](cosmosdb-migrationchoices.md)을 참조 하세요. 

다음 단계에서는 SQL API 및 해당 데이터에 대 한 Azure Cosmos DB 계정을 한 지역에서 다른 지역으로 마이그레이션하는 방법을 보여 줍니다.

1. 원하는 지역에 새 Azure Cosmos DB 계정을 만듭니다.

    Azure Portal, PowerShell 또는 Azure CLI를 통해 새 계정을 만들려면 [Azure Cosmos DB 계정 만들기](how-to-manage-database-account.md#create-an-account)를 참조 하세요.

1. 새 데이터베이스 및 컨테이너를 만듭니다.

    새 데이터베이스 및 컨테이너를 만들려면 [Azure Cosmos 컨테이너 만들기](how-to-create-container.md)를 참조 하세요.

1. Azure Cosmos DB Live Data Migrator 도구를 사용 하 여 데이터를 마이그레이션합니다.

    가동 중지 시간이 거의 없이 데이터를 마이그레이션하려면 [Azure Cosmos DB Live Data Migrator tool](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)을 참조 하세요.

1. 응용 프로그램 연결 문자열을 업데이트 합니다.

    라이브 데이터 Migrator 도구를 계속 실행 하 여 응용 프로그램의 새 배포에서 연결 정보를 업데이트 합니다. Azure Portal에서 응용 프로그램에 대 한 끝점 및 키를 검색할 수 있습니다.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="NoSQL 데이터베이스 보안을 보여 주는 Azure Portal의 액세스 제어":::

1. 요청을 새 응용 프로그램으로 리디렉션합니다.

    새 응용 프로그램이 Azure Cosmos DB에 연결 된 후 클라이언트 요청을 새 배포로 리디렉션할 수 있습니다.

1. 더 이상 필요 하지 않은 리소스를 삭제 합니다.

    이제 요청이 새 인스턴스로 완전히 리디렉션되면 이전 Azure Cosmos DB 계정 및 Live Data Migrator tool을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정 뿐 아니라 데이터베이스 및 컨테이너를 관리 하는 방법에 대 한 자세한 내용 및 예제는 다음 문서를 참조 하세요.

* [Azure Cosmos 계정 관리](how-to-manage-database-account.md)
* [Azure Cosmos DB에서 피드 변경](change-feed.md)
