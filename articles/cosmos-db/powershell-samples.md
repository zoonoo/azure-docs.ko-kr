---
title: Azure Cosmos DB에 대한 Azure PowerShell 샘플
description: Azure PowerShell 샘플 - Azure Cosmos DB 계정을 만들고 관리할 수 있도록 하는 스크립트입니다.
services: cosmos-db
author: SnehaGunda
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 480bccd73a049d2f181d5acf906882b2f9b6ccdb
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808403"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 Azure PowerShell 샘플

다음 표에는 Azure Cosmos DB의 Azure PowerShell 샘플 스크립트에 대한 링크가 나와 있습니다. 현재는 PowerShell을 통해 Azure Cosmos DB 계정만 관리할 수 있으며 데이터베이스와 컨테이너와 같은 기타 리소스는 PowerShell로 관리할 수 없습니다.

| |  |
|---|---|
|**Azure Cosmos DB 계정 만들기**||
|[SQL API를 사용하여 Cosmos 계정 만들기 및 구성](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| SQL API와 함께 사용할 단일 Azure Cosmos DB 계정을 만듭니다. |
|[Azure Cosmos DB의 MongoDB API를 사용하여 Cosmos 계정 만들기 및 구성](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB의 MongoDB API를 사용하여 단일 Cosmos 계정을 만듭니다. |
|[Gremlin API를 사용하여 Cosmos 계정 만들기 및 구성](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Gremlin API와 함께 사용할 단일 Azure Cosmos DB 계정을 만듭니다. |
|[Cassandra API를 사용하여 Cosmos 계정 만들기 및 구성](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cassandra API와 함께 사용할 단일 Azure Cosmos DB 계정을 만듭니다. |
|[Table API를 사용하여 Cosmos 계정 만들기 및 구성](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Table API와 함께 사용할 단일 Azure Cosmos DB 계정을 만듭니다. |
|**Azure Cosmos DB 확장**||
|[Azure Cosmos DB 계정을 여러 하위 지역에서 복제 및 장애 조치 우선 순위 구성](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|지정된 장애 조치 우선 순위를 사용해서 계정 데이터를 여러 하위 지역으로 전체적으로 복제합니다.|
|**Azure Cosmos DB 보안 유지**||
| [계정 키 가져오기](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 계정에 대한 기본 및 보조 마스터 쓰기 키와 기본 및 보조 읽기 전용 키를 가져옵니다.|
| [MongoDB 연결 문자열 가져오기](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | MongoDB 앱을 Azure Cosmos DB 계정에 연결하기 위한 연결 문자열을 가져옵니다.|
|[계정 키 재생성](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|계정에 대한 마스터 또는 읽기 전용 키를 재생성합니다.|
|[방화벽 만들기](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 승인된 컴퓨터 및/또는 클라우드 서비스 집합에서 계정에 대한 액세스를 제한하기 위한 인바운드 IP 액세스 제어 정책을 만듭니다.|
|**고가용성, 재해 복구, 백업 및 복원**||
|[장애 조치 정책 구성](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|계정이 복제되는 각 하위 지역의 장애 조치 우선 순위를 설정합니다.|
|||
