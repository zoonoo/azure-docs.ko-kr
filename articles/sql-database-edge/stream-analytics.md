---
title: SQL Database DAC 패키지를 사용 하 고 Azure SQL Database Edge를 사용 하 여 작업 Stream Analytics Microsoft Docs
description: Edge SQL Database에서 Stream Analytics 작업을 사용 하는 방법에 대해 알아봅니다.
keywords: sql database edge, stream analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: c3ed84e06f693925ed8b484070616e223929e401
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108752"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>SQL Database DAC 패키지 및 Stream Analytics 작업을 SQL Database Edge와 함께 사용

Azure SQL Database Edge 미리 보기는 IoT 및 Edge 배포를 위해 설계 된 최적화 된 관계형 데이터베이스 엔진입니다. 업계 최고의 성능, 보안 및 쿼리 처리 기능을 제공 하는 최신 버전의 Microsoft SQL Server 데이터베이스 엔진을 기반으로 합니다. Azure SQL Database Edge SQL Server의 업계 최고의 관계형 데이터베이스 관리 기능과 함께 실시간 분석 및 복잡 한 이벤트 처리를 위한 구축 가능한 스트리밍 기능을 제공 합니다.

Azure SQL Database Edge에서는 사용자가 SQL Database Edge를 배포 하는 동안 [SQL DATABASE DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 패키지를 배포할 수 있도록 하는 SQLPackage의 네이티브 구현도 제공 합니다.

Azure SQL Database Edge는 *모듈 쌍의 desired 속성* 옵션을 통해 IoT Edge 모듈의 선택적 매개 변수 두 개를 노출 합니다.

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|필드 | 설명 |
|------|-------------|
| SQLPackage | SQL Database DAC 패키지를 포함 하는 * .zip 파일의 URI를 Azure Blob Storage 합니다.
| ASAJobInfo | GLOBAL.ASA에 지 작업의 URI를 Azure Blob Storage 합니다. 이 작업을 게시 하는 방법에 대 한 자세한 내용은 [SQL Database edge에 대 한 주문형 edge 작업 게시](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)를 참조 하세요.

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database Edge에서 SQL Database DAC 패키지 사용

SQL Database Edge에서 SQL Database DAC 패키지 (* .dacpac)를 사용 하려면 아래에 설명 된 단계를 수행 하세요.

1. SQL Database DAC 패키지를 만들거나 추출 합니다. 기존 [데이터베이스에서 DAC를 추출](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) 하는 방법에 설명 된 개념을 사용 하 여 기존 SQL Database에 대 한 DacPac를 생성할 수 있습니다.

2. * *.Dacpac* 를 압축 하 고 Azure Blob storage 계정에 업로드 합니다. Azure Blob Storage에 파일을 업로드 하는 방법에 대 한 자세한 내용은 Azure Portal를 사용 하 여 [Blob 업로드, 다운로드 및 나열](../storage/blobs/storage-quickstart-blobs-portal.md)을 참조 하세요.

3. Azure Portal를 사용 하 여 zip 파일에 대 한 SAS 서명을 생성 합니다. 자세한 내용은 [SAS (공유 액세스 서명)를 사용 하 여 액세스 위임](../storage/common/storage-sas-overview.md)을 참조 하세요.

4. DAC 패키지에 대 한 SAS URI를 포함 하도록 SQL Database Edge 모듈 구성을 업데이트 합니다. SQL Database Edge 모듈을 업데이트 하려면

    1. Azure Portal에서 IoT Hub 배포로 이동 합니다.

    2. 왼쪽 창에서 **IoT Edge**을 클릭 합니다.

    3. **IoT Edge** 페이지에서 SQL Database Edge 모듈이 배포 된 IoT Edge를 찾아 클릭 합니다.

    4. *IoT Edge 장치* 장치 페이지에서 **모듈 설정**을 클릭 합니다. 

    5. **모듈 설정** 페이지에서 SQL Database Edge 모듈에 대해 *구성* 을 클릭 합니다. 

    6. **사용자 지정 모듈 IoT Edge** 창에서 *모듈 쌍의 desired 속성* 을 선택 하 고 아래 예제와 같이 SQLPackage 옵션에 대 한 URI를 포함 하도록 desired 속성을 업데이트 합니다. 

        > [!NOTE]
        > 아래 SAS URI는 설명을 위한 것입니다. URI를 배포의 실제 URI로 바꾸세요.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. **저장**을 클릭합니다.

    8. **모듈 설정** 페이지에서 *다음*을 클릭 합니다.

    9. **모듈 설정** 페이지에서 *다음* 을 클릭 한 다음 **제출**을 클릭 합니다.

5. 모듈 업데이트를 게시 하 고, dacpac 파일이 다운로드 되 고, 압축을 해제 하 고, SQL Database에 지 인스턴스에 대해 배포 합니다.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>SQL Database Edge를 사용 하 여 스트리밍 작업 사용

Azure SQL Database Edge에는 stream analytics 런타임의 네이티브 구현이 있습니다. 이렇게 하면 사용자가 Azure Stream Analytics에 지 작업을 만들고 해당 작업을 SQL Database에 지 스트리밍 작업으로 배포할 수 있습니다. Stream analytics Edge 작업을 만들려면 다음 단계를 수행 합니다.

1. 미리 보기 [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)을 사용 하 여 Azure Portal로 이동 합니다. 사용자는이 미리 보기 URL을 사용 하 여 stream analytics edge 작업에 대해 SQL Database 출력을 구성할 수 있습니다.

2. 새 **IoT Edge의 Azure Stream Analytics** 작업을 만들고 **Edge**를 대상으로 하는 호스팅 환경을 선택 합니다.

3. Azure Stream Analytics 작업에 대 한 *입력* 및 *출력* 을 정의 합니다. 각 SQL 출력 (아래 구성 됨)은 데이터베이스 내의 단일 테이블에 연결 됩니다. 데이터를 여러 테이블에 스트리밍하려면 여러 개의 SQL Database 출력을 만들어야 합니다. SQL 출력은 다른 데이터베이스를 가리키도록 구성할 수 있습니다.

    *입력-에 지 작업에 대 한 입력으로 EdgeHub을 선택 하 고 리소스 정보를 입력 합니다.*

    *출력-SQL Database를 출력으로 선택 하 고, "SQL Database 설정을 수동으로 제공" 하 고, 데이터베이스 및 테이블에 대 한 구성 세부 정보를 제공 합니다.*

    |필드      | 설명 |
    |---------------|-------------|
    |출력 별칭 | 출력 별칭의 이름입니다.|
    |데이터베이스 | SQL Database 이름입니다. 이 이름은 올바른 데이터베이스 이름 이어야 하며,이는 SQL Database Edge 인스턴스에 존재 합니다.|
    |서버 이름 | SQL 인스턴스의 이름 (또는 IP 주소)과 포트 번호 정보입니다. SQL Database Edge 배포의 경우 **tcp:., 1433을** 서버 이름으로 사용할 수 있습니다.|
    |사용자 이름 | 위에서 언급 된 데이터베이스에 대 한 데이터 판독기 및 데이터 기록기 액세스 권한이 있는 SQL 로그인 계정입니다.|
    |암호 | 위에서 언급 한 SQL 로그인 계정의 암호입니다.|
    |Table | 스트리밍 작업에 대해 출력 되는 테이블의 이름입니다.|
    |분할 상속| 이 SQL 출력 구성 옵션을 사용 하면 이전 쿼리 단계 또는 입력의 파티션 구성표를 상속할 수 있습니다. 이 기능을 사용 하도록 설정 하면 디스크 기반 테이블에 기록 하 고 작업에 대 한 완전 한 병렬 토폴로지를 사용 하 여 더 나은 처리량을 볼 수 있습니다.|
    |Batch 크기| 일괄 처리 크기는 모든 bulk insert 트랜잭션과 함께 전송 되는 최대 레코드 수입니다.|

    아래의 샘플 입/출력 구성:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Azure Stream Analytics에 대 한 SQL 출력 어댑터에 대 한 자세한 내용은 [Azure SQL Database Azure Stream Analytics 출력](../stream-analytics/stream-analytics-sql-output-perf.md)을 참조 하세요.

4. Edge 작업에 대해 GLOBAL.ASA 작업 쿼리를 정의 합니다. 이 쿼리는 정의 된 입력/출력 별칭을 쿼리에서 입력 및 출력 이름으로 사용 해야 합니다. 자세한 내용은 [Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)를 참조 하세요.

5. Edge 작업에 대 한 저장소 계정 설정을 설정 합니다. 저장소 계정은 edge 작업의 게시 대상으로 사용 됩니다.

6. 구성 아래에서 게시를 선택 하 고 게시 단추를 클릭 합니다. SQL Database Edge 모듈에 사용할 SAS URL을 저장 합니다.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>SQL Database Edge에 stream analytics edge 작업을 배포 합니다.

스트리밍 작업을 SQL Database Edge 모듈에 배포 하려면 위 단계에서 스트리밍 작업에 대 한 SAS URI를 포함 하도록 SQL Database Edge 모듈 구성을 업데이트 합니다. SQL Database Edge 모듈을 업데이트 하려면

1. Azure Portal에서 IoT Hub 배포로 이동 합니다.

2. 왼쪽 창에서 **IoT Edge**을 클릭 합니다.

3. **IoT Edge** 페이지에서 SQL Database Edge 모듈이 배포 된 IoT Edge를 찾아 클릭 합니다.

4. *IoT Edge 장치* 장치 페이지에서 **모듈 설정**을 클릭 합니다. 

5. **모듈 설정** 페이지에서 SQL Database Edge 모듈에 대해 *구성* 을 클릭 합니다. 

6. **사용자 지정 모듈 IoT Edge** 창에서 모듈 쌍 *의 desired 속성* 을 선택 하 고 아래 예제와 같이 asajobinfo 옵션에 대 한 URI를 포함 하도록 desired 속성을 업데이트 합니다. 

    > [!NOTE]
    > 아래 SAS URI는 설명을 위한 것입니다. URI를 배포의 실제 URI로 바꾸세요.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. **저장**을 클릭합니다.

8. **모듈 설정** 페이지에서 *다음*을 클릭 합니다.

9. **모듈 설정** 페이지에서 *다음* 을 클릭 한 다음 **제출**을 클릭 합니다.

10. 모듈 업데이트를 게시 하 고, stream analytics 작업 파일을 다운로드 하 고, 압축을 해제 하 고, SQL Database에 지 인스턴스에 대해 배포 합니다.

## <a name="next-steps"></a>다음 단계

- 가격 책정 및 가용성 관련 세부 정보는 [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)를 참조 하세요.
- 구독에 대 한 Azure SQL Database Edge 사용을 요청 합니다.
- 시작 하려면 다음을 참조 하세요.
  - [Azure Portal를 통해 SQL Database에 지 배포](deploy-portal.md)
