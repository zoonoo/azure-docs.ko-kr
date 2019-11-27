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
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384170"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>SQL Database DAC 패키지를 사용 하 고 SQL Database Edge를 사용 하 여 작업 Stream Analytics

Azure SQL Database Edge 미리 보기는 IoT 및 Edge 배포를 위해 설계 된 최적화 된 관계형 데이터베이스 엔진입니다. 업계 최고의 성능, 보안 및 쿼리 처리 기능을 제공 하는 최신 버전의 Microsoft SQL Server 데이터베이스 엔진을 기반으로 합니다. Azure SQL Database Edge SQL Server의 업계 최고의 관계형 데이터베이스 관리 기능과 함께 실시간 분석 및 복잡 한 이벤트 처리를 위한 구축 가능한 스트리밍 기능을 제공 합니다.

Azure SQL Database Edge는 SQL Database Edge를 배포 하는 동안 [SQL DATABASE DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 패키지를 배포할 수 있도록 하는 SqlPackage의 네이티브 구현도 제공 합니다.

Azure SQL Database Edge는 IoT Edge 모듈의 `module twin's desired properties` 옵션을 통해 두 개의 선택적 매개 변수를 노출 합니다.

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
| SqlPackage | SQL Database DAC 패키지를 포함 하는 * .zip 파일의 Azure Blob storage URI입니다.
| ASAJobInfo | 저장소에 지 작업에 대 한 Azure Blob 저장소 URI입니다. 자세한 내용은 [SQL Database Edge에 대 한 Global.asa edge 작업 게시](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)를 참조 하세요.

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database Edge에서 SQL Database DAC 패키지 사용

SQL Database Edge에서 SQL Database DAC 패키지 (* .dacpac)를 사용 하려면 다음 단계를 수행 합니다.

1. SQL Database DAC 패키지를 만들거나 추출 합니다. 기존 SQL Server 데이터베이스용 DAC 패키지를 생성 하는 방법에 대 한 자세한 내용은 [데이터베이스에서 Dac 추출](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) 을 참조 하세요.

2. \* .Dacpac를 압축 하 고 Azure Blob storage 계정에 업로드 합니다. Azure Blob storage에 파일을 업로드 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Blob 업로드, 다운로드 및 나열](../storage/blobs/storage-quickstart-blobs-portal.md)을 참조 하세요.

3. Azure Portal를 사용 하 여 zip 파일에 대 한 공유 액세스 서명을 생성 합니다. 자세한 내용은 [SAS (공유 액세스 서명)를 사용 하 여 액세스 위임](../storage/common/storage-sas-overview.md)을 참조 하세요.

4. DAC 패키지에 대 한 공유 액세스 URI를 포함 하도록 SQL Database Edge 모듈 구성을 업데이트 합니다. SQL Database Edge 모듈을 업데이트 하려면 다음 단계를 수행 합니다.

    1. Azure Portal에서 IoT Hub 배포로 이동 합니다.

    2. 왼쪽 창에서 **IoT Edge**를 선택합니다.

    3. **IoT Edge** 페이지에서 SQL Database Edge 모듈이 배포 되는 IoT Edge를 찾아 선택 합니다.

    4. **IoT Edge 장치** 장치 페이지에서 **모듈 설정**을 선택 합니다.

    5. **모듈 설정** 페이지에서 SQL Database Edge 모듈에 대해 **구성** 을 선택 합니다.

    6. **사용자 지정 모듈 IoT Edge** 창에서 모듈 쌍 **의 desired 속성 설정**을 선택 합니다. 다음 예제와 같이 `SQLPackage` 옵션에 대 한 URI를 포함 하도록 desired 속성을 업데이트 합니다.

        > [!NOTE]
        > 다음 JSON의 SAS URI는 단지 예입니다. URI를 배포의 실제 URI로 바꿉니다.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. **저장**을 선택합니다.

    8. **모듈 설정** 페이지에서 **다음**을 선택 합니다.

    9. **모듈 설정** 페이지에서 **다음** 을 선택 하 고 **제출**을 선택 합니다.

5. 모듈을 업데이트 한 후에는 DAC 패키지 파일이 다운로드 되 고 압축을 푼 다음 SQL Database에 지 인스턴스에 대해 배포 됩니다.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>SQL Database Edge를 사용 하 여 스트리밍 작업 사용

Azure SQL Database Edge에는 stream analytics 런타임의 네이티브 구현이 있습니다. 이 구현을 통해 edge 작업 Azure Stream Analytics 만들고 해당 작업을 SQL Database에 지 스트리밍 작업으로 배포할 수 있습니다. Stream Analytics edge 작업을 만들려면 다음 단계를 완료 합니다.

1. 미리 보기 [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)을 사용 하 여 Azure Portal로 이동 합니다. 이 미리 보기 URL을 사용 하 여 Stream Analytics에 지 작업에 대 한 SQL Database 출력을 구성할 수 있습니다.

