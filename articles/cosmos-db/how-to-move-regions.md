---
title: Azure Cosmos DB 계정을 다른 지역으로 이동 하는 방법을 알아봅니다.
description: Azure Cosmos DB 계정을 다른 지역으로 이동 하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059367"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Azure Cosmos DB 계정을 다른 지역으로 이동 하는 방법

이 문서에서는 Azure Cosmos DB에서 데이터가 복제 되는 지역을 이동 하거나 한 지역에서 다른 지역으로 데이터를 마이그레이션하는 방법, 즉 계정 (Azure Resource Manager) 메타 데이터를 마이그레이션하는 방법에 대해 설명 합니다.

## <a name="move-data-from-one-region-to-another"></a>한 지역에서 다른 지역으로 데이터 이동

Azure Cosmos DB은 데이터 복제를 기본적으로 지원 하므로 한 지역에서 다른 지역으로 데이터를 이동 하는 것은 간단 하며 Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여 수행할 수 있으며 다음 단계를 포함 합니다.

1. 계정에 새 지역 추가

    Azure Cosmos DB 계정에 새 지역을 추가 하려면 [Azure Cosmos DB 계정에 하위 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account) 를 참조 하세요.

1. 새 지역으로 수동 장애 조치 (failover) 수행

    제거 중인 지역이 현재 계정의 쓰기 영역인 경우 위에 추가 된 새 지역에 대 한 장애 조치 (failover)를 시작 해야 합니다. 가동 중지 시간이 0입니다. 다중 지역 계정에서 읽기 영역을 이동 하는 경우이 단계를 건너뛸 수 있습니다. 장애 조치 (failover)를 시작 하려면 [Azure Cosmos 계정에서 수동 장애 조치 (failover) 수행](how-to-manage-database-account.md#manual-failover) 을 참조 하세요.

1. 원래 영역 제거

    Azure Cosmos DB 계정에서 영역을 제거 하려면 [Azure Cosmos DB 계정에 하위 지역 추가/제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account) 를 참조 하세요.

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>계정 메타 데이터 Azure Cosmos DB 마이그레이션

Azure Cosmos DB은 기본적으로 한 지역에서 다른 지역으로 계정 메타 데이터 마이그레이션을 지원 하지 않습니다. 계정 메타 데이터와 고객 데이터를 한 지역에서 다른 지역으로 마이그레이션하려면 새 계정을 원하는 지역에 만들어야 합니다. 그러면 데이터를 수동으로 복사 해야 합니다. SQL API에 대 한 가동 중지 시간이 0 이면 [Changefeed](change-feed.md) 나이를 활용 하는 도구를 사용 해야 합니다. MongoDB API, Cassandra 또는 기타 API를 마이그레이션하거나 계정 간에 데이터를 마이그레이션하는 옵션에 대해 자세히 알아보려면 [온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB로 마이그레이션하는 옵션](cosmosdb-migrationchoices.md)을 참조 하세요. 아래 단계에서는 SQL API 및 해당 데이터에 대 한 Azure Cosmos DB 계정을 한 지역에서 다른 지역으로 마이그레이션하는 방법을 보여 줍니다.

1. 원하는 지역에 새 Azure Cosmos DB 계정 만들기

    Azure Portal, PowerShell 또는 CLI를 통해 새 계정을 만들려면 [Azure Cosmos DB 계정 만들기](how-to-manage-database-account.md#create-an-account)를 참조 하세요.

1. 새 데이터베이스 및 컨테이너 만들기

    새 데이터베이스 및 컨테이너를 만들려면 [Azure Cosmos 컨테이너 만들기](how-to-create-container.md) 를 참조 하세요.

1. Azure Cosmos DB Live Data Migrator tool을 사용 하 여 데이터 마이그레이션

    가동 중지 시간이 거의 없는 데이터를 마이그레이션하려면 [Azure Cosmos DB Live Data Migrator tool](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator) 을 참조 하세요.

1. 응용 프로그램 연결 문자열 업데이트

    라이브 migrator 도구를 계속 실행 하 여 응용 프로그램의 새 배포에서 연결 정보를 업데이트 합니다. 응용 프로그램의 끝점과 키는 Azure Portal에서 검색할 수 있습니다.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure Portal에서 액세스 제어(IAM) - NoSQL 데이터베이스 보안 설명":::

1. 새 응용 프로그램으로 요청 리디렉션

    새 응용 프로그램이 Azure Cosmos DB에 연결 되 면 클라이언트 요청을 새 배포로 리디렉션할 수 있습니다.

1. 더 이상 필요 하지 않은 리소스를 삭제 합니다.

    이제 요청이 새 인스턴스로 완전히 리디렉션되면 이전 Azure Cosmos DB 계정 및 Live Data Migrator tool을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

데이터베이스 및 컨테이너와 함께 Azure Cosmos 계정을 관리하는 방법에 대한 자세한 내용 및 예제는 다음 문서를 읽어보세요.

* [Azure Cosmos 계정 관리](how-to-manage-database-account.md)
* [Azure Cosmos DB에서 피드 변경](change-feed.md)
