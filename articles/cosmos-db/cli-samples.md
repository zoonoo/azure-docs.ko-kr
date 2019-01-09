---
title: Azure Cosmos DB에 대한 Azure CLI 샘플
description: Azure CLI 샘플 - Azure Cosmos DB 계정, 데이터베이스, 컨테이너, 하위 지역 및 방화벽을 만들고 관리합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 3d8ba7c89f320344081ed8f3a722883c3234fa90
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53810987"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 Azure CLI 샘플

다음 표는 Azure Cosmos DB의 Azure CLI 샘플 스크립트에 대한 링크를 포함합니다. 모든 Azure Cosmos DB CLI 명령에 대한 참조 페이지는 [Azure CLI 참조](/cli/azure/cosmosdb)에서 제공됩니다.

| |  |
|---|---|
|**Azure Cosmos DB 계정, 데이터베이스 및 컨테이너 만들기**||
| [SQL API를 사용하여 Azure Cosmos DB 계정 만들기](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 단일 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다. |
| [Cosmos DB의 MongoDB API를 사용하여 Azure Cosmos DB 계정 만들기](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 단일 Azure Cosmos DB 계정, 데이터베이스 및 컬렉션을 만듭니다. |
| [Gremlin API를 사용하여 Azure Cosmos DB 계정 만들기](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 단일 Azure Cosmos DB 계정, 데이터베이스 및 그래프를 만듭니다. |
| [Cassandra API를 사용하여 Azure Cosmos DB 계정 만들기](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 단일 Azure Cosmos DB 계정 및 데이터베이스를 만듭니다. |
| [Table API를 사용하여 Azure Cosmos DB 계정 만들기](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 단일 Azure Cosmos DB 계정, 데이터베이스 및 테이블을 만듭니다. |
|**Azure Cosmos DB 확장**||
| [컨테이너 처리량 확장](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 컨테이너에서 프로비전되는 처리량을 변경합니다.|
| [Azure Cosmos DB 데이터베이스 계정을 여러 하위 지역에서 복제 및 장애 조치 우선 순위 구성](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|지정된 장애 조치 우선 순위를 사용해서 계정 데이터를 여러 하위 지역으로 전체적으로 복제합니다.|
|**Azure Cosmos DB 보안 유지**||
| [계정 키 가져오기](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 계정에 대한 기본 및 보조 마스터 쓰기 키와 기본 및 보조 읽기 전용 키를 가져옵니다.|
| [Azure Cosmos DB의 MongoDB API로 구성된 Cosmos 계정에 대한 연결 문자열 가져오기](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | MongoDB 앱을 Azure Cosmos DB 계정에 연결하기 위한 연결 문자열을 가져옵니다.|
| [계정 키 재생성](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|계정 키를 다시 생성합니다.|
| [방화벽 만들기](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 승인된 머신 및/또는 클라우드 서비스 집합에서 계정에 대한 액세스를 제한하기 위한 인바운드 IP 액세스 제어 정책을 만듭니다.|
|**고가용성, 재해 복구, 백업 및 복원**||
| [장애 조치 정책 구성](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|계정이 복제되는 각 하위 지역의 장애 조치 우선 순위를 설정합니다.|
|**Azure Cosmos DB를 리소스에 연결**||
| [웹앱을 Azure Cosmos DB에 연결](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Azure Cosmos DB 데이터베이스 및 Azure Web App을 만들고 연결합니다.|
|||