2. 새 **IoT Edge의 Azure Stream Analytics** 작업을 만듭니다. **Edge**를 대상으로 하는 호스팅 환경을 선택 합니다.

3. Azure Stream Analytics 작업에 대 한 입력 및 출력을 정의 합니다. 여기에서 설정할 각 SQL 출력은 데이터베이스의 단일 테이블에 연결 됩니다. 데이터를 여러 테이블에 스트리밍하려면 여러 개의 SQL Database 출력을 만들어야 합니다. SQL 출력을 다른 데이터베이스를 가리키도록 구성할 수 있습니다.

    **입력**. Edge 작업의 입력으로 EdgeHub을 선택 하 고 리소스 정보를 제공 합니다.

    **출력**. As output SQL Database를 선택 합니다. **수동으로 SQL Database 설정 제공**을 선택 합니다. 데이터베이스 및 테이블에 대 한 구성 세부 정보를 제공 합니다.

    |필드      | 설명 |
    |---------------|-------------|
    |출력 별칭 | 출력 별칭의 이름입니다.|
    |데이터베이스 | SQL 데이터베이스의 이름입니다. SQL Database Edge 인스턴스에 있는 데이터베이스의 올바른 이름 이어야 합니다.|
    |서버 이름 | SQL 인스턴스의 이름 (또는 IP 주소)과 포트 번호 정보입니다. SQL Database Edge 배포의 경우 서버 이름에 **tcp:., 1433** 을 사용할 수 있습니다.|
    |사용자 이름 | 이전에 지정한 데이터베이스에 대 한 데이터 판독기 및 데이터 기록기 액세스 권한이 있는 SQL 로그인 계정입니다.|
    |암호 | 이전에 지정한 SQL 로그인 계정에 대 한 암호입니다.|
    |테이블 | 스트리밍 작업에 대해 출력 되는 테이블의 이름입니다.|
    |분할 상속| 이전 쿼리 단계 또는 입력의 파티션 구성표를 상속할 수 있도록 합니다. 이 옵션을 사용 하도록 설정 하면 디스크 기반 테이블에 쓸 때 작업에 대 한 완전 한 병렬 토폴로지가 있는 경우 더 나은 처리량을 볼 수 있습니다.|
    |Batch 크기| 모든 bulk insert 트랜잭션과 함께 전송 되는 최대 레코드 수입니다.|

    다음은 샘플 입/출력 구성입니다.

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
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

6. **구성**아래에서 **게시**를 선택한 후 **게시** 단추를 선택 합니다. SQL Database Edge 모듈에 사용할 SAS URI를 저장 합니다.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Edge 작업을 SQL Database에 Stream Analytics 배포

스트리밍 작업을 SQL Database Edge 모듈에 배포 하려면 이전 단계의 스트리밍 작업에 대 한 SAS URI를 포함 하도록 SQL Database Edge 모듈 구성을 업데이트 합니다. SQL Database Edge 모듈을 업데이트 하려면:

1. Azure Portal에서 IoT Hub 배포로 이동 합니다.

2. 왼쪽 창에서 **IoT Edge**를 선택합니다.

3. **IoT Edge** 페이지에서 SQL Database Edge 모듈이 배포 되는 IoT Edge를 찾아 선택 합니다.

4. **IoT Edge 장치** 장치 페이지에서 **모듈 설정**을 선택 합니다.

5. **모듈 설정** 페이지에서 SQL Database Edge 모듈에 대해 **구성** 을 선택 합니다.

6. **사용자 지정 모듈 IoT Edge** 창에서 모듈 쌍 **의 desired 속성 설정**을 선택 합니다. 다음 예제와 같이 `ASAJobInfo` 옵션에 대 한 URI를 포함 하도록 desired 속성을 업데이트 합니다.

    > [!NOTE]
    > 다음 JSON의 SAS URI는 단지 예입니다. URI를 배포의 실제 URI로 바꿉니다.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. **저장**을 선택합니다.

8. **모듈 설정** 페이지에서 **다음**을 선택 합니다.

9. **모듈 설정** 페이지에서 **다음** 을 선택 하 고 **제출**을 선택 합니다.

10. 모듈 업데이트 후에는 stream analytics 작업 파일이 다운로드 되 고, 압축을 해제 하 고, SQL Database에 지 인스턴스에 대해 배포 됩니다.

## <a name="next-steps"></a>다음 단계

- 가격 책정 및 가용성에 대 한 자세한 내용은 [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)를 참조 하세요.
- 구독에 대 한 Azure SQL Database Edge 활성화를 요청 합니다.
- 시작 하려면 [Azure Portal를 통해 SQL Database Edge 배포](deploy-portal.md)를 참조 하세요.
