---
title: Table API의 Azure Cosmos DB 글로벌 배포 자습서
description: Table API를 사용하여 Azure Cosmos DB 전역 배포를 설정하는 방법을 알아봅니다.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/13/2018
ms.reviewer: sngun
ms.openlocfilehash: 372f2ffc6b64cefb93f8dfa17e1ba752fd81b538
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66477857"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Table API를 사용하여 Azure Cosmos DB 전역 배포 설정

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * [Table API](table-introduction.md)를 사용하여 전역 배포 구성

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Table API를 사용하여 기본 설정 지역에 연결

[전역 배포](distribute-data-globally.md)를 활용하기 위해 클라이언트 응용 프로그램은 문서 작업을 수행하는 데 사용할 정렬된 기본 지역 목록을 지정할 수 있습니다. [TableConnectionPolicy.PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.table.tableconnectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_CosmosDB_Table_TableConnectionPolicy_PreferredLocations) 속성을 설정하여 이 작업을 수행할 수 있습니다. Azure Cosmos DB Table API SDK는 계정 구성, 현재 가용성 및 제공된 기본 설정 목록에 따라 최상의 엔드포인트를 선택하여 통신합니다.

PreferredLocations에는 읽기에 대해 기본 설정된(멀티 홈) 위치를 쉼표로 구분한 목록이 있어야 합니다. 각 클라이언트 인스턴스는 대기 시간이 짧은 읽기에 대해 기본 설정된 순서대로 이러한 지역의 하위 집합을 지정할 수 있습니다. 지역 이름은 [표시 이름](https://msdn.microsoft.com/library/azure/gg441293.aspx)(예: `West US`)을 사용하여 지정해야 합니다.

모든 읽기는 PreferredLocations 목록에서 사용 가능한 첫 번째 지역으로 보냅니다. 요청이 실패하면 클라이언트는 목록의 다음 지역으로 옮겨갑니다.

SDK는 PreferredLocations에 지정된 지역에서 읽기를 시도합니다. 따라서 가령 데이터베이스 계정이 3개 지역에서 사용할 수 있지만 클라이언트는 PreferredLocations에 쓰기에 해당하지 않는 지역 중 두 가지만 지정했다면, 장애 조치 시에도 쓰기 지역에서 읽기를 제공하지 않습니다.

SDK는 현재 쓰기 지역에 모든 쓰기를 자동으로 보냅니다.

PreferredLocations 속성이 설정되지 않는다면 모든 요청은 현재 쓰기 지역에서 제공됩니다.

이것으로 끝이며, 이 자습서를 완료했습니다! [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하여 전역적으로 복제한 계정의 일관성을 관리하는 방법에 대해 알아볼 수 있습니다. 그리고 Azure Cosmos DB에서 전역 데이터베이스 복제가 작동하는 방법에 대한 자세한 내용은 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포](distribute-data-globally.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * Azure Cosmos DB Table API를 사용하여 전역 배포를 설정하는 방법

