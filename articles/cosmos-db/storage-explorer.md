---
title: Azure Storage 탐색기에서 Azure Cosmos DB 관리
description: Azure Storage 탐색기에서 Azure Cosmos DB를 관리하는 방법을 알아봅니다.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: 18f580f1eae31c9bf3626e100217467bb48ca881
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Azure Storage 탐색기에서 Azure Cosmos DB 관리(미리 보기)

Azure Storage 탐색기에서 Azure Cosmos DB를 사용하면 사용자는 Azure Cosmos DB 엔터티를 관리하고, 데이터를 조작하고, Storage Blob 및 큐와 같은 다른 Azure 엔터티와 함께 저장 프로시저 및 트리거를 업데이트할 수 있게 됩니다. 이제 동일한 도구를 사용하여 다양한 Azure 엔터티를 한 곳에서 관리할 수 있습니다. 현재 Azure Storage 탐색기는 SQL, MongoDB, Graph 및 Table 계정을 지원합니다.

이 문서에서는 Storage 탐색기를 사용하여 Azure Cosmos DB를 관리하는 방법을 배울 수 있습니다.


## <a name="prerequisites"></a>필수 조건

SQL API <!--or MongoDB API-->에 대한 Azure Cosmos DB 계정. 계정이 없는 경우 [Azure Cosmos DB: .NET 및 Azure Portal을 사용하여 SQL API 웹앱 빌드](create-sql-api-dotnet.md)의 설명에 따라 Azure Portal에서 계정을 만들 수 있습니다.

## <a name="installation"></a>설치

[Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)에서 최신 Azure Storage 탐색기 비트를 설치하세요. 이제 Windows, Linux 및 MAC 버전이 지원됩니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

1. **Azure Storage 탐색기**를 설치한 후 다음 이미지에 나온 것처럼 왼쪽에 있는 **플러그 인** 아이콘을 클릭합니다.
       
   ![플러그 인 아이콘](./media/storage-explorer/plug-in-icon.png)
 
2. **Azure 계정 추가**를 선택하고 **로그인**을 클릭합니다.

   ![Azure 구독에 연결](./media/storage-explorer/connect-to-azure-subscription.png)

2. **Azure 로그인** 대화 상자에서 **로그인**을 선택한 후에 Azure 자격 증명을 입력합니다.

    ![로그인](./media/storage-explorer/sign-in.png)

3. 목록에서 구독을 선택하고 **적용**을 클릭합니다.

    ![적용](./media/storage-explorer/apply-subscription.png)

    탐색기 창이 업데이트되고, 선택한 구독에서 계정이 표시됩니다.

    ![계정 목록](./media/storage-explorer/account-list.png)

    **Cosmos DB 계정**을 Azure 구독에 성공적으로 연결했습니다.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB에 연결

Azure Cosmos DB에 연결하는 또 다른 방법은 연결 문자열을 사용하는 것입니다. 연결 문자열을 사용하여 연결하려면 다음 단계를 따르세요.

