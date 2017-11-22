---
title: "Table API의 Azure Cosmos DB 전역 배포 자습서 | Microsoft Docs"
description: "Table API를 사용하여 Azure Cosmos DB 전역 배포를 설정하는 방법에 대해 알아봅니다."
services: cosmos-db
keywords: "전역 배포, Table"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 93e75429d66a30bfc4588a3070e32d58eec0df4b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Table API를 사용하여 Azure Cosmos DB 전역 배포를 설정하는 방법

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * [Table API](table-introduction.md)를 사용하여 전역 배포 구성

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Table API를 사용하여 기본 설정 지역에 연결

[전역 배포](distribute-data-globally.md)를 활용하기 위해 클라이언트 응용 프로그램은 문서 작업을 수행하는 데 사용할 정렬된 기본 지역 목록을 지정할 수 있습니다. 이렇게 하려면 Azure Cosmos DB Table API SDK의 app.config에서 `TablePreferredLocations` 구성 값을 설정합니다. Azure Cosmos DB Table API SDK는 계정 구성, 현재 가용성 및 제공된 기본 설정 목록에 따라 최상의 끝점을 선택하여 통신합니다.

`TablePreferredLocations`에는 읽기에 대해 기본 설정된(멀티 홈) 위치를 쉼표로 구분한 목록이 있어야 합니다. 각 클라이언트 인스턴스는 대기 시간이 짧은 읽기에 대해 기본 설정된 순서대로 이러한 지역의 하위 집합을 지정할 수 있습니다. 지역 이름은 [표시 이름](https://msdn.microsoft.com/library/azure/gg441293.aspx)(예: `West US`)을 사용하여 지정해야 합니다.

모든 읽기는 `TablePreferredLocations` 목록에서 사용 가능한 첫 번째 지역으로 보내집니다. 요청이 실패하면 클라이언트는 목록의 다음 지역으로 옮겨갑니다.

SDK는 `TablePreferredLocations`에 지정된 지역에서 읽기를 시도합니다. 예를 들어 데이터베이스 계정이 세 지역에서 사용할 수 있지만, 클라이언트에서 `TablePreferredLocations`에 대해 쓰기에 해당하지 않는 영역 두 개만 지정하면 장애 조치 시에도 쓰기 지역에서 읽기를 제공하지 않습니다.

SDK는 현재 쓰기 지역에 모든 쓰기를 자동 전송합니다.

`TablePreferredLocations` 속성이 설정되지 않으면 모든 요청이 현재 쓰기 지역에서 제공됩니다.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

이것으로 끝이며, 이 자습서를 완료했습니다! [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하여 전역적으로 복제한 계정의 일관성을 관리하는 방법에 대해 알아볼 수 있습니다. 그리고 Azure Cosmos DB에서 전역 데이터베이스 복제가 작동하는 방법에 대한 자세한 내용은 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포](distribute-data-globally.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * Azure Cosmos DB Table API를 사용하여 전역 배포를 설정하는 방법

