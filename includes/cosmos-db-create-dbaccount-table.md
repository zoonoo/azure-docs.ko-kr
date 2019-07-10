---
title: 포함 파일
description: 포함 파일
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 4fec9be34e390498b85ecfcb3f3b61055a08fdd2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181894"
---
1. 새 브라우저 창에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 탐색 창에서 **리소스 만들기**를 선택합니다. **데이터베이스**를 선택한 다음, **Azure Cosmos DB**를 선택합니다.
   
   ![더 많은 서비스와 Azure Cosmos DB를 강조 표시한 Azure Portal의 스크린샷](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. **Azure Cosmos DB 계정 만들기** 페이지에서 새 Azure Cosmos DB 계정에 대한 설정을 입력합니다.
 
    설정|값|설명
    ---|---|---
    구독|사용자의 구독|이 Azure Cosmos DB 계정에 사용하려는 Azure 구독을 선택합니다. 
    리소스 그룹|새로 만들기<br><br>다음으로, ID에 제공된 것과 동일한 고유한 이름 입력|**새로 만들기**를 선택합니다. 그런 다음, 계정의 새 리소스 그룹 이름을 입력합니다. 간단히 하기 위해 ID와 동일한 이름을 사용합니다. 
    계정 이름|고유한 이름을 입력합니다.|내 Azure Cosmos DB 계정을 식별하는 고유한 이름을 입력합니다.<br><br>ID는 소문자, 숫자 및 하이픈(-) 문자만 사용할 수 있으며, 길이는 3~31자 사이여야 합니다.
    API|Azure 테이블|API는 만들 계정의 형식을 결정합니다. Azure Cosmos DB는 문서 데이터베이스용 Core(SQL), 그래프 데이터베이스용 Gremlin, 문서 데이터베이스용 MongoDB, Azure Table 및 Cassandra의 5가지 API를 제공합니다. 현재 각 API에 대한 별도의 계정을 만들어야 합니다. <br><br>이 빠른 시작에서는 Table API를 사용하는 테이블을 만들 예정이므로 **Azure 테이블**을 선택합니다. <br><br>[Table API에 대해 자세히 알아봅니다](../articles/cosmos-db/table-introduction.md).|
    위치|사용자와 가장 가까운 지역 선택|Azure Cosmos DB 계정을 호스트할 지리적 위치를 선택합니다. 데이터에 가장 빨리 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.

    RU 요금이 발생하지 않도록 **지리적 중복** 및 **다중 영역 쓰기** 옵션을 기본값(**사용 안 함**)으로 두어도 됩니다. **네트워크** 및 **태그** 섹션은 건너뛰어도 됩니다.

5. **검토+만들기**를 선택합니다. 유효성 검사가 완료되면 **만들기**를 선택하여 계정을 만듭니다. 
 
   ![Azure Cosmos DB에 대한 새 계정 페이지](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. 계정을 만드는 데 몇 분이 걸립니다. **배포가 진행 중**이라는 메시지가 표시될 것입니다. 배포가 완료될 때까지 기다렸다가 **리소스로 이동**을 선택합니다.

    ![Azure Portal 알림 창](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
