---
title: 제어 테이블을 사용하여 데이터베이스의 대량 복사본
description: 솔루션 템플릿을 사용하여 외부 제어 테이블을 사용하여 Azure Data Factory를 사용하여 원본 테이블의 파티션 목록을 저장하여 데이터베이스에서 대량 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3a42d7da21cfb2e3066fbdd81b27c82155d8456f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439976"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>컨트롤 테이블이 있는 데이터베이스의 대량 복사본

오라클 서버, 네테자, 테라데이타 또는 SQL Server의 데이터 웨어하우스에서 Azure SQL 데이터 웨어하우스로 데이터를 복사하려면 여러 테이블에서 엄청난 양의 데이터를 로드해야 합니다. 일반적으로 단일 테이블에서 여러 스레드가 있는 행을 로드할 수 있도록 각 테이블에서 데이터를 분할해야 합니다. 이 문서에서는 이러한 시나리오에서 사용할 템플릿에 대해 설명합니다.

 >! 참고 비교적 적은 수의 테이블에서 데이터를 SQL 데이터 웨어하우스로 복사하려는 경우 [Azure 데이터 팩터리 복사 데이터 도구를](copy-data-tool.md)사용하는 것이 더 효율적입니다. 이 문서에서 설명하는 템플릿은 해당 시나리오에 필요한 것 이상입니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 외부 제어 테이블에서 복사할 소스 데이터베이스 파티션 목록을 검색합니다. 그런 다음 원본 데이터베이스의 각 파티션을 반복해서 데이터를 대상에 복사합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **조회는** 외부 제어 테이블에서 확실한 데이터베이스 파티션 목록을 검색합니다.
- **ForEach는** 조회 활동에서 파티션 목록을 얻고 각 파티션을 복사 활동으로 이격합니다.
- 원본 데이터베이스 저장소에서 대상 저장소로 각 파티션을 **복사합니다.**

템플릿은 다음 매개 변수를 정의합니다.
- *Control_Table_Name* 원본 데이터베이스의 파티션 목록을 저장하는 외부 제어 테이블입니다.
- *Control_Table_Schema_PartitionID* 각 파티션 ID를 저장하는 외부 제어 테이블의 열 이름입니다. 원본 데이터베이스의 각 파티션에 대해 파티션 ID가 고유해야 합니다.
- *Control_Table_Schema_SourceTableName* 원본 데이터베이스에서 각 테이블 이름을 저장하는 외부 제어 테이블입니다.
- *Control_Table_Schema_FilterQuery* 원본 데이터베이스의 각 파티션에서 데이터를 얻기 위해 필터 쿼리를 저장하는 외부 제어 테이블의 열 이름입니다. 예를 들어 연도별로 데이터를 분할한 경우 각 행에 저장된 쿼리는 LastModifytime >= '2015-01-01 00:00'와 LastModifytime <= ''2015-12-31 23:59:599''인 데이터 원본에서 '선택*'과 유사할 수 있습니다.
- *Data_Destination_Folder_Path* 데이터가 대상 저장소에 복사되는 경로입니다(선택한 대상이 "파일 시스템" 또는 "Azure Data Lake Storage Gen1"인 경우 해당). 
- *Data_Destination_Container* 데이터가 대상 저장소에 복사되는 루트 폴더 경로입니다. 
- *Data_Destination_Directory* 데이터가 대상 저장소에 복사되는 루트 아래의 디렉터리 경로입니다. 

대상 저장소에서 경로를 정의하는 마지막 세 매개 변수는 선택한 대상이 파일 기반 저장소인 경우에만 표시됩니다. 대상 저장소로 "Azure Synapse 분석(이전 SQL DW)"를 선택하는 경우 이러한 매개 변수는 필요하지 않습니다. 그러나 SQL 데이터 웨어하우스의 테이블 이름과 스키마는 원본 데이터베이스의 스키마와 같아야 합니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. SQL Server 또는 Azure SQL Database에서 제어 테이블을 만들어 대량 복사본에 대한 원본 데이터베이스 파티션 목록을 저장합니다. 다음 예제에서는 원본 데이터베이스에 5개의 파티션이 있습니다. 세 개의 파티션은 *datasource_table*위한 것이고 두 파티션은 *project_table.* *LastModifytime* 열은 원본 데이터베이스에서 *datasource_table* 테이블의 데이터를 분할하는 데 사용됩니다. 첫 번째 파티션을 읽는 데 사용되는 쿼리는 'lastModifytime >= '2015-01-01 00:00'' 및 LastModifytime <' ''2015-12-31 23:59:5999''인 datasource_table *를 선택합니다. 유사한 쿼리를 사용하여 다른 파티션의 데이터를 읽을 수 있습니다.

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

2. **데이터베이스 템플릿에서 대량 복사로** 이동합니다. 1단계에서 만든 외부 제어 테이블에 대한 **새** 연결을 만듭니다.

    ![제어 테이블에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 데이터를 복사하는 원본 데이터베이스에 대한 **새** 연결을 만듭니다.

    ![원본 데이터베이스에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 데이터를 복사하는 대상 데이터 저장소에 대한 **새** 연결을 만듭니다.

    ![대상 저장소에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. **이 템플릿 사용을**선택합니다.

6. 다음 예제와 같이 파이프라인이 표시됩니다.

    ![파이프라인 검토](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.

    ![**디버그**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 다음 예제와 유사한 결과가 표시됩니다.

    ![결과 검토](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (선택 사항) 데이터 대상으로 "Azure Synapse Analytics(이전의 SQL DW)"를 선택한 경우 SQL 데이터 웨어하우스 Polybase에서 요구하는 대로 스테이징을 위해 Azure Blob 저장소에 대한 연결을 입력해야 합니다. 템플릿은 Blob 저장소에 대한 컨테이너 경로를 자동으로 생성합니다. 파이프라인실행 후 컨테이너가 만들어졌는지 확인합니다.
    
    ![Polybase 설정](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
