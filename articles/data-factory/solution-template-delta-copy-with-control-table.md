---
title: Azure Data Factory를 사용 하 여 제어 테이블을 사용 하 여 데이터베이스에서 델타 복사 | Microsoft Docs
description: 솔루션 템플릿을 사용하여 증분 방식으로 Azure Data Factory의 데이터베이스에서 새 행 또는 업데이트된 행만 복사하는 방법을 알아봅니다.
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
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312510"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>제어 테이블이 있는 데이터베이스에서 복사

이 문서를 증분 로드 새롭거나 업데이트 된 행을 데이터베이스 테이블에서 Azure로 상위 워터 마크 값을 저장 하는 컨트롤 외부 테이블을 사용 하 여 사용할 수 있는 서식 파일을 설명 합니다.

이 템플릿에 신규 또는 업데이트 된 행을 식별 하는 타임 스탬프 열 또는 증분 키 원본 데이터베이스의 스키마에 포함 되어 있는지 필요 합니다.

>[!NOTE]
> 새롭거나 업데이트 된 행을 식별 하 여 원본 데이터베이스의 타임 스탬프 열이 있는 델타 복사에 사용할 외부 제어 테이블을 만들고 않으려는 하지만 대신 사용할 수 있습니다 하는 경우는 [Azure Data Factory 데이터 복사 도구](copy-data-tool.md) 파이프라인을 가져오려고 합니다. 해당 도구 변수로 트리거 예약 시간을 사용 하 여 원본 데이터베이스에서 새 행을 읽습니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿을 먼저 이전 워터 마크 값을 가져오고 현재 워터 마크 값을 비교 합니다. 그런 다음 2 개의 워터 마크 값 간의 비교를 기반으로 원본 데이터베이스에서 변경 내용만 복사 합니다. 마지막으로, 다음 번 로드 하는 델타 데이터에 대 한 외부 제어 테이블을 새 상위 워터 마크 값을 저장 합니다.

이 템플릿은 다음 4가지 작업을 포함합니다.
- **조회** 컨트롤 외부 테이블에 저장 된 이전 상위 워터 마크 값을 검색 합니다.
- 다른 **조회** 활동 원본 데이터베이스에서 현재 상위 워터 마크 값을 검색 합니다.
- **복사** 대상 저장소를 원본 데이터베이스에서 변경 내용만 복사 합니다. 원본 데이터베이스의 변경 내용을 식별 하는 쿼리는 비슷합니다 ' 선택 *에서 Data_Source_Table 여기서 TIMESTAMP_Column > "마지막 상위 워터 마크" 및 TIMESTAMP_Column < "현재 상위 워터 마크" ='.
- **SqlServerStoredProcedure** 현재 상위 워터 마크 값이 다음 번 델타 복사에 대 한 외부 컨트롤 테이블에 기록 합니다.

이 템플릿은 다음 5개의 매개 변수를 정의합니다.
- *Data_Source_Table_Name* 원본 데이터베이스에서 데이터를 로드 하려는 테이블이 사용 됩니다.
- *Data_Source_WaterMarkColumn* 새 식별 하는 데 사용 되거나 업데이트 된 행 원본 테이블의 열 이름입니다. 이 칼럼의 형식은 일반적으로 *날짜/시간*, *INT*, 또는 유사 합니다.
- *Data_Destination_Folder_Path* 나 *Data_Destination_Table_Name* 대상 저장소에 데이터를 복사할 위치 하면 됩니다.
- *Control_Table_Table_Name* 높은 워터 마크 값을 저장 하는 외부 컨트롤 테이블입니다.
- *Control_Table_Column_Name* 높은 워터 마크 값을 저장 하는 컨트롤 외부 테이블의 열입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 탐색 소스를 로드 하려는 해당 하면 테이블 및 새롭거나 업데이트 된 행을 식별 하는 상위 워터 마크 열을 정의 합니다. 이 칼럼의 형식이 있을 *날짜/시간*를 *INT*, 또는 유사한 합니다. 이 열의 값에는 새 행 추가 됨에 따라 증가 합니다. 다음 샘플 원본 테이블 (data_source_table)에서 사용할 수 있습니다 합니다 *LastModifytime* 상위 워터 마크 열으로 열입니다.

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
    
2. 델타 데이터 로드에 대 한 상위 워터 마크 값을 저장 하도록 SQL Server 또는 Azure SQL Database에서 제어 테이블을 만듭니다. 다음 예제에서는 제어 테이블의 이름은 *watermarktable*합니다. 이 표에 *WatermarkValue* 상위 워터 마크 값을 저장 하는 열은 이며 해당 형식만 *datetime*합니다.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 제어 테이블을 만드는 데는 동일한 SQL Server 또는 Azure SQL Database 인스턴스에 저장된 프로시저를 만듭니다. 저장된 프로시저는 델타 데이터 로드 다음 시간에 대 한 외부 컨트롤 테이블에 새 상위 워터 마크 값을 쓸 사용 됩니다.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 로 이동 합니다 **데이터베이스에서 델타 복사** 템플릿. 만들기는 **새로 만들기** 에서 데이터를 복사 하려는 원본 데이터베이스에 연결 합니다.

    ![원본 테이블에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 만들기는 **새로 만들기** 데이터를 복사 하려는 대상 데이터 저장소에 연결 합니다.

    ![대상 테이블에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 만들기는 **새로 만들기** 외부 컨트롤 테이블과 2와 3 단계에서 만든 저장된 프로시저에 연결 합니다.

    ![제어 테이블 데이터 저장소에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. 선택 **이 템플릿을 사용 하 여**입니다.

     ![이 템플릿 사용](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. 다음 예제에서와 같이 사용 가능한 파이프라인을 참조 하세요.

     ![파이프라인 검토](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 선택 **저장 프로시저**합니다. 에 대 한 **저장 프로시저 이름**, 선택 **[update_watermark]** 합니다. 선택 **가져오기 매개 변수**를 선택한 후 **동적 콘텐츠 추가**합니다.  

     ![저장된 프로시저 작업을 설정 합니다.](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. 콘텐츠를 작성  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** 를 선택한 후 **마침**합니다.  

     ![저장된 프로시저의 매개 변수에 대 한 내용을 작성 합니다.](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. 선택 **디버그**를 입력 합니다 **매개 변수**를 선택한 후 **마침**합니다.

    ![선택 * * 디버그 * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 다음 예제와 비슷한 결과가 표시 됩니다.

    ![결과 검토](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 원본 테이블에 새 행을 만들 수 있습니다. 새 행을 만드는 샘플 SQL 언어는 다음과 같습니다.

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. 파이프라인을 다시 실행 하려면 선택 **디버그**를 입력 합니다 **매개 변수**를 선택한 후 **마침**합니다.

    ![선택 * * 디버그 * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    대상에 복사 된 새 행이 표시 됩니다.

15. (선택 사항) SQL Data Warehouse를 데이터 대상으로 선택한 경우 SQL Data Warehouse Polybase 필요한 스테이징을 위해 Azure Blob storage에 연결을 제공 해야 합니다. Blob storage에서 컨테이너는 이미 만들어진 있는지 확인 합니다.
    
    ![PolyBase 구성](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory를 사용 하 여 제어 테이블을 사용 하 여 데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)
- [Azure Data Factory를 사용 하 여 여러 컨테이너에서 파일을 복사 합니다.](solution-template-copy-files-multiple-containers.md)
