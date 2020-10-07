---
title: 포함 파일
description: 포함 파일
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "70020163"
---
이제 데이터 탐색기를 사용하여 새 컨테이너에 데이터를 추가할 수 있습니다.

1. **Data Explorer**에서 **Tasks** 데이터베이스를 확장하고 **Items** 컨테이너를 확장합니다. **항목**을 선택한 다음, **새 항목**을 선택합니다.

   ![Azure Portal의 데이터 탐색기에서 새 문서 만들기](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. 이제 다음과 같은 구조를 사용하여 컨테이너에 문서를 추가합니다.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. **문서** 탭에 json을 추가했으면 **저장**을 선택합니다.

    ![Azure Portal의 Azure Data Explorer에서 Json 데이터를 복사하고 저장을 선택합니다.](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  `id` 속성에 대한 고유한 값을 삽입하는 경우 둘 이상의 문서를 만들고 저장하며 다른 속성을 적합하게 변경합니다. Azure Cosmos DB가 데이터에 어떠한 스키마도 적용하지 않으므로 새 문서는 사용자가 원하는 어떠한 구조든 가질 수 있습니다.