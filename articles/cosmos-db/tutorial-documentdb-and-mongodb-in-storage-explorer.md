---
title: "Azure Storage 탐색기에서 Azure Cosmos DB 관리"
description: "Azure Storage 탐색기에서 Azure Cosmos DB를 관리하는 방법을 알아봅니다."
Keywords: "Azure Cosmos DB, Azure Storage 탐색기, DocumentDB, MongoDB, DocumentDB"
services: cosmos-db
documentationcenter: 
author: Jiaj-Li
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: Jiaj-Li
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2cd3656156b77c71be85a1a18567232f4466fc68
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Azure Storage 탐색기에서 Azure Cosmos DB 관리(미리 보기)

Azure Storage 탐색기에서 Azure Cosmos DB를 사용하면 사용자는 Azure Cosmos DB 엔터티를 관리하고, 데이터를 조작하고, Storage Blob 및 큐와 같은 다른 Azure 엔터티와 함께 저장 프로시저 및 트리거를 업데이트할 수 있게 됩니다. 이제 동일한 도구를 사용하여 다양한 Azure 엔터티를 한 곳에서 관리할 수 있습니다. 현재 Azure Storage 탐색기는 SQL(DocumentDB) 및 MongoDB 계정을 지원합니다.

이 문서에서는 Storage 탐색기를 사용하여 Azure Cosmos DB를 관리하는 방법을 배울 수 있습니다.


## <a name="prerequisites"></a>필수 조건

