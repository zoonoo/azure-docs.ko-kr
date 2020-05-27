---
title: Azure Cosmos DB에 대한 Azure PowerShell 샘플 - MongoDB API
description: Azure PowerShell 샘플을 가져와 Azure Cosmos DB의 API for MongoDB에서 다양한 일반 작업 수행
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: c8e0a7a60a3512d19a1dfdfdb07b20e523ce7b92
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649722"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB에 대한 Azure PowerShell 샘플 - MongoDB API

다음 표에는 MongoDB API용 Azure Cosmos DB를 위한 샘플 Azure PowerShell 스크립트에 대한 링크가 포함되어 있습니다.

> [!NOTE]
> 현재 PowerShell, CLI 및 Resource Manager 템플릿을 사용하여 Azure Cosmos DB의 API for MongoDB 3.2 버전 계정(즉, `*.documents.azure.com` 형식의 엔드포인트를 사용하는 계정)만 만들 수 있습니다. 3\.6 버전의 계정을 만들려면 Azure Portal을 대신 사용하세요.

> [!NOTE]
> 이 샘플에서는 [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 관리 cmdlet을 사용합니다. `Az.CosmosDB`에 대한 업데이트를 정기적으로 확인하세요.

| | |
|---|---|
|[계정, 데이터베이스 및 컬렉션 만들기](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정, 데이터베이스 및 컬렉션을 만듭니다. |
|[데이터베이스 또는 컬렉션 나열 또는 가져오기](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컬렉션을 나열하거나 가져옵니다. |
|[RU/s 가져오기](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컬렉션에 대한 RU/s를 가져옵니다. |
|[RU/s 업데이트](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컬렉션에 대한 RU/s를 업데이트합니다. |
|[계정을 업데이트 또는 지역 추가](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos 계정에 지역을 추가합니다. 다른 계정 속성을 수정하는 데도 사용할 수 있지만 이러한 속성은 지역에 대한 변경 내용과 구분되어야 합니다. |
|[장애 조치(failover) 우선순위 변경 또는 장애 조치(failover) 트리거](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정의 장애 조치(failover) 우선순위를 변경하거나 수동 장애 조치(failover)를 트리거합니다. |
|[계정 키 또는 연결 문자열](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 기본 및 보조 키, 연결 문자열을 가져오거나 Azure Cosmos 계정의 계정 키를 다시 생성합니다. |
|[IP 방화벽을 사용하여 Cosmos 계정 만들기](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 활성화된 IP 방화벽을 사용하여 Azure Cosmos 계정을 만듭니다. |
|||
