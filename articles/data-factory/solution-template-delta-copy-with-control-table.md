---
title: 제어 테이블을 사용하여 데이터베이스에서 델타 복사
description: 솔루션 템플릿을 사용하여 증분 방식으로 Azure Data Factory의 데이터베이스에서 새 행 또는 업데이트된 행만 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 3c077e2c04cae94d2e1a2a84ccd7d09c7a0829b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439631"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>제어 테이블이 있는 데이터베이스의 델타 복사

이 문서에서는 높은 워터마크 값을 저장하는 외부 제어 테이블을 사용하여 데이터베이스 테이블에서 Azure로 새 행이나 업데이트된 행을 증분적으로 로드하는 데 사용할 수 있는 템플릿에 대해 설명합니다.

이 템플릿에서는 원본 데이터베이스의 스키마에 새 행이나 업데이트된 행을 식별하기 위해 타임스탬프 열 또는 증분 키가 포함되어야 합니다.

>[!NOTE]
> 원본 데이터베이스에 새 행이나 업데이트된 행을 식별하는 타임스탬프 열이 있지만 델타 복사본에 사용할 외부 제어 테이블을 만들지 않으려는 경우 [대신 Azure Data Factory Copy Data 데이터 도구를](copy-data-tool.md) 사용하여 파이프라인을 얻을 수 있습니다. 이 도구는 트리거 예약 시간을 변수로 사용하여 원본 데이터베이스에서 새 행을 읽습니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 먼저 이전 워터마크 값을 검색하고 현재 워터마크 값과 비교합니다. 그런 다음 두 워터마크 값 간의 비교를 기반으로 원본 데이터베이스의 변경 내용만 복사합니다. 마지막으로 다음 번에 델타 데이터 로드를 위해 새 하이 워터마크 값을 외부 제어 테이블에 저장합니다.

이 템플릿은 다음 4가지 작업을 포함합니다.
- **조회는** 외부 제어 테이블에 저장되는 이전 하이 워터마크 값을 검색합니다.
- 또 다른 **조회** 활동은 원본 데이터베이스에서 현재 하이 워터마크 값을 검색합니다.
- **복사복사본은** 원본 데이터베이스에서 대상 저장소로변경만 복사합니다. 원본 데이터베이스의 변경 내용을 식별하는 쿼리는 'SELECT * FROM Data_Source_Table TIMESTAMP_Column > "마지막 하이 워터마크"와 TIMESTAMP_Column <= "현재 하이워터마크"와 유사합니다.
- **SqlServer저장프로시저는** 다음 번에 델타 복사본에 대한 외부 제어 테이블에 현재 하이 워터마크 값을 씁니다.

템플릿은 다음 매개 변수를 정의합니다.
- *Data_Source_Table_Name* 데이터를 로드하려는 원본 데이터베이스의 테이블입니다.
- *Data_Source_WaterMarkColumn* 새 행또는 업데이트된 행을 식별하는 데 사용되는 원본 테이블의 열 이름입니다. 이 열의 형식은 일반적으로 *datetime,* *INT*또는 이와 유사합니다.
- *Data_Destination_Container* 데이터가 대상 저장소에 복사되는 위치의 루트 경로입니다.
- *Data_Destination_Directory* 데이터가 대상 저장소에 복사되는 위치의 루트 아래에 있는 디렉터리 경로입니다.
- *Data_Destination_Table_Name* 데이터가 대상 저장소에 복사되는 위치입니다("Azure Synapse Analytics(이전의 SQL DW)"가 데이터 대상으로 선택된 경우 해당).
- *Data_Destination_Folder_Path* 데이터가 대상 저장소에 복사되는 위치입니다("파일 시스템" 또는 "Azure Data Lake Storage Gen1"이 데이터 대상으로 선택된 경우 해당).
- *Control_Table_Table_Name* 높은 워터마크 값을 저장하는 외부 제어 테이블입니다.
- *Control_Table_Column_Name* 높은 워터마크 값을 저장하는 외부 제어 테이블의 열입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 로드할 원본 테이블을 탐색하고 새 행 또는 업데이트된 행을 식별하는 데 사용할 수 있는 하이 워터마크 열을 정의합니다. 이 열의 형식은 *날짜 시간,* *INT*또는 이와 유사할 수 있습니다. 새 행이 추가될 수록 이 열의 값이 증가합니다. 다음 샘플 원본 테이블(data_source_table)에서 *LastModifytime* 열을 하이 워터마크 열로 사용할 수 있습니다.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. SQL Server 또는 Azure SQL Database에서 컨트롤 테이블을 만들어 델타 데이터 로드에 대한 하이 워터마크 값을 저장합니다. 다음 예제에서는 컨트롤 테이블의 이름은 *워터마크 할 수 있습니다.* 이 표에서 *워터마크값은* 하이 워터마크 값을 저장하는 열이며 해당 형식은 *datetime입니다.*

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 컨트롤 테이블을 만드는 데 사용한 것과 동일한 SQL Server 또는 Azure SQL Database 인스턴스에서 저장 프로시저를 만듭니다. 저장 프로시저는 다음에 델타 데이터 로드를 위해 외부 제어 테이블에 새 하이 워터마크 값을 작성하는 데 사용됩니다.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. **데이터베이스 템플릿에서 델타 복사본으로** 이동합니다. 데이터 복사할 원본 데이터베이스에 대한 **새** 연결을 만듭니다.

    ![원본 테이블에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 데이터를 복사할 대상 데이터 저장소에 대한 **새** 연결을 만듭니다.

    ![대상 테이블에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 2단계와 3단계에서 만든 외부 제어 테이블 및 저장 프로시저에 대한 **새** 연결을 만듭니다.

    ![제어 테이블 데이터 저장소에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. **이 템플릿 사용을**선택합니다.
    
8. 다음 예제와 같이 사용 가능한 파이프라인이 표시됩니다.
  
    ![파이프라인 검토](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. **저장 프로시저를**선택합니다. **저장 프로시저 이름의**경우 **[dbo]를 선택합니다.] update_watermark]**. **매개 변수 가져오기를**선택한 다음 **동적 콘텐츠 추가를**선택합니다.  

    ![저장 프로시저 활동 설정](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. 콘텐츠 ** \@{활동('조회현재워터마크')을 작성합니다.output.firstRow.New워터마크값}을**선택한 다음 **완료를 선택합니다.**  

    ![저장 프로시저의 매개 변수에 대한 콘텐츠 작성](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.

    ![**디버그** 선택](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 다음 예제와 유사한 결과가 표시됩니다.

    ![결과 검토](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 원본 테이블에 새 행을 만들 수 있습니다. 다음은 새 행을 만드는 샘플 SQL 언어입니다.

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. 파이프라인을 다시 실행하려면 **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.

    새 행만 대상에 복사된 것을 볼 수 있습니다.

15. (선택 사항:) Azure Synapse Analytics(이전의 SQL DW)를 데이터 대상으로 선택하는 경우 SQL 데이터 웨어하우스 Polybase에서 요구하는 스테이징을 위해 Azure Blob 저장소에 대한 연결도 제공해야 합니다. 템플릿은 당신을 위해 컨테이너 경로를 생성합니다. 파이프라인이 실행된 후 컨테이너가 Blob 저장소에 만들어졌는지 확인합니다.
    
    ![PolyBase 구성](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>다음 단계

- [Azure 데이터 팩터리의 컨트롤 테이블을 사용하여 데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)
- [Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)