- SQL(DocumentDB) 또는 MongoDB 데이터베이스에 대한 Azure Cosmos DB 계정. 계정이 없는 경우 [Azure Cosmos DB: Build a DocumentDB API web app with .NET and the Azure portal](create-documentdb-dotnet.md)(Azure Cosmos DB: .NET 및 Azure Portal을 사용하여 DocumentDB API 웹앱 빌드)의 설명에 따라 Azure Portal에서 계정을 만들 수 있습니다.
- 최신 Azure Storage 탐색기 BITS를 설치합니다. [Linux](https://go.microsoft.com/fwlink/?linkid=858559), [Mac](https://go.microsoft.com/fwlink/?linkid=858561), [Windows](https://go.microsoft.com/fwlink/?linkid=858562)용 링크를 사용하여 설치할 수 있습니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

1. **Azure Storage 탐색기**를 설치한 후 왼쪽에 있는 **플러그 인** 아이콘을 클릭합니다. 다음 이미지를 참조하세요.
       
   ![플러그 인 아이콘](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. **Azure 계정 추가**를 선택하고 **로그인**을 클릭합니다.

   ![Azure 구독에 연결](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. **Azure 로그인** 대화 상자에서 **로그인**을 선택한 후에 Azure 자격 증명을 입력합니다.

    ![로그인](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. 목록에서 구독을 선택하고 **적용**을 클릭합니다.

    ![적용](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    탐색기 창이 업데이트되고, 선택한 구독에서 계정이 표시됩니다.

    ![계정 목록](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    **Azure Cosmos DB 계정**을 Azure 구독에 연결했습니다.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB에 연결

Azure Cosmos DB에 연결하는 또 다른 방법은 연결 문자열을 사용하는 것입니다. 연결 문자열을 사용하여 연결하려면 다음 단계를 따르세요.

1. 왼쪽 트리에서 **Local and Attached**(로컬 및 연결)를 찾아 **Azure Cosmos DB 계정**을 마우스 오른쪽 단추로 클릭한 후 **Connect to Azure Cosmos DB...**(Azure Cosmos DB에 연결...)를 선택합니다.

    ![연결 문자열을 사용하여 Azure Cosmos DB에 연결](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. 계정 유형에 적합한 **Default Experience**(기본 환경)를 **DocumentDB** 또는 **MongoDB** 중에서 선택하여 **연결 문자열**에 붙여넣은 후에 **확인**을 클릭하여 Azure Cosmos DB 계정을 연결합니다. 연결 문자열 검색에 대한 자세한 내용은 [Get the connection string](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string)(연결 문자열 가져오기)을 참조하세요.

    ![연결 문자열](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 리소스 관리

다음을 수행하여 Azure Cosmos DB 계정을 관리할 수 있습니다.
* Azure Portal에서 계정 열기
* 빠른 액세스 목록에 리소스 추가
* 리소스 검색 및 새로 고침
* 데이터베이스 만들기 및 삭제
* 컬렉션 만들기 및 삭제
* 문서 만들기, 편집, 삭제 및 필터링
* 저장 프로시저, 트리거 및 사용자 정의 함수 관리

### <a name="quick-access-tasks"></a>빠른 액세스 작업

탐색기 창에서 구독을 마우스 오른쪽 단추로 클릭하여 다양한 바로 가기 작업을 수행할 수 있습니다.

* Azure Cosmos DB 계정 또는 데이터베이스를 마우스 오른쪽 단추로 클릭하여 **포털에서 열기**를 선택하면 Azure Portal을 통해 브라우저에서 리소스를 관리할 수 있습니다.

     ![포털에서 열기](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* Azure Cosmos DB 계정, 데이터베이스, 컬렉션 등을 **빠른 액세스**에 추가할 수도 있습니다.
* **여기에서 검색**을 선택하면 선택한 경로에서 키워드 검색이 가능합니다.

    ![여기에서 검색](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>데이터베이스 및 컬렉션 관리
#### <a name="create-a-database"></a>데이터베이스 만들기 
Azure Cosmos DB 계정을 마우스 오른쪽 단추로 클릭하고 **데이터베이스 만들기**를 선택하고 데이터베이스 이름을 입력한 후 **Enter** 키를 눌러 완료합니다.

![데이터베이스 만들기](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>데이터베이스 삭제
데이터베이스를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 삭제**를 클릭한 후 팝업 창에서 **예**를 클릭합니다. 데이터베이스 노드가 삭제되고, Azure Cosmos DB 계정이 자동으로 새로 고쳐집니다.

![database1 삭제](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![database2 삭제](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>컬렉션 만들기
데이터베이스를 마우스 오른쪽 단추로 클릭하고 **컬렉션 만들기**를 선택한 후 **컬렉션 ID**, **저장소 용량**과 같은 정보를 제공합니다. **확인**을 클릭하여 마칩니다. 파티션 키 설정에 대한 자세한 내용은 [Design for partitioning](partition-data.md#designing-for-partitioning)(분할 설계)을 참조하세요.

컬렉션을 만들 때 파티션 키가 사용된 경우, 컬렉션을 만들고 나면 컬렉션에 대해 파티션 키 값을 변경할 수 없습니다.

![collection1 만들기](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![collection2 만들기](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>컬렉션 삭제
컬렉션을 마우스 오른쪽 단추로 클릭하고 **컬렉션 삭제**를 클릭한 후 팝업 창에서 **예**를 클릭합니다. 

컬렉션 노드가 삭제되고, 데이터베이스가 자동으로 새로 고쳐집니다.  

![컬렉션 삭제](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>문서 관리

#### <a name="create-and-modify-documents"></a>문서 만들기 및 수정
새 문서를 만들려면 왼쪽 창에서 **문서**를 연 다음 **새 문서**를 클릭하고 오른쪽 창에서 내용을 편집한 후 **저장**을 클릭합니다. 기존 문서를 업데이트한 후 **저장**을 클릭할 수도 있습니다. **취소**를 클릭하여 변경 내용을 취소할 수 있습니다.

![문서](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>문서 삭제
선택한 문서를 삭제하려면 **삭제** 단추를 클릭합니다.
#### <a name="query-for-documents"></a>문서에 대한 쿼리
[SQL 쿼리](documentdb-sql-query.md)를 입력하고 **적용**을 클릭하여 문서 필터를 편집합니다.

![Filter](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>저장 프로시저, 트리거 및 UDF 관리
* 저장 프로시저를 만들려면 왼쪽 트리에서 **저장 프로시저**를 마우스 오른쪽 단추로 클릭하고 **저장 프로시저 만들기**를 선택한 후에 왼쪽에 이름을, 오른쪽 창에 저장 프로시저 스크립트를 입력한 다음 **만들기**를 클릭합니다. 
* 기존 저장 프로시저를 편집할 수도 있습니다. 기존 저장 프로시저를 두 번 클릭하고 업데이트한 다음 **업데이트**를 클릭하여 저장하거나 **취소**를 클릭하여 변경 내용을 취소하면 됩니다.

![저장 프로시저](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* **트리거** 및 **UDF**에 대한 작업은 **저장 프로시저**에 대한 작업과 비슷합니다.

## <a name="demo"></a>데모
* Azure Storage 탐색기에서 Azure Cosmos DB를 사용하는 방법에 대해 설명하는 다음 비디오를 시청하세요. [Use Azure Cosmos DB in Azure Storage Explorer](https://go.microsoft.com/fwlink/?linkid=858710)(Azure Storage 탐색기에서 Azure Cosmos DB 사용)

## <a name="next-steps"></a>다음 단계

Azure Storage 탐색기 계정을 Azure Cosmos DB 계정에 연결했습니다. 이제 Storage 탐색기에 대해 자세히 알아보고 [Get started with Storage Explorer (Preview)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)(Storage 탐색기 시작(미리 보기))에서 추가 서비스를 연결해 보겠습니다.


