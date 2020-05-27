---
title: Azure SQL Edge(미리 보기)에서 Azure Stream Analytics Edge 작업 사용
description: Azure SQL Edge(미리 보기)에서 Stream Analytics 작업을 사용하는 방법 알아보기
keywords: SQL Edge, stream analytics,
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b57cb8cae80381a6c2cd88358dd9284ba56c919
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594522"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>SQL Edge에서 Azure Stream Analytics 작업 사용

Azure SQL Edge(미리 보기)는 IoT 및 에지 배포에 최적화된 관계형 데이터베이스 엔진입니다. 업계 최고 수준의 성능, 보안 및 쿼리 처리 기능을 제공하는 Microsoft SQL Server 데이터베이스 엔진의 최신 버전을 기반으로 합니다. Azure SQL Edge는 Microsoft SQL Server의 업계 최고의 관계형 데이터베이스 관리 기능과 함께 실시간 분석 및 복잡한 이벤트 처리를 위한 기본 제공 스트리밍 기능을 제공합니다.

Azure SQL Edge에는 Stream Analytics 런타임의 네이티브 구현이 있습니다. 이 구현을 통해 Azure Stream Analytics Edge 작업을 만들고 SQL Edge 스트리밍 작업으로 배포할 수 있습니다. Azure Stream Analytics 작업은 SQL Edge 모듈의 `module twin's desired properties` 옵션을 통해 노출된 ASAJobInfo 매개 변수를 사용하여 SQL Edge에 배포할 수 있습니다.

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|필드 | Description |
|------|-------------|
| SqlPackage | SQL Database DAC 패키지를 포함하는 *.zip 파일의 Azure Blob Storage URI입니다.
| ASAJobInfo | ASA Edge 작업에 대한 Azure Blob 스토리지 URI입니다.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Azure Stream Analytics Edge 작업 만들기

1. Azure Portal로 이동합니다.

2. **IoT Edge의 Azure Stream Analytics** 작업을 새로 만듭니다. **Edge**를 대상으로 하는 호스팅 환경을 선택합니다.

3. Azure Stream Analytics 작업의 입력과 출력을 정의합니다. 여기에서 설정하는 각 SQL 출력이 데이터베이스의 단일 테이블에 연결됩니다. 데이터를 여러 테이블에 스트리밍해야 하는 경우 SQL Database 출력을 여러 개 만들어야 합니다. 서로 다른 데이터베이스를 가리키도록 SQL 출력을 구성할 수 있습니다.

    **입력**. Edge 작업의 입력으로 EdgeHub를 선택하고 리소스 정보를 제공합니다.

    **출력**. 출력으로 SQL Database를 선택합니다. **수동으로 SQL 데이터베이스 설정 제공**을 선택합니다. 데이터베이스와 테이블에 대한 구성 정보를 제공 합니다.

    |필드      | Description |
    |---------------|-------------|
    |출력 별칭 | 출력 별칭의 이름입니다.|
    |데이터베이스 | SQL 데이터베이스의 이름입니다. SQL Edge 인스턴스에 있는 데이터베이스의 유효한 이름이어야 합니다.|
    |서버 이름 | SQL 인스턴스의 이름 또는 IP 주소와 포트 번호 정보입니다. SQL Edge 배포의 경우 서버 이름으로 **tcp:.,1433**을 사용할 수 있습니다.|
    |사용자 이름 | 데이터 판독기와 데이터 기록기가 앞에서 지정한 데이터베이스에 액세스할 수 있게 하는 SQL 로그인 계정입니다.|
    |암호 | 앞에서 지정한 SQL 로그인 계정의 암호입니다.|
    |테이블 | 스트리밍 작업의 출력이 될 테이블의 이름입니다.|
    |파티션 구성표 상속| 이전 쿼리 단계 또는 입력의 파티션 구성표를 상속할 수 있도록 합니다. 이 옵션을 사용하면 디스크 기반 테이블에 쓰고 작업에 대한 완전 병렬 토폴로지가 있을 때 처리량 향상을 기대할 수 있습니다.|
    |Batch 크기| 모든 대량 삽입 트랜잭션에서 전송된 최대 레코드 수입니다.|

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
    > Azure Stream Analytics의 SQL 출력 어댑터에 대한 자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](../stream-analytics/stream-analytics-sql-output-perf.md)을 참조하세요.

4. Edge 작업에 대해 ASA 작업 쿼리를 정의합니다. 이 쿼리는 정의된 입력/출력 별칭을 쿼리의 입력 및 출력 이름으로 사용해야 합니다. 자세한 내용은 [Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)를 참조하세요.

5. Edge 작업에 대한 스토리지 계정 설정을 지정합니다. 스토리지 계정은 Edge 작업의 게시 대상으로 사용됩니다.

6. **구성**에서 **게시**를 선택하고 **게시** 단추를 선택합니다. SQL Edge 모듈에 사용할 SAS URI를 저장합니다.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>SQL Edge에 Azure Stream Analytics Edge 작업 배포

스트리밍 작업을 SQL Edge 모듈에 배포하려면 이전 단계의 스트리밍 작업에 대한 SAS URI를 포함하도록 SQL Edge 모듈 구성을 업데이트합니다. SQL Edge 모듈을 업데이트하려면

1. Microsoft Azure Portal에서 IoT 허브 배포로 이동합니다.

2. 왼쪽 창에서 **IoT Edge**를 선택합니다.

3. **IoT Edge** 페이지에서 SQL Edge 모듈이 배포된 IoT Edge를 찾아 선택합니다.

4. **IoT Edge 디바이스** 디바이스 페이지에서 **모듈 설정**을 선택합니다.

5. **모듈 설정** 페이지에서 SQL Edge 모듈에 대한 **구성**을 선택합니다.

6. **IoT Edge 사용자 지정 모듈** 창에서 **모듈 쌍의 원하는 속성 설정**을 선택합니다. 다음 예제와 같이 `ASAJobInfo` 옵션에 대한 URI를 포함하도록 원하는 속성을 업데이트합니다.

    > [!NOTE]
    > 다음 JavaScript Object Notation의 SAS URI는 예시일 뿐입니다. URI를 배포의 실제 URI로 바꿉니다.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. **저장**을 선택합니다.

8. **모듈 설정** 페이지에서 **다음**을 선택합니다.

9. **모듈 설정** 페이지에서 **다음**을 선택하고 **제출**을 선택합니다.

10. 모듈 업데이트 후에는 Stream Analytics 작업 파일을 다운로드하고 압축을 해제한 후 SQL Edge 인스턴스에 대해 배포합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure Portal을 통해 SQL Edge 배포](deploy-portal.md)

- [데이터 스트리밍](stream-data.md)

- [SQL Edge(미리 보기)에서 Open Neural Network Exchange를 통한 기계 학습 및 AI](onnx-overview.md)
