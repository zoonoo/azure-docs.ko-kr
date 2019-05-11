---
title: Azure Cosmos DB에 대한 Azure PowerShell 샘플
description: Azure PowerShell 샘플 - Azure Cosmos DB 계정을 만들고 관리할 수 있도록 하는 스크립트입니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069294"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 Azure PowerShell 샘플

다음 표에는 Core용 Azure Cosmos DB(SQL) API를 위한 샘플 Azure PowerShell 스크립트에 대한 링크가 포함되어 있습니다.

| |  |
|---|---|
|**Azure Cosmos 계정**||
|[계정 만들기](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos SQL API 계정을 만듭니다. |
|[계정 가져오기](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정의 속성을 가져옵니다. |
|[지역 추가](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정을 가져오고 위치 목록에 하위 지역을 추가합니다. |
|[장애 조치(failover) 우선 순위 변경](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 수동 장애 조치(failover) 트리거를 사용하여 Azure Cosmos 계정의 장애 조치(failover) 우선 순위를 변경합니다. |
|[태그 업데이트](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정에 대한 태그를 업데이트합니다. |
|[계정 키 가져오기](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정의 기본 및 보조 키를 가져옵니다. |
|[계정 키 재생성](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정의 기본 및 보조 키를 다시 생성합니다. |
|[연결 문자열 나열](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정의 기본 및 보조 연결 문자열을 가져옵니다. |
|[IP 방화벽 만들기](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정에 대한 IP 방화벽을 만듭니다. |
|[Azure Cosmos 계정 삭제](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정을 삭제합니다. |
|**Azure Cosmos 데이터베이스**||
| [데이터베이스 만들기](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos 계정 내에서 데이터베이스를 만듭니다.|
| [공유/데이터베이스 수준 처리량을 사용하여 데이터베이스 만들기](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 해당 컨테이너와 공유되는 데이터베이스 수준 처리량을 사용하여 Azure Cosmos 데이터베이스를 만듭니다.|
| [모든 데이터베이스 나열](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos 계정에 있는 데이터베이스를 모두 나열합니다.|
| [데이터베이스 가져오기](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos 데이터베이스의 속성을 가져옵니다.|
|**Azure Cosmos 컨테이너**||
| [컨테이너 만들기](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 전용 처리량을 사용하여 Azure Cosmos 컨테이너를 만듭니다.|
| [공유 처리량을 사용하여 컨테이너 만들기](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 데이터베이스의 다른 컨테이너와 공유되는 처리량을 사용하여 Azure Cosmos 컨테이너를 만듭니다.|
| [인덱스 정책을 사용하여 컨테이너 만들기](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 사용자 지정 인덱스 정책을 사용하여 Azure Cosmos 컨테이너를 만듭니다.|
| [인덱스 정책이 없는 컨테이너 만들기](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 인덱스 정책을 해제한 Azure Cosmos 컨테이너를 만듭니다.|
| [고유 키 및 TTL을 사용하여 컨테이너 만들기](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 고유 키 제약 조건 및 TTL(Time-To-Live)이 구성된 Azure Cosmos 컨테이너를 만듭니다.|
| [충돌 해결을 사용하여 컨테이너 만들기](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 최종 작성자 인정(last-writer-wins) 충돌 해결 정책을 사용하여 Azure Cosmos 컨테이너를 만듭니다.|
| [모든 컨테이너 나열](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos 데이터베이스의 컨테이너를 모두 나열합니다.|
| [컨테이너 가져오기](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos 데이터베이스의 컨테이너에 대한 속성을 가져옵니다.|
| [컨테이너 삭제](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure Cosmos 데이터베이스의 컨테이너를 삭제합니다.|
|||