1. 왼쪽 트리에서 **로컬 및 연결**을 찾아 **Azure Cosmos DB 계정**을 마우스 오른쪽 단추로 클릭한 후 **Azure Cosmos DB에 연결...**을 선택합니다.

    ![연결 문자열을 사용하여 Cosmos DB에 연결](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. 현재 SQL 및 테이블 API만 지원합니다. API를 선택하고, **연결 문자열**을 붙여넣고, **계정 레이블**을 입력하고, **다음**을 클릭하여 요약을 확인한 다음, **연결**을 클릭하여 Azure Cosmos DB 계정을 연결합니다. 연결 문자열 검색에 대한 자세한 내용은 [Get the connection string](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string)(연결 문자열 가져오기)을 참조하세요.

    ![연결 문자열](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>로컬 에뮬레이터를 사용하여 Azure Cosmos DB에 연결
다음 단계를 사용하여 현재 SQL 계정만 지원하는 에뮬레이터로 Azure Cosmos DB에 연결합니다.
1. 에뮬레이터를 설치하고 시작합니다. 에뮬레이터를 설치하는 방법은 [Cosmos DB 에뮬레이터](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator)를 참조하세요.
2. 왼쪽 트리에서 **로컬 및 연결**을 찾아 **Cosmos DB 계정**을 마우스 오른쪽 단추로 클릭한 후 **Cosmos DB 에뮬레이터에 연결...**을 선택합니다.

    ![에뮬레이터로 Cosmos DB에 연결](./media/storage-explorer/emulator-entry.png)

3. 현재 SQL API만 지원합니다. **연결 문자열**을 붙여넣고, **계정 레이블**을 입력하고, **다음**을 클릭하여 요약을 확인한 다음, **연결**을 클릭하여 Azure Cosmos DB 계정을 연결합니다. 연결 문자열 검색에 대한 자세한 내용은 [Get the connection string](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string)(연결 문자열 가져오기)을 참조하세요.

    ![에뮬레이터로 Cosmos DB에 연결 대화 상자](./media/storage-explorer/emulator-dialog.png)



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

     ![포털에서 열기](./media/storage-explorer/open-in-portal.png)

* Azure Cosmos DB 계정, 데이터베이스, 컬렉션 등을 **빠른 액세스**에 추가할 수도 있습니다.
* **여기에서 검색**을 선택하면 선택한 경로에서 키워드 검색이 가능합니다.

    ![여기에서 검색](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>데이터베이스 및 컬렉션 관리
#### <a name="create-a-database"></a>데이터베이스 만들기 
-   Azure Cosmos DB 계정을 마우스 오른쪽 단추로 클릭하고 **데이터베이스 만들기**를 선택하고 데이터베이스 이름을 입력한 후 **Enter** 키를 눌러 완료합니다.
       
    ![데이터베이스 만들기](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>데이터베이스 삭제
- 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 삭제**를 클릭한 후 팝업 창에서 **예**를 클릭합니다. 데이터베이스 노드가 삭제되고, Azure Cosmos DB 계정이 자동으로 새로 고쳐집니다.

    ![database1 삭제](./media/storage-explorer/delete-database1.png)  

    ![database2 삭제](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>컬렉션 만들기
1. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **컬렉션 만들기**를 선택한 후 **컬렉션 ID**, **저장소 용량**과 같은 정보를 제공합니다. **확인**을 클릭하여 마칩니다. 

    ![collection1 만들기](./media/storage-explorer/create-collection.png)

    ![collection2 만들기](./media/storage-explorer/create-collection2.png) 

2. 파티션 키를 지정할 수 있으려면 **무제한**을 선택한 다음, **확인**을 클릭하여 완료합니다.

    컬렉션을 만들 때 파티션 키가 사용된 경우, 컬렉션을 만들고 나면 컬렉션에 대해 파티션 키 값을 변경할 수 없습니다. 파티션 키 설정에 대한 자세한 내용은 [Design for partitioning](partition-data.md#designing-for-partitioning)(분할 설계)을 참조하세요.

    ![파티션 키](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>컬렉션 삭제
- 컬렉션을 마우스 오른쪽 단추로 클릭하고 **컬렉션 삭제**를 클릭한 후 팝업 창에서 **예**를 클릭합니다. 

    컬렉션 노드가 삭제되고, 데이터베이스가 자동으로 새로 고쳐집니다.

    ![컬렉션 삭제](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>문서 관리

#### <a name="create-and-modify-documents"></a>문서 만들기 및 수정
- 새 문서를 만들려면 왼쪽 창에서 **문서**를 연 다음 **새 문서**를 클릭하고 오른쪽 창에서 내용을 편집한 후 **저장**을 클릭합니다. 기존 문서를 업데이트한 후 **저장**을 클릭할 수도 있습니다. **취소**를 클릭하여 변경 내용을 취소할 수 있습니다.

    ![문서](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>문서 삭제
- 선택한 문서를 삭제하려면 **삭제** 단추를 클릭합니다.

#### <a name="query-for-documents"></a>문서에 대한 쿼리
- [SQL 쿼리](sql-api-sql-query.md)를 입력하고 **적용**을 클릭하여 문서 필터를 편집합니다.

    ![문서 필터](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>그래프 관리

#### <a name="create-and-modify-vertex"></a>꼭짓점 만들기 및 수정
1. 새 꼭짓점을 만들려면 왼쪽 창에서 **그래프**를 열고, **새 꼭짓점**을 클릭하고, 내용을 편집한 다음, **확인**을 클릭합니다.    
2. 기존 꼭짓점을 수정하려면 오른쪽 창에서 펜 아이콘을 클릭합니다.   

    ![그래프](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>그래프 삭제
- 꼭짓점을 삭제하려면 꼭짓점 이름 옆에 있는 휴지통 아이콘을 클릭합니다.

#### <a name="filter-for-graph"></a>그래프에 대한 필터
- [Gremlin 쿼리](gremlin-support.md)를 입력하여 그래프 필터를 편집한 다음, **필터 적용**을 클릭합니다.

    ![그래프 필터](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>테이블 관리

#### <a name="create-and-modify-table"></a>테이블 만들기 및 수정
1. 새 테이블을 만들려면 왼쪽 창에서 **엔터티**를 열고, **추가**를 클릭하고, **엔터티 추가** 대화 상자에서 콘텐츠를 편집하고, **추가 속성** 단추를 클릭하여 속성을 추가한 다음, **삽입**을 클릭합니다.
2. 테이블을 수정하려면 **편집**을 클릭하고, 콘텐츠를 수정한 다음, **업데이트**를 클릭합니다.

    ![테이블](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>테이블 가져오기 및 내보내기
1. 가져오려면 **가져오기** 단추를 클릭하고 기존 테이블을 선택합니다.
2. 내보내려면 **내보내기** 단추를 클릭하고 대상을 선택합니다.

    ![테이블 가져오기 및 내보내기](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>엔터티 삭제
- 엔터티를 선택하고 **삭제** 단추를 클릭합니다.

    ![테이블 삭제](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>쿼리 테이블
- **쿼리** 단추를 클릭하고, 쿼리 조건을 입력한 다음, **쿼리 실행** 단추를 클릭합니다. **쿼리 닫기** 단추를 클릭하여 쿼리 창을 닫습니다.

    ![테이블 쿼리](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>저장 프로시저, 트리거 및 UDF 관리
* 저장 프로시저를 만들려면 왼쪽 트리에서 **저장 프로시저**를 마우스 오른쪽 단추로 클릭하고 **저장 프로시저 만들기**를 선택한 후에 왼쪽에 이름을, 오른쪽 창에 저장 프로시저 스크립트를 입력한 다음 **만들기**를 클릭합니다. 
* 기존 저장 프로시저를 편집할 수도 있습니다. 기존 저장 프로시저를 두 번 클릭하고 업데이트한 다음 **업데이트**를 클릭하여 저장하거나 **취소**를 클릭하여 변경 내용을 취소하면 됩니다.

    ![저장 프로시저](./media/storage-explorer/stored-procedure.png)
* **트리거** 및 **UDF**에 대한 작업은 **저장 프로시저**에 대한 작업과 비슷합니다.

## <a name="next-steps"></a>다음 단계

* Azure Storage 탐색기에서 Azure Cosmos DB를 사용하는 방법에 대해 설명하는 다음 비디오를 시청하세요. [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)(Azure Storage 탐색기에서 Azure Cosmos DB 사용)
* [Storage 탐색기(미리 보기) 시작](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)에서 Storage 탐색기에 대해 자세히 알아보고 더 많은 서비스에 연결합니다.

