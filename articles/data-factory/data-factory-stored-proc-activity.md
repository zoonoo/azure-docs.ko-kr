---
title: "SQL Server 저장 프로시저 작업"
description: "SQL Server 저장 프로시저 작업을 사용하여 데이터 팩터리 파이프라인으로 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에서 저장 프로시저를 호출하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 1b2514e1e6f39bb3ce9d8a46f4af01835284cdcc
ms.openlocfilehash: 3510b0446cf3c1a7ffb3ff02a4d84d24ead1cae7


---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server 저장 프로시저 작업
> [!div class="op_single_selector"]
> [Hive](data-factory-hive-activity.md)  
> [Pig](data-factory-pig-activity.md)  
> [MapReduce](data-factory-map-reduce.md)  
> [Hadoop 스트리밍](data-factory-hadoop-streaming-activity.md)
> [Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> [저장 프로시저](data-factory-stored-proc-activity.md)
> [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> [.NET 사용자 지정](data-factory-use-custom-activities.md)
>
>

데이터 팩터리 [파이프라인](data-factory-create-pipelines.md) 에서 SQL Server 저장 프로시저 작업을 사용하여 다음 데이터 저장소 중 하나에서 저장된 프로시저를 호출할 수 있습니다.

* Azure SQL 데이터베이스
* Azure SQL 데이터 웨어하우스  
* 엔터프라이즈 또는 Azure VM에서 SQL Server 데이터베이스. 데이터베이스와 리소스 경쟁을 피하려면 데이터 관리 게이트웨이를 데이터베이스를 호스팅하는 컴퓨터와 동일한 컴퓨터 또는 별도의 컴퓨터에 설치해야 합니다. 데이터 관리 게이트웨이는 온-프레미스 데이터 원본/Azure VM에 호스팅된 데이터 원본을 클라우드 서비스에 안전하고 관리되는 방식으로 연결하는 소프트웨어입니다. 데이터 관리 게이트웨이에 대한 자세한 내용은 [온-프레미스 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 을 참조하세요.

이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

## <a name="walkthrough"></a>연습
### <a name="sample-table-and-stored-procedure"></a>샘플 테이블 및 저장 프로시저
1. SQL Server Management Studio 또는 익숙한 다른 도구를 사용하여 Azure SQL 데이터베이스에서 다음 **테이블** 을 만듭니다. datetimestamp 열은 해당 ID가 생성된 날짜와 시간입니다.

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    Id는 고유 식별자이며 datetimestamp 열은 해당 ID가 생성된 날짜와 시간입니다.
    ![샘플 데이터](./media/data-factory-stored-proc-activity/sample-data.png)

   > [!NOTE]
   > 이 샘플은 Azure SQL 데이터베이스를 사용하지만 Azure SQL 데이터 웨어하우스 및 SQL Server 데이터베이스와 동일한 방식으로 작동합니다.
   >
   >
2. **sampletable**에 데이터를 삽입하는 다음 **저장 프로시저**를 만듭니다.

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS

        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

   > [!IMPORTANT]
   > 매개 변수(이 예에서는 DateTime)의 **이름** 및 **대/소문자**는 파이프라인/작업 JSON에서 지정된 매개 변수의 이름 및 대/소문자와 일치해야 합니다. 저장 프로시저 정의에서 **@** 는 매개 변수에 대한 접두사로 사용되어야 합니다.
   >
   >

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **인텔리전스 + 분석**을 클릭한 다음 **Data Factory**를 클릭합니다.

    ![새 data factory](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. **새 data factory** 블레이드에서 이름으로 **SProcDF**를 입력합니다. Azure Data Factory 이름은 **전역적으로 고유**합니다. 팩터리를 성공적으로 만들려면 데이터 팩터리의 이름의 접두사를 사용자의 이름으로 해야 합니다.

   ![새 data factory](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Azure **구독**을 선택합니다.
5. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
   1. **새로 만들기**를 클릭하고 리소스 그룹의 이름을 입력합니다.
   2. **기존 항목 사용**을 클릭하고 기존 리소스 그룹을 선택합니다.  
6. 데이터 팩터리의 **위치** 를 선택합니다.
7. 다음에 로그인할 때 대시보드에 Data Factory가 표시되도록 **대시보드에 고정**을 선택합니다.
8. **새 Data Factory** 블레이드에서 **만들기**를 클릭합니다.
9. Azure Portal의 **대시보드** 에 생성된 데이터 팩터리가 표시됩니다. 데이터 팩터리 만들기를 완료한 후에는 데이터 팩터리 페이지가 표시되며 여기에 데이터 팩터리의 내용이 표시됩니다.
   ![Data Factory 홈 페이지](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Azure SQL 연결된 서비스 만들기
데이터 팩터리를 만든 후 Azure SQL 데이터베이스를 데이터 팩터리에 연결하는 Azure SQL 연결된 서비스를 만듭니다. 이 데이터베이스는 sampletable 테이블과 sp_sample 저장 프로시저를 포함합니다.

1. **SProcDF**에 대한 **Data Factory** 블레이드에서 **작성 및 배포**를 클릭하여 Data Factory 편집기를 시작합니다.
2. 명령 모음에서 **새 데이터 저장소**를 클릭하고 **Azure SQL Database**를 선택합니다. 편집기에 Azure SQL 연결된 서비스를 만들기 위한 JSON 스크립트가 표시됩니다.

   ![새 데이터 저장소](media/data-factory-stored-proc-activity/new-data-store.png)
3. JSON 스크립트에서 다음과 같이 변경합니다.

   1. **&lt;servername&gt;**을 Azure SQL Database 서버 이름으로 바꿉니다.
   2. **&lt;databasename&gt;**을 테이블 및 저장 프로시저를 만든 데이터베이스로 바꿉니다.
   3. **&lt;username@servername&gt;**를 데이터베이스에 대한 액세스 권한이 있는 사용자 계정으로 바꿉니다.
   4. **&lt;password&gt;**를 사용자 계정의 암호로 바꿉니다.

      ![새 데이터 저장소](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. 명령 모음에서 **배포** 를 클릭하여 연결된 서비스를 배포합니다. 왼쪽의 트리 뷰에 AzureSqlLinkedService가 표시되는지 확인합니다.

    ![연결된 서비스와 트리 뷰](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>출력 데이터 집합 만들기
1. 단추가 표시되지 않는 경우 도구 모음에서 **... 추가**, **새 데이터 집합**, **Azure SQL**을 차례로 클릭합니다. 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure SQL**을 선택합니다.

    ![연결된 서비스와 트리 뷰](media/data-factory-stored-proc-activity/new-dataset.png)
2. 다음 JSON 스크립트를 복사하여 JSON 편집기에 붙여 넣습니다.

        {                
            "name": "sprocsampleout",
            "properties": {
                "type": "AzureSqlTable",
                "linkedServiceName": "AzureSqlLinkedService",
                "typeProperties": {
                    "tableName": "sampletable"
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }
3. 명령 모음에서 **배포** 를 클릭하여 데이터 집합을 배포합니다. 트리 뷰에 데이터 집합이 표시되는지 확인합니다.

    ![연결된 서비스와 트리 뷰](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>SqlServerStoredProcedure 작업을 사용하여 파이프라인 만들기
이제 SqlServerStoredProcedure 작업을 사용하여 파이프라인 만들겠습니다.

1. 단추가 표시되지 않는 경우 도구 모음에서 **... 추가**를 클릭하고 **새 파이프라인**을 클릭합니다.
2. 다음 JSON 코드 조각을 복사하여 붙여 넣습니다. **storedProcedureName**은 **sp_sample**로 설정됩니다. **DateTime** 매개 변수의 이름 및 대/소문자는 저장 프로시저 정의에 있는 매개 변수의 이름 및 대/소문자와 일치해야 합니다.  

        {
            "name": "SprocActivitySamplePipeline",
            "properties": {
                "activities": [
                    {
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
                            "storedProcedureName": "sp_sample",
                            "storedProcedureParameters": {
                                "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                            }
                        },
                        "outputs": [
                            {
                                "name": "sprocsampleout"
                            }
                        ],
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "SprocActivitySample"
                    }
                ],
                 "start": "2016-08-02T00:00:00Z",
                 "end": "2016-08-02T05:00:00Z",
                "isPaused": false
            }
        }

    매개 변수에 대해 null을 전달해야 하는 경우 구문: "param1": null(모두 소문자)을 사용합니다.
3. 도구 모음에서 **배포** 를 클릭하여 파이프라인을 배포합니다.  

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링
1. **X**를 클릭하여 Data Factory 편집기 블레이드를 닫고 Data Factory 블레이드로 돌아가서 **다이어그램**을 클릭합니다.

    ![다이어그램 타일](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. **다이어그램 보기**에 파이프라인의 개요와 이 자습서에 사용된 데이터 집합이 표시됩니다.

    ![다이어그램 타일](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. 다이어그램 뷰에서 **sprocsampleout**데이터 집합을 두 번 클릭합니다. 준비 상태의 조각이 표시됩니다. 조각은 JSON에서 시작 시간 및 종료 시간 사이의 매시간 생성되므로 5개 조각입니다.

    ![다이어그램 타일](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. 조각이 **Ready** 상태일 때 Azure SQL 데이터베이스에 대해 **select * from sampletable** 쿼리를 실행하여 저장 프로시저에 의해 데이터가 테이블에 삽입되었는지 확인합니다.

   ![출력 데이터](./media/data-factory-stored-proc-activity/output.png)

   Azure Data Factory 파이프라인 모니터링에 대한 자세한 내용은 [파이프라인 모니터링](data-factory-monitor-manage-pipelines.md) 을 참조하세요.  

> [!NOTE]
> 이 예에서 SprocActivitySample에는 입력이 없습니다. 이 작업을 작업 업스트림과 연결하려면(즉, 처리 전) 업스트림 작업의 출력을 이 작업의 입력으로 사용할 수 있습니다. 이 경우 업스트림 작업이 완료되어 업스트림 작업의 출력이 제공될 때까지(Ready 상태) 이 작업은 실행되지 않습니다. 입력은 저장 프로시저 작업에 대한 매개 변수로 직접 사용할 수 없습니다.
>
>

## <a name="json-format"></a>JSON 형식
    {
        "name": "SQLSPROCActivity",
        "description": "description",
        "type": "SqlServerStoredProcedure",
        "inputs":  [ { "name": "inputtable"  } ],
        "outputs":  [ { "name": "outputtable" } ],
        "typeProperties":
        {
            "storedProcedureName": "<name of the stored procedure>",
            "storedProcedureParameters":  
            {
                "param1": "param1Value"
                …
            }
        }
    }

## <a name="json-properties"></a>JSON 속성
| 속성 | 설명 | 필수 |
| --- | --- | --- |
| name |작업의 이름 |예 |
| 설명 |작업이 무엇에 사용되는지 설명하는 텍스트입니다. |아니요 |
| type |SqlServerStoredProcedure |예 |
| inputs |선택 사항입니다. 입력 데이터 집합을 지정하는 경우 실행할 저장 프로시저 작업에 사용할 수 있어야 합니다('Ready' 상태). 저장 프로시저에서 입력 데이터 집합을 매개 변수로 사용할 수 없습니다. 저장 프로시저 작업을 시작하기 전에 종속성을 확인하는 데만 사용됩니다. |아니요 |
| outputs |저장 프로시저 작업에 대한 출력 데이터 집합을 지정해야 합니다. 출력 데이터 집합은 저장 프로시저 작업에 대한 **일정** (매시간, 매주, 매월 등)을 지정합니다. <br/><br/>출력 데이터 집합은 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스나 저장 프로시저를 실행하려는 SQL Server 데이터베이스를 참조하는 **연결된 서비스** 를 사용해야 합니다. <br/><br/>출력 데이터 집합은 파이프라인에서 다른 작업에 의한 후속 처리([활동 체이닝](data-factory-scheduling-and-execution.md#run-activities-in-a-sequence))를 위해 저장 프로시저의 결과를 전달하는 방법으로 사용할 수 있습니다. 그러나 Data Factory는 저장 프로시저의 출력을 이 데이터 집합에 자동으로 쓰지 않습니다. 출력 데이터 집합이 가리키는 SQL 테이블에 기록하는 저장 프로시저입니다. <br/><br/>경우에 따라 출력 데이터 집합은 저장 프로시저 작업을 실행하는 일정을 지정하기 위해서만 사용되는 **더미 데이터 집합**일 수 있습니다. |예 |
| storedProcedureName |출력 테이블에서 사용하는 연결된 서비스로 표시되는 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스의 저장 프로시저 이름을 지정합니다. |예 |
| storedProcedureParameters |저장 프로시저 매개 변수의 값을 지정합니다. 매개 변수에 대해 null을 전달해야 하는 경우 구문: "param1": null(모두 소문자)을 사용합니다. 이 속성을 사용하는 방법에 대한 자세한 내용은 다음 샘플을 참조하세요. |아니요 |

## <a name="passing-a-static-value"></a>정적 값 전달
'Document sample'라는 정적 값이 포함된 테이블에 'Scenario'라는 다른 열을 추가해보겠습니다.

![샘플 데이터 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)

    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

이제 Scenario 매개 변수와 저장 프로시저 작업의 값을 전달합니다. 위 샘플에서 typeProperties 섹션은 다음 코드 조각과 같습니다.

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters":
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }



<!--HONumber=Nov16_HO3-->


