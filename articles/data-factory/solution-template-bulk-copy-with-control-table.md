---
title: Azure Data Factory에서 제어 테이블을 사용하여 데이터베이스에서 대량 복사 | Microsoft Docs
description: Azure Data Factory에 원본 테이블의 파티션 목록을 저장하기 위해 솔루션 템플릿을 사용하여 외부 제어 테이블을 통해 데이터베이스에서 데이터를 대량 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967012"
---
# <a name="bulk-copy-from-database-with-control-table"></a>제어 테이블을 사용하여 데이터베이스에서 대량 복사

Oracle 서버, Netezza 서버, Teradata 서버, SQL Server 등의 데이터 웨어하우스에서 Azure로 데이터를 복사하려는 경우 데이터 원본의 여러 테이블에서 대량 데이터를 로드해야 합니다. 대부분의 경우 단일 테이블에서 여러 스레드를 병렬 처리하여 행을 로드할 수 있도록 각 테이블에 데이터를 추가로 분할해야 합니다. 현재 템플릿은 이러한 경우에 맞게 디자인되었습니다. 

소수의 테이블에서 작은 데이터 크기로 데이터를 복사하려는 경우 “데이터 복사 도구”로 이동하여 단일 복사 작업이나 foreach 작업 + 복사 작업을 파이프라인에 포함하는 것이 더 효율적입니다. 이 간단한 사용 사례에는 이 템플릿으로 충분합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 대상 저장소에 복사해야 하는 원본 데이터베이스의 파티션 목록을 외부 제어 테이블에서 검색한 다음, 원본 데이터베이스의 각 파티션을 반복하고 데이터 복사 작업을 수행합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
-   **조회** 작업 - 외부 제어 테이블에서 원본 데이터베이스의 파티션 목록을 검색합니다.
-   **ForEach** 작업 - 조회 작업에서 파티션 목록을 가져온 다음, 각 파티션을 반복하여 복사 작업을 수행합니다.
-   **복사** 작업 - 원본 데이터베이스 저장소에서 대상 저장소로 각 파티션을 복사합니다.

이 템플릿은 다음 5개의 매개 변수를 정의합니다.
-   *Control_Table_Name* 매개 변수 - 외부 제어 테이블의 테이블 이름입니다. 제어 테이블은 원본 데이터베이스의 파티션 목록을 저장하는 데 사용됩니다.
-   *Control_Table_Schema_PartitionID* 매개 변수 - 각 파티션 ID를 저장할 외부 제어 테이블의 열 이름입니다. 파티션 ID가 원본 데이터베이스의 각 파티션에서 고유한지 확인합니다.
-   *Control_Table_Schema_SourceTableName* 매개 변수 - 원본 데이터베이스의 각 테이블 이름을 저장할 외부 제어 테이블의 열 이름입니다.
-   *Control_Table_Schema_FilterQuery* 매개 변수 - 원본 데이터베이스의 각 파티션에서 데이터를 가져오는 필터 쿼리를 저장할 외부 제어 테이블의 열 이름입니다. 예를 들어 각 연도를 기준으로 데이터를 분할한 경우 각 행에 저장된 쿼리는 ‘select * from datasource where LastModifytime >= “2015-01-01 00:00:00” and LastModifytime <= “2015-12-31 23:59:59.999”’와 유사할 수 있습니다.
-   *Data_Destination_Folder_Path* 매개 변수 - 데이터가 대상 저장소에 복사되는 폴더 경로입니다.  이 매개 변수는 선택한 대상이 파일 기반 스토리지 저장소인 경우에만 표시됩니다.  SQL Data Warehouse를 대상 저장소로 선택한 경우에는 여기서 입력해야 하는 매개 변수가 없습니다. 그러나 SQL Data Warehouse의 테이블 이름과 스키마가 원본 데이터베이스와 같아야 합니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 대량 복사를 위해 원본 데이터베이스의 파티션 목록을 저장할 제어 테이블을 SQL Server 또는 SQL Azure에 만듭니다.  아래 예제에서는 원본 데이터베이스에 5개 파티션이 있는 것을 확인할 수 있습니다. 여기서 파티션 3개는 *datasource_table* 테이블에 사용되고, 파티션 2개는  *project_table* 테이블에 사용됩니다. *LastModifytime* 열은 원본 데이터베이스에서 *datasource_table* 테이블의 데이터를 분할하는 데 사용됩니다. 첫 번째 파티션을 읽는 데 사용되는 쿼리는 ‘select * from datasource_table where LastModifytime >= “2015-01-01 00:00:00” and LastModifytime <= “2015-12-31 23:59:59.999”’입니다.  다른 파티션에서 데이터를 읽는 유사한 쿼리를 확인할 수도 있습니다. 

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. **데이터베이스에서 대량 복사** 템플릿으로 이동한 다음, 외부 제어 테이블에 대한 **새 연결**을 만듭니다.  이 연결은 #1단계에서 제어 테이블을 만든 데이터베이스에 연결됩니다.

    ![제어 테이블에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 데이터를 복사할 원본 데이터베이스에 대한 **새 연결**을 만듭니다.

     ![원본 데이터베이스에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 데이터를 복사할 대상 데이터 저장소에 대한 **새 연결**을 만듭니다.

    ![대상 저장소에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. **이 템플릿 사용**을 클릭합니다.

    ![이 템플릿 사용](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. 다음 예제와 같이 패널에서 사용할 수 있는 파이프라인이 표시됩니다.

    ![파이프라인 검토](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. **디버그**를 클릭하고 매개 변수를 입력한 다음, **마침**을 클릭합니다.

    ![디버그 클릭](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 다음 예제와 같이 패널에서 사용할 수 있는 결과가 표시됩니다.

    ![결과 검토](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (선택 사항) SQL Data Warehouse를 데이터 대상으로 선택하는 경우 Azure Blob Storage의 연결을 SQL Data Warehouse Polybase에 필요한 스테이징으로 입력해야 합니다.  Blob Storage의 컨테이너를 이미 만들었는지 확인합니다.  
    
    ![Polybase 설정](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)