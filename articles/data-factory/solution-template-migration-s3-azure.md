---
title: 아마존 S3에서 Azure 데이터 레이크 스토리지 Gen2로 데이터 마이그레이션
description: 외부 제어 테이블을 사용하여 Azure 데이터 팩터리와 함께 AWS S3에 파티션 목록을 저장하여 Amazon S3에서 데이터를 마이그레이션하는 솔루션 템플릿을 사용하는 방법을 알아봅니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: 23d799f84cb3ac3ca911a5669041b0a25394a7ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414769"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>아마존 S3에서 Azure 데이터 레이크 스토리지 Gen2로 데이터 마이그레이션

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

템플릿을 사용하여 Amazon S3에서 Azure Data Lake Storage Gen2로 수억 개의 파일로 구성된 페타바이트 규모의 데이터를 마이그레이션합니다. 

 > [!NOTE]
 > AWS S3에서 Azure로 작은 데이터 볼륨을 복사하려는 경우(예: 10TB 미만) [Azure 데이터 팩터리 복사 데이터 도구를](copy-data-tool.md)보다 효율적이고 쉽게 사용할 수 있습니다. 이 문서에서 설명하는 템플릿은 필요한 것 이상입니다.

## <a name="about-the-solution-templates"></a>솔루션 템플릿 소개

특히 10TB 이상의 데이터를 마이그레이션할 때는 데이터 파티션을 권장합니다. 데이터를 분할하려면 '접두사' 설정을 활용하여 Amazon S3의 폴더와 파일을 이름으로 필터링한 다음 각 ADF 복사 작업은 한 번에 하나의 파티션을 복사할 수 있습니다. 더 나은 처리량을 위해 여러 ADF 복사 작업을 동시에 실행할 수 있습니다.

데이터 마이그레이션에는 일반적으로 일회성 기록 데이터 마이그레이션과 AWS S3에서 Azure로의 변경 내용을 주기적으로 동기화해야 합니다. 아래에는 한 템플릿이 일회성 기록 데이터 마이그레이션을 다루고 다른 템플릿은 AWS S3에서 Azure로의 변경 내용을 동기화하는 것을 다룹니다.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>템플릿이 Amazon S3에서 Azure 데이터 레이크 저장소 Gen2로 기록 데이터를 마이그레이션하려면

이*템플릿(템플릿 이름: AWS S3에서 Azure Data Lake Storage Gen2로 기록 데이터를 마이그레이션)은*Azure SQL Database의 외부 제어 테이블에 파티션 목록을 작성했다고 가정합니다. 따라서 *Lookup* 활동을 사용하여 외부 제어 테이블에서 파티션 목록을 검색하고, 각 파티션을 반복하며, 각 ADF 복사 작업 복사본을 한 번에 하나씩 복사합니다. 복사 작업이 완료되면 저장 *프로시저* 작업을 사용하여 제어 테이블에서 각 파티션을 복사하는 상태를 업데이트합니다.

템플릿에는 다음 5가지 활동이 포함되어 있습니다.
- **조회는** 외부 제어 테이블에서 Azure Data Lake Storage Gen2에 복사되지 않은 파티션을 검색합니다. 테이블 이름은 *s3_partition_control_table* 테이블에서 데이터를 로드하는 쿼리는 *"s3_partition_control_table 에서 Select PartitionPrefix 성공또는 실패 = 0"입니다.*
- **ForEach는** *조회* 활동에서 파티션 목록을 얻고 각 파티션을 *TriggerCopy* 활동으로 이격합니다. *batchCount를* 설정하여 여러 ADF 복사 작업을 동시에 실행할 수 있습니다. 이 템플릿에서 2를 설정했습니다.
- **실행 파이프라인은** *CopyFolderPartitionFromS3* 파이프라인을 실행합니다. 각 복사 작업 복사본을 파티션으로 만들기 위해 다른 파이프라인을 만드는 이유는 실패한 복사 작업을 쉽게 다시 실행하여 AWS S3에서 특정 파티션을 다시 로드할 수 있기 때문입니다. 다른 파티션을 로드하는 다른 모든 복사 작업은 영향을 받지 않습니다.
- AWS S3에서 Azure 데이터 레이크 저장소 Gen2로 각 파티션을 **복사합니다.**
- **SqlServer저장프로시저는** 제어 테이블에서 각 파티션을 복사하는 상태를 업데이트합니다.

템플릿에는 다음 두 가지 매개 변수가 포함되어 있습니다.
- **AWS_S3_bucketName** AWS S3에서 데이터를 마이그레이션하려는 버킷 이름입니다. AWS S3의 여러 버킷에서 데이터를 마이그레이션하려는 경우 외부 제어 테이블에 열을 하나 더 추가하여 각 파티션에 대한 버킷 이름을 저장하고 파이프라인을 업데이트하여 해당 열에서 데이터를 검색할 수도 있습니다.
- **Azure_Storage_fileSystem** Azure Data Lake Storage Gen2에서 데이터를 마이그레이션하려는 파일시스템 이름입니다.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>템플릿이 Amazon S3에서 Azure 데이터 레이크 저장소 Gen2로만 변경된 파일을 복사하려면

