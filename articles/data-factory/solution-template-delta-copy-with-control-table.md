---
title: Azure Data Factory에서 제어 테이블을 사용하여 데이터베이스에서 델타 복사 | Microsoft Docs
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
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966924"
---
# <a name="delta-copy-from-database-with-control-table"></a>제어 테이블을 사용하여 데이터베이스에서 델타 복사

데이터베이스의 테이블에서 변경 내용(새 행 또는 업데이트된 행)만 상위 워터마크 값을 저장하는 Azure의 외부 제어 테이블에 증분 방식으로 로드하려고 합니다.  현재 템플릿은 이 경우를 위해 디자인되었습니다. 

이 템플릿에서는 원본 데이터베이스의 스키마가 새 행 또는 업데이트된 행을 식별하기 위한 타임스탬프 열 또는 증분 키를 포함해야 합니다.

원본 데이터베이스에 새 행 또는 업데이트된 행을 식별하는 타임스탬프 열이 있지만 델타 복사를 사용하기 위해 외부 제어 테이블을 만들지는 않으려는 경우, 데이터 복사 도구를 사용하여 트리거 예약 시간을 변수로 사용하여 원본 데이터베이스에서 새 행만 읽는 파이프라인을 가져올 수 있습니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 항상 이전 워터마크 값을 먼저 검색한 후 현재 워터마크 값과 비교합니다. 그런 다음, 두 워터마크 값 간의 비교 결과를 기준으로 원본 데이터베이스에서 변경된 내용만 복사합니다.  완료되면 다음번에 델타 데이터 로드를 위해 새로운 상위 워터마크 값을 외부 제어 테이블에 저장합니다.

이 템플릿은 다음 4가지 작업을 포함합니다.
-   외부 제어 테이블에 저장된 이전 상위 워터마크 값을 검색하는 **Lookup** 작업
-   원본 데이터베이스에서 현재 상위 워터마크 값을 검색하는 **Lookup** 작업
-   원본 데이터베이스의 변경 내용만 대상 저장소로 복사하는 **Copy** 작업 복사 작업에서 원본 데이터베이스의 변경 내용을 식별하는 데 사용하는 쿼리는 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” 및 TIMESTAMP_Column <= “current high-watermark”'와 비슷합니다.
-   다음번에 델타 데이터 로드를 위해 현재 상위 워터마크 값을 외부 제어 테이블에 쓰는 **SqlServerStoredProcedure** 작업

이 템플릿은 다음 5개의 매개 변수를 정의합니다.
-   매개 변수 *Data_Source_Table_Name*은 데이터를 로드하려는 원본 데이터베이스의 테이블 이름입니다.
-   매개 변수 *Data_Source_WaterMarkColumn*은 새 행 또는 업데이트된 행을 식별하는 데 사용할 수 있는 원본 테이블의 열 이름입니다. 일반적으로 이 열의 형식은 datetime 또는 INT 등이 될 수 있습니다.
-   매개 변수 *Data_Destination_Folder_Path* 또는 *Data_Destination_Table_Name*은 데이터가 대상 저장소에 복사되는 위치입니다.
-   매개 변수 *Control_Table_Table_Name*은 상위 워터마크 값을 저장할 외부 제어 테이블의 이름입니다.
-   매개 변수 *Control_Table_Column_Name*은 상위 워터마크 값을 저장할 외부 제어 테이블의 열 이름입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 로드하려는 원본 테이블을 살펴보고 새 행 또는 업데이트된 행을 분할하는 데 사용할 수 있는 상위 워터마크 열을 정의합니다. 일반적으로 이 열의 형식은 datetime 또는 INT 등이 될 수 있으며 해당 데이터는 새 행이 추가될 때 계속 커집니다.  아래의 샘플 원본 테이블(테이블 이름: data_source_table)에서 열 *LastModifytime*을 상위 워터마크 열로 사용할 수 있습니다.

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
    
2. SQL Server 또는 SQL Azure에 델타 데이터 로드를 위해 상위 워터마크 값을 저장하는 제어 테이블을 만듭니다. 아래 예제에서는 제어 테이블의 이름이 *watermarktable*임을 확인할 수 있습니다. 여기서 상위 워터마크 값을 저장하기 위한 열 이름은 *WatermarkValue*이고 해당 형식은 *datetime*입니다.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 제어 테이블을 만드는 데 사용되는 동일한 SQL Server 또는 SQL Azure에서 저장 프로시저를 만듭니다. 이 저장 프로시저는 다음번에 델타 데이터 로드를 위해 새로운 상위 워터마크 값을 외부 제어 테이블에 쓰는 데 사용됩니다.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 템플릿 **데이터베이스에서 델타 복사**로 이동한 후 데이터를 복사해올 원본 데이터베이스에 대한 **새 연결**을 만듭니다.

    ![원본 테이블에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 데이터 사본을 저장할 대상 데이터 저장소에 대한 **새 연결**을 만듭니다.

    ![대상 테이블에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 외부 제어 테이블 및 저장 프로시저에 대한 **새 연결**을 만듭니다.  #2 및 #3단계에서 제어 테이블 및 저장 프로시저를 만들었던 데이터베이스에 연결됩니다.

    ![제어 테이블 데이터 저장소에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. **이 템플릿 사용**을 클릭합니다.

     ![이 템플릿 사용](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. 다음 예제와 같이 패널에서 사용할 수 있는 파이프라인이 표시됩니다.

     ![파이프라인 검토](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 저장 프로시저 작업을 클릭하고, **저장 프로시저 이름**을 선택한 후, **매개 변수 가져오기**를 클릭하고, **동적 콘텐츠 추가**를 클릭합니다.  

     ![저장 프로시저 작업 설정](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. 콘텐츠 **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** 를 작성하고 **마침**을 클릭합니다.  

     ![저장 프로시저에 대한 매개 변수 콘텐츠 작성](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. **디버그**를 클릭하고 매개 변수를 입력한 후 **마침**을 클릭합니다.

    ![디버그 클릭](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 다음 예제와 같이 패널에서 사용할 수 있는 결과가 표시됩니다.

    ![결과 검토](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 원본 테이블에 새 행을 만들 수 있습니다.  새 행을 만드는 샘플 SQL은 다음과 같을 수 있습니다.

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. **디버그**를 클릭하여 파이프라인을 다시 실행하고, 매개 변수를 입력한 후 **마침**을 클릭합니다.

    ![디버그 클릭](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. 대상에 새 행만 복사된 것을 볼 수 있습니다.

15. (선택 사항) SQL Data Warehouse를 데이터 대상으로 선택하는 경우 또한 Azure Blob 스토리지의 연결을 SQL Data Warehouse Polybase에 필요한 스테이징으로 입력해야 합니다.  Blob 스토리지의 컨테이너를 이미 만들었는지 확인하세요.  
    
    ![PolyBase 구성](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)