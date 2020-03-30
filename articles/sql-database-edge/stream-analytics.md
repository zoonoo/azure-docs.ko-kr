---
title: Azure SQL 데이터베이스 에지에서 SQL 데이터베이스 DAC 패키지 및 스트림 분석 작업 사용 | 마이크로 소프트 문서
description: SQL 데이터베이스 에지에서 스트림 분석 작업 사용에 대해 알아보기
keywords: sql 데이터베이스 에지, 스트림 분석, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384170"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>SQL 데이터베이스 에지에서 SQL 데이터베이스 DAC 패키지 및 스트림 분석 작업 사용

Azure SQL Database 에지 미리 보기는 IoT 및 에지 배포를 위해 준비된 최적화된 관계형 데이터베이스 엔진입니다. 업계 최고의 성능, 보안 및 쿼리 처리 기능을 제공하는 최신 버전의 Microsoft SQL Server 데이터베이스 엔진을 기반으로 합니다. Azure SQL Database Edge는 SQL Server의 업계 최고의 관계형 데이터베이스 관리 기능과 함께 실시간 분석 및 복잡한 이벤트 처리를 위한 내장 스트리밍 기능을 제공합니다.

또한 Azure SQL Database Edge는 SQL Database Edge를 배포하는 동안 [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 패키지를 배포할 수 있는 SqlPackage.exe의 기본 구현을 제공합니다.

Azure SQL Database Edge는 IoT `module twin's desired properties` Edge 모듈의 옵션을 통해 두 가지 선택적 매개 변수를 노출합니다.

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
| SqlPackage | SQL Database DAC 패키지를 포함하는 *.zip 파일에 대한 Azure Blob 저장소 URI입니다.
| 아사조인 | ASA Edge 작업에 대한 Azure Blob 저장소 URI입니다. 자세한 내용은 [SQL Database Edge에 대한 ASA Edge 작업 게시를](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)참조하십시오.

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL 데이터베이스 에지가 있는 SQL 데이터베이스 DAC 패키지 사용

SQL 데이터베이스 가장자리와 함께 SQL 데이터베이스 DAC 패키지(*.dacpac)를 사용하려면 다음 단계를 수행하십시오.

1. SQL 데이터베이스 DAC 패키지를 만들거나 추출합니다. 기존 SQL Server 데이터베이스에 대한 DAC 패키지를 생성하는 방법에 대한 자세한 내용은 [데이터베이스에서 DAC 추출을](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) 참조하십시오.

2. *.dacpac을 압축하고 Azure Blob 저장소 계정에 업로드합니다. Azure Blob 저장소에 파일을 업로드하는 방법에 대한 자세한 내용은 [Azure 포털에서 Blob 업로드, 다운로드 및 목록](../storage/blobs/storage-quickstart-blobs-portal.md)보기를 참조하십시오.

3. Azure 포털을 사용하여 zip 파일에 대한 공유 액세스 서명을 생성합니다. 자세한 내용은 [공유 액세스 서명(SAS)을 통해 대리자 액세스를](../storage/common/storage-sas-overview.md)참조하십시오.

4. Sql Database Edge 모듈 구성을 업데이트하여 DAC 패키지에 대한 공유 액세스 URI를 포함합니다. SQL 데이터베이스 에지 모듈을 업데이트하려면 다음 단계를 수행하십시오.

    1. Azure 포털에서 IoT Hub 배포로 이동합니다.

    2. 왼쪽 창에서 **IoT Edge**를 선택합니다.

    3. **IoT Edge** 페이지에서 SQL Database Edge 모듈이 배포된 IoT 에지를 찾아 선택합니다.

    4. **IoT Edge 장치** 장치 페이지에서 **모듈 설정을**선택합니다.

    5. 모듈 **설정** 페이지에서 SQL Database Edge 모듈에 대해 **구성을** 선택합니다.

    6. **IoT Edge 사용자 지정 모듈** 창에서 모듈 **트윈의 원하는 속성 설정을**선택합니다. 다음 예제와 같이 `SQLPackage` 옵션에 대한 URI를 포함하도록 원하는 속성을 업데이트합니다.

        > [!NOTE]
        > 다음 JSON의 SAS URI는 예에 불과합니다. URI를 배포의 실제 URI로 바꿉습니다.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. **저장**을 선택합니다.

    8. 모듈 설정 페이지에서 **다음**을 **선택합니다.**

    9. 모듈 설정 페이지에서 **다음을** 선택한 다음 을 제출한 다음 을 **선택합니다.** **Submit**

5. 모듈 업데이트 후 DAC 패키지 파일이 다운로드, 압축 해제 및 SQL Database Edge 인스턴스에 대해 배포됩니다.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>SQL 데이터베이스 에지에서 스트리밍 작업 사용

Azure SQL Database Edge에는 스트림 분석 런타임의 기본 구현이 있습니다. 이 구현을 통해 Azure Stream Analytics 에지 작업을 만들고 해당 작업을 SQL Database Edge 스트리밍 작업으로 배포할 수 있습니다. [분석 스트리밍 에지] 작업을 만들려면 다음 단계를 완료합니다.

1. 미리 보기 [URL을](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)사용하여 Azure 포털로 이동합니다. 이 미리 보기 URL을 사용하면 스트림 분석 에지 작업에 대한 SQL 데이터베이스 출력을 구성할 수 있습니다.

2. **IoT 에지** 작업에 새 Azure 스트림 분석을 만듭니다. Edge를 대상으로 하는 호스팅 환경을 **선택합니다.**

3. Azure 스트림 분석 작업에 대한 입력 및 출력을 정의합니다. 여기서 설정하게 될 각 SQL 출력은 데이터베이스의 단일 테이블에 연결됩니다. 데이터를 여러 테이블로 스트리밍해야 하는 경우 여러 SQL Database 출력을 만들어야 합니다. 다른 데이터베이스를 가리키도록 SQL 출력을 구성할 수 있습니다.

    **입력**합니다. EdgeHub를 에지 작업의 입력으로 선택하고 리소스 정보를 제공합니다.

    **출력**. SQL 데이터베이스를 출력으로 선택합니다. **SQL 데이터베이스 설정 을 수동으로 제공을 선택합니다.** 데이터베이스 및 테이블에 대한 구성 세부 정보를 제공합니다.

    |필드      | 설명 |
    |---------------|-------------|
    |출력 별칭 | 출력 별칭의 이름입니다.|
    |데이터베이스 | SQL 데이터베이스의 이름입니다. SQL Database Edge 인스턴스에 있는 데이터베이스의 유효한 이름이어야 합니다.|
    |서버 이름 | SQL 인스턴스의 이름(또는 IP 주소) 및 포트 번호 세부 정보입니다. SQL Database Edge 배포의 경우 서버 이름에 **tcp:.1433을** 사용할 수 있습니다.|
    |사용자 이름 | 이전에 지정한 데이터베이스에 대한 데이터 판독기 및 데이터 기록기 액세스 권한이 있는 SQL 로그인 계정입니다.|
    |암호 | 이전에 지정한 SQL 로그인 계정에 대한 암호입니다.|
    |테이블 | 스트리밍 작업에 대해 출력될 테이블의 이름입니다.|
    |분할 상속| 이전 쿼리 단계 또는 입력의 분할 체계를 상속할 수 있습니다. 이 옵션을 사용하도록 설정하면 디스크 기반 테이블에 작성하고 작업에 완전히 병렬 토폴로지가 있을 때 더 나은 처리량을 기대할 수 있습니다.|
    |Batch 크기| 모든 대량 삽입 트랜잭션과 함께 전송되는 최대 레코드 수입니다.|

    다음은 샘플 입력/출력 구성입니다.

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
    > Azure 스트림 분석에 대한 SQL 출력 어댑터에 대한 자세한 내용은 [Azure 스트림 분석 에서 Azure SQL Database에 대한 출력을](../stream-analytics/stream-analytics-sql-output-perf.md)참조하십시오.

4. 에지 작업에 대한 ASA 작업 쿼리를 정의합니다. 이 쿼리는 정의된 입력/출력 별칭을 쿼리의 입력 및 출력 이름으로 사용해야 합니다. 자세한 내용은 [스트림 분석 쿼리 언어 참조를](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)참조하십시오.

5. 에지 작업에 대한 저장소 계정 설정을 설정합니다. 저장소 계정은 에지 작업에 대한 게시 대상으로 사용됩니다.

6. **구성에서** **게시를**선택한 다음 **게시** 단추를 선택합니다. SQL Database Edge 모듈과 함께 사용할 수 있는 SAS URI를 저장합니다.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>SQL 데이터베이스 에지에 스트림 분석 에지 작업 배포

스트리밍 작업을 SQL Database Edge 모듈에 배포하려면 SQL Database Edge 모듈 구성을 업데이트하여 이전 단계에서 스트리밍 작업에 대한 SAS URI를 포함합니다. SQL 데이터베이스 에지 모듈을 업데이트하려면 다음을 수행하십시오.

1. Azure 포털에서 IoT Hub 배포로 이동합니다.

2. 왼쪽 창에서 **IoT Edge**를 선택합니다.

3. **IoT Edge** 페이지에서 SQL Database Edge 모듈이 배포된 IoT 에지를 찾아 선택합니다.

4. **IoT Edge 장치** 장치 페이지에서 **모듈 설정을**선택합니다.

5. 모듈 **설정** 페이지에서 SQL Database Edge 모듈에 대해 **구성을** 선택합니다.

6. **IoT Edge 사용자 지정 모듈** 창에서 모듈 **트윈의 원하는 속성 설정을**선택합니다. 다음 예제와 같이 `ASAJobInfo` 옵션에 대한 URI를 포함하도록 원하는 속성을 업데이트합니다.

    > [!NOTE]
    > 다음 JSON의 SAS URI는 예에 불과합니다. URI를 배포의 실제 URI로 바꿉습니다.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. **저장**을 선택합니다.

8. 모듈 설정 페이지에서 **다음**을 **선택합니다.**

9. 모듈 설정 페이지에서 **다음을** 선택한 다음 을 제출한 다음 을 **선택합니다.** **Submit**

10. 모듈 업데이트 후 스트림 분석 작업 파일이 다운로드, 압축 해제 및 SQL Database Edge 인스턴스에 대해 배포됩니다.

## <a name="next-steps"></a>다음 단계

- 가격 및 가용성 에대한 정보는 [Azure SQL 데이터베이스 에지를](https://azure.microsoft.com/services/sql-database-edge/)참조하십시오.
- 구독에 대해 Azure SQL 데이터베이스 에지 활성화를 요청합니다.
- 시작하려면 Azure [포털을 통해 SQL 데이터베이스 가장자리 배포를](deploy-portal.md)참조하십시오.