이*템플릿(템플릿 이름: AWS S3에서 Azure Data Lake Storage Gen2로 델타 데이터 복사)은*각 파일의 LastModifiedTime을 사용하여 AWS S3에서 Azure로만 새 또는 업데이트된 파일을 복사합니다. 파일 또는 폴더가 AWS S3의 파일 또는 폴더 이름의 일부로 시간 분할 된 시간 (예 : /yyyy/ mm/dd/file.csv)으로 이미 분할 된 경우 이 [자습서로](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) 이동하여 새 파일을 증분로드하기위한 보다 성능있는 접근 방식을 얻을 수 있습니다. 이 템플릿은 Azure SQL Database의 외부 제어 테이블에 파티션 목록을 작성했다고 가정합니다. 따라서 *Lookup* 활동을 사용하여 외부 제어 테이블에서 파티션 목록을 검색하고, 각 파티션을 반복하며, 각 ADF 복사 작업 복사본을 한 번에 하나씩 복사합니다. 각 복사 작업이 AWS S3에서 파일을 복사하기 시작하면 LastModifiedTime 속성을 사용하여 새 파일또는 업데이트된 파일만 식별하고 복사합니다. 복사 작업이 완료되면 저장 *프로시저* 작업을 사용하여 제어 테이블에서 각 파티션을 복사하는 상태를 업데이트합니다.

템플릿에는 다음 7가지 활동이 포함되어 있습니다.
- **조회는** 외부 제어 테이블에서 파티션을 검색합니다. 테이블 이름이 *s3_partition_delta_control_table* 테이블에서 데이터를 로드하는 쿼리는 *"s3_partition_delta_control_table 고유한 PartitionPrefix 선택"입니다.*
- **ForEach는** *조회* 활동에서 파티션 목록을 얻고 각 파티션을 *TriggerDeltaCopy* 활동으로 이량거합니다. *batchCount를* 설정하여 여러 ADF 복사 작업을 동시에 실행할 수 있습니다. 이 템플릿에서 2를 설정했습니다.
- **실행 파이프라인은** *델타복사폴더파티션FromS3* 파이프라인을 실행합니다. 각 복사 작업 복사본을 파티션으로 만들기 위해 다른 파이프라인을 만드는 이유는 실패한 복사 작업을 쉽게 다시 실행하여 AWS S3에서 특정 파티션을 다시 로드할 수 있기 때문입니다. 다른 파티션을 로드하는 다른 모든 복사 작업은 영향을 받지 않습니다.
- **조회는** LastModifiedTime을 통해 새 파일또는 업데이트된 파일을 식별할 수 있도록 외부 제어 테이블에서 마지막 복사 작업 런타임을 검색합니다. 테이블 이름은 *s3_partition_delta_control_table* 테이블에서 데이터를 로드하는 쿼리는 *"partitionPrefix = '@{파이프라인()"과 성공또는 실패 = 1"s3_partition_delta_control_table LastModifiedTime으로 선택합니다.*
- AWS S3에서 Azure Data Lake 저장소 Gen2에 각 파티션에 대해서만 새 파일또는 변경된 파일을 **복사합니다.** *수정된DatetimeStart의* 속성은 마지막 복사 작업 런타임으로 설정됩니다. *수정된DatetimeEnd의* 속성은 현재 복사 작업 런타임으로 설정됩니다. 시간이 UTC 표준 시간대에 적용됩니다.
- **SqlServer저장프로시저는** 각 파티션을 복사하는 상태를 업데이트하고 성공하면 제어 테이블에서 실행 시간을 복사합니다. 성공또는실패의열은1로 설정됩니다.
- **SqlServer저장프로시저는** 각 파티션을 복사하는 상태를 업데이트하고 실패할 때 제어 테이블에서 실행 시간을 복사합니다. 성공또는실패의열은0으로설정됩니다.

템플릿에는 다음 두 가지 매개 변수가 포함되어 있습니다.
- **AWS_S3_bucketName** AWS S3에서 데이터를 마이그레이션하려는 버킷 이름입니다. AWS S3의 여러 버킷에서 데이터를 마이그레이션하려는 경우 외부 제어 테이블에 열을 하나 더 추가하여 각 파티션에 대한 버킷 이름을 저장하고 파이프라인을 업데이트하여 해당 열에서 데이터를 검색할 수도 있습니다.
- **Azure_Storage_fileSystem** Azure Data Lake Storage Gen2에서 데이터를 마이그레이션하려는 파일시스템 이름입니다.

## <a name="how-to-use-these-two-solution-templates"></a>이 두 솔루션 템플릿을 사용하는 방법

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>템플릿이 Amazon S3에서 Azure 데이터 레이크 저장소 Gen2로 기록 데이터를 마이그레이션하려면 

1. Azure SQL Database에서 제어 테이블을 만들어 AWS S3의 파티션 목록을 저장합니다. 

    > [!NOTE]
    > 테이블 이름이 s3_partition_control_table.
    > 컨트롤 테이블의 스키마는 PartitionPrefix 및 SuccessOrFailure이며, PartitionPrefix는 S3의 접두사 설정으로 Amazon S3의 폴더와 파일을 이름으로 필터링하고 SuccessOrFailure은 각 파티션을 복사하는 상태입니다.
    > 컨트롤 테이블에정의된 5개의 파티션이 있으며 각 파티션을 복사하는 기본 상태는 0입니다.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. 제어 테이블에 대해 동일한 Azure SQL 데이터베이스에 저장 프로시저를 만듭니다. 

    > [!NOTE]
    > 저장 프로시저의 이름은 sp_update_partition_success. ADF 파이프라인의 SqlServer저장프로시서 활동에 의해 호출됩니다.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. **AWS S3에서 Azure 데이터 레이크 스토리지 Gen2 템플릿으로 마이그레이션된 기록** 데이터로 이동합니다. 외부 제어 테이블, AWS S3에 대한 연결을 데이터 원본 저장소로 입력하고 Azure Data Lake Storage Gen2를 대상 저장소로 입력합니다. 외부 제어 테이블과 저장 프로시저는 동일한 연결을 참조합니다.

    ![새 연결 만들기](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. **이 템플릿 사용을**선택합니다.

    ![이 템플릿 사용](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. 다음 예제와 같이 2개의 파이프라인과 3개의 데이터 집합이 만들어졌습니다.

    ![파이프라인 검토](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.

    ![**디버그**](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. 다음 예제와 유사한 결과가 표시됩니다.

    ![결과 검토](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>템플릿이 Amazon S3에서 Azure 데이터 레이크 저장소 Gen2로만 변경된 파일을 복사하려면

1. Azure SQL Database에서 제어 테이블을 만들어 AWS S3의 파티션 목록을 저장합니다. 

    > [!NOTE]
    > 테이블 이름이 s3_partition_delta_control_table.
    > 컨트롤 테이블의 스키마는 PartitionPrefix, JobRunTime 및 SuccessOrFailure이며, PartitionPrefix는 S3의 접두사 설정으로 아마존 S3의 폴더와 파일을 이름으로 필터링하고, JobRunTime은 복사 작업이 실행될 때 의 날짜 시간 값이며, SuccessOrFailure은 각 파티션을 복사할 때 의 날짜 값이며, SuccessOrFailure은 이 파티션이 Azure에 복사되지 않음을 의미하며 1은 이 파티션이 Azure에 복사되지 않음을 의미합니다.
    > 컨트롤 테이블에정의된 5개의 파티션이 있습니다. JobRunTime의 기본값은 일회성 기록 데이터 마이그레이션이 시작되는 시간일 수 있습니다. ADF 복사 활동은 해당 시간 이후에 마지막으로 수정된 AWS S3의 파일을 복사합니다. 각 파티션을 복사하는 기본 상태는 1입니다.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. 제어 테이블에 대해 동일한 Azure SQL 데이터베이스에 저장 프로시저를 만듭니다. 

    > [!NOTE]
    > 저장 프로시저의 이름은 sp_insert_partition_JobRunTime_success. ADF 파이프라인의 SqlServer저장프로시서 활동에 의해 호출됩니다.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. **AWS S3에서 Azure 데이터 레이크 저장소 Gen2 템플릿으로 델타 복사** 데이터로 이동합니다. 외부 제어 테이블, AWS S3에 대한 연결을 데이터 원본 저장소로 입력하고 Azure Data Lake Storage Gen2를 대상 저장소로 입력합니다. 외부 제어 테이블과 저장 프로시저는 동일한 연결을 참조합니다.

    ![새 연결 만들기](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. **이 템플릿 사용을**선택합니다.

    ![이 템플릿 사용](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. 다음 예제와 같이 2개의 파이프라인과 3개의 데이터 집합이 만들어졌습니다.

    ![파이프라인 검토](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. **디버그를**선택하고 **매개 변수를**입력한 다음 **완료를**선택합니다.

    ![**디버그**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. 다음 예제와 유사한 결과가 표시됩니다.

    ![결과 검토](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. 또한 *"s3_partition_delta_control_table * 선택"을*쿼리하여 컨트롤 테이블의 결과를 확인할 수 있으며 다음 예제와 유사한 출력을 볼 수 있습니다.

    ![결과 검토](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>다음 단계

- [여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)
- [파일 이동](solution-template-move-files.md)