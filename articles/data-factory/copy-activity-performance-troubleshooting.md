---
title: 복사 작업 성능 문제 해결
description: Azure Data Factory에서 복사 작업 성능 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: dd0343fc2e25a50f9aa9a9fdc3ef5ebb9615bc56
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125772"
---
# <a name="troubleshoot-copy-activity-performance"></a>복사 작업 성능 문제 해결

이 문서에서는 Azure Data Factory에서 복사 작업 성능 문제를 해결 하는 방법을 설명 합니다. 

복사 작업을 실행 한 후 [복사 작업 모니터링](copy-activity-monitoring.md) 보기에서 실행 결과 및 성능 통계를 수집할 수 있습니다. 다음은 이에 대한 예입니다.

![복사 작업 실행 세부 정보 모니터링](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>성능 튜닝 팁

일부 시나리오에서는 Data Factory에서 복사 작업을 실행할 때 위의 예제와 같이 위쪽에 **"성능 튜닝 팁"** 이 표시 됩니다. 이 팁은 복사 처리량을 높이는 방법에 대 한 제안 사항을 비롯 하 여이 특정 복사 실행에 대해 ADF에서 식별 한 병목 상태를 알려 줍니다. 다시 변경을 수행한 후 복사를 다시 실행 하십시오.

참고로, 현재 성능 튜닝 팁은 다음과 같은 경우에 대 한 제안을 제공 합니다.

| Category              | 성능 튜닝 팁                                      |
| --------------------- | ------------------------------------------------------------ |
| 데이터 저장소 관련   | **Azure Synpase 분석 (이전의 SQL DW)** 에 데이터를 로드 하는 중입니다. 사용 되지 않는 경우 POLYBASE 또는 COPY 문을 사용 하는 것이 좋습니다. |
| &nbsp;                | **Azure SQL Database**간 데이터 복사: DTU가 높은 사용률을 사용 하는 경우 더 높은 계층으로 업그레이드 하는 것이 좋습니다. |
| &nbsp;                | **Azure Cosmos DB**에서/로 데이터 복사: 높은 사용률을 사용 중인 경우에는 더 큰 사용으로 업그레이드 하는 것이 좋습니다. |
| &nbsp;                | **Amazon Redshift**의 데이터 수집: 사용 되지 않는 경우 UNLOAD를 사용 하는 것이 좋습니다. |
| 데이터 저장소 제한 | 복사 하는 동안 데이터 저장소에서 읽기/쓰기 작업을 제한 하는 경우 데이터 저장소에 대해 허용 되는 요청 빈도를 확인 하 고 늘리는 것이 좋습니다. 그렇지 않으면 동시 작업을 줄일 수 있습니다. |
| Integration runtime  | Ir **(자체 호스팅 Integration Runtime)** 및 복사 작업을 사용 하는 경우 ir에 사용할 수 있는 리소스를 사용할 수 있을 때까지 큐에서 대기 하는 경우 ir 확장/축소를 제안 합니다. |
| &nbsp;                | 최적이 아닌 영역에 있는 **Azure Integration Runtime** 를 사용 하 여 읽기/쓰기 속도가 느려지는 경우 다른 지역에서 IR을 사용 하도록를 구성 하는 것이 좋습니다. |
| 내결함성       | 내결함성을 구성 하 고 호환 되지 않는 행을 건너뛰면 성능이 저하 되는 경우 원본 및 싱크 데이터가 호환 되는지 확인 하는 것이 좋습니다. |
| 준비된 복사           | 준비 된 복사본이 구성 되지만 원본 싱크 쌍에는 도움이 되지 않는 경우 제거를 제안 합니다. |
| 다시 시작                | 마지막 오류 지점에서 복사 작업을 다시 시작할 때 원래 실행 후 DIU 설정을 변경 하는 경우에는 새 DIU 설정이 적용 되지 않습니다. |

## <a name="understand-copy-activity-execution-details"></a>복사 활동 실행 세부 정보 이해

복사 작업 모니터링 보기의 아래쪽에 있는 실행 세부 정보 및 기간은 복사 작업의 핵심 단계에 대해 설명 합니다 .이는 복사 성능 문제를 해결 하는 데 특히 유용 합니다. 복사 실행에 대 한 병목 상태는 기간이 가장 긴 것입니다. 각 단계의 정의에서 다음 표를 참조 하 고, [Azure IR의 복사 작업 문제를 해결](#troubleshoot-copy-activity-on-azure-ir) 하 고 [자체 호스팅 IR에서](#troubleshoot-copy-activity-on-self-hosted-ir) 이러한 정보를 사용 하 여 복사 작업의 문제를 해결 하는 방법을 알아봅니다.

| 단계           | Description                                                  |
| --------------- | ------------------------------------------------------------ |
| 큐           | 통합 런타임에 복사 작업이 실제로 시작 될 때까지 경과 된 시간입니다. |
| 사전 복사 스크립트 | IR에서 시작 하는 복사 작업과 복사 작업에서 싱크 데이터 저장소의 사전 복사 스크립트 실행을 완료 하는 데 걸리는 시간입니다. 데이터베이스 싱크에 대해 사전 복사 스크립트를 구성할 때 적용 됩니다. 예를 들어 Azure SQL Database에 데이터를 쓸 때 새 데이터를 복사 하기 전에 정리 작업을 수행할 수 있습니다. |
| 전송        | 이전 단계와 IR 사이에 경과 된 시간으로, 원본에서 싱크로 모든 데이터를 전송 합니다. "Transfer" 아래의 하위 단계는 병렬로 실행 됩니다.<br><br>**첫 번째 바이트 까지의 시간 - :** 이전 단계의 끝과 IR이 원본 데이터 저장소에서 첫 번째 바이트를 받는 시간 사이에 경과 된 시간입니다. 파일 기반이 아닌 소스에 적용 됩니다.<br>- **목록 원본:** 원본 파일 또는 데이터 파티션을 열거 하는 데 소요 된 시간입니다. 후자는 Oracle/SAP HANA/Teradata/Netezza/등의 데이터베이스에서 데이터를 복사 하는 경우와 같이 데이터베이스 원본에 대 한 파티션 옵션을 구성할 때 적용 됩니다.<br/>**원본에서 읽기 -:** 원본 데이터 저장소에서 데이터를 검색 하는 데 소요 된 시간입니다.<br/>**싱크에 쓸 - :** 싱크 데이터 저장소에 데이터를 쓰는 데 소요 된 시간입니다. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Azure IR의 복사 작업 문제 해결

[성능 튜닝 단계](copy-activity-performance.md#performance-tuning-steps) 를 따라 시나리오에 대 한 성능 테스트를 계획 하 고 수행 합니다. 

복사 작업 성능이 예상과 일치 하지 않는 경우 Azure Integration Runtime에서 실행 되는 단일 복사 작업의 문제를 해결 하려면 [모니터링] 보기에 표시 되는 [성능 조정 팁](#performance-tuning-tips) 이 표시 되 면 제안을 적용 하 고 다시 시도 하세요. 그렇지 않으면 [복사 작업 실행 세부 정보를 이해](#understand-copy-activity-execution-details)하 고, 기간이 **가장 긴** 단계를 확인 하 고, 복사 성능을 향상 시키기 위해 아래 지침을 적용 합니다.

- **"사전 복사 스크립트"에 오랜 시간이** 소요 되었습니다. 즉, 싱크 데이터베이스에서 실행 되는 사전 복사 스크립트를 완료 하는 데 시간이 오래 걸립니다. 지정 된 복사 전 스크립트 논리를 조정 하 여 성능을 향상 시킵니다. 스크립트를 개선 하는 데 도움이 필요한 경우 데이터베이스 팀에 문의 하십시오.

- **"첫 번째 바이트로의 전송 시간"은 긴 작업 시간**입니다 .이는 원본 쿼리가 데이터를 반환 하는 데 시간이 오래 걸리는 것을 의미 합니다. 쿼리 또는 서버를 확인 하 고 최적화 합니다. 추가 도움이 필요 하면 데이터 저장소 팀에 문의 하세요.

- **"전송 목록 원본"의 장기 작동 기간**: 원본 파일을 열거 하거나 원본 데이터베이스 데이터 파티션을 더 느리게 열거 하는 것을 의미 합니다.

  - 파일 기반 원본에서 데이터를 복사할 때 폴더 경로 또는 파일 이름에 **와일드 카드 필터** 를 사용 하거나 (`wildcardFolderPath` 또는 `wildcardFileName`) **파일을 마지막으로 수정한 시간 필터** (`modifiedDatetimeStart` 또는`modifiedDatetimeEnd`)를 사용 하는 경우 해당 필터는 지정 된 폴더 아래의 모든 파일을 클라이언트 쪽으로 나열 하는 복사 작업을 생성 한 다음 필터를 적용 합니다. 이러한 파일 열거는 특히 작은 파일 집합만 필터 규칙을 충족 하는 경우 병목 상태가 될 수 있습니다.

    - [Datetime 분할 파일 경로 또는 이름에 따라 파일을 복사할](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)수 있는지 여부를 확인 합니다. 이러한 방식으로 원본 측을 나열 하는 것은 부담 하지 않습니다.

    - 대신 데이터 저장소의 기본 필터를 사용할 수 있는지 확인 합니다. 특히 Amazon S3 및 Azure Blob의 경우 "**prefix**"를 사용 합니다. 접두사 필터는 데이터 저장소 서버 쪽 필터 이며 성능이 훨씬 향상 됩니다.

    - 단일 대량 데이터 집합을 여러 개의 작은 데이터 집합으로 분할 하는 것이 좋습니다. 이러한 복사 작업은 있는지 데이터의 각 부분에서 동시에 실행 될 수 있습니다. Lookup/GetMetadata + ForEach + Copy를 사용 하 여이 작업을 수행할 수 있습니다. 일반적인 예제와 같이 [여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md) 또는 Amazon s 3 [에서 ADLS Gen2 솔루션 템플릿으로 데이터 마이그레이션](solution-template-migration-s3-azure.md) 을 참조 하세요.

  - ADF가 원본에서의 제한 오류를 보고 하는지 또는 데이터 저장소가 높은 사용률 상태에 있는지를 보고 하는지 확인 합니다. 그렇다면 데이터 저장소에서 작업을 줄이거나 데이터 저장소 관리자에 게 문의 하 여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 원본 데이터 저장소 영역과 같거나 가까운 위치에 Azure IR를 사용 합니다.

- **"원본에서 전송-읽기" 숙련 된 긴 작업 시간**: 

  - 이 적용 되는 경우 커넥터 관련 데이터 로드 모범 사례를 채택 합니다. 예를 들어 [Amazon Redshift](connector-amazon-redshift.md)에서 데이터를 복사 하는 경우 Redshift UNLOAD를 사용 하도록를 구성 합니다.

  - ADF가 원본에서의 제한 오류를 보고 하는지 또는 데이터 저장소의 사용률이 높은 지를 보고 하는지 확인 합니다. 그렇다면 데이터 저장소에서 작업을 줄이거나 데이터 저장소 관리자에 게 문의 하 여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 원본 및 싱크 패턴을 확인 합니다. 

    - 복사 패턴이 4 개 보다 큰 DIUs (데이터 통합 단위)를 지 원하는 경우-일반적으로 자세한 내용은 [이 섹션](copy-activity-performance.md#data-integration-units) 을 참조 하 여 성능을 향상 시킬 수 있습니다. 

    - 그렇지 않은 경우에는 단일 크기의 데이터 집합을 여러 개의 작은 데이터 집합으로 분할 하 고 이러한 복사 작업이 있는지 데이터의 각 부분에서 동시에 실행 되도록 하는 것이 좋습니다. Lookup/GetMetadata + ForEach + Copy를 사용 하 여이 작업을 수행할 수 있습니다. 일반적인 예로는 [여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md), Amazon s 3 [에서 ADLS Gen2 데이터 마이그레이션](solution-template-migration-s3-azure.md)또는 [컨트롤 테이블 솔루션 템플릿을 사용 하 여 대량 복사](solution-template-bulk-copy-with-control-table.md) 를 참조 하세요.

  - 원본 데이터 저장소 영역과 같거나 가까운 위치에 Azure IR를 사용 합니다.

- **"싱크에 대 한 전송-진행 중" 경험이 긴 작업 시간**:

  - 이 적용 되는 경우 커넥터 관련 데이터 로드 모범 사례를 채택 합니다. 예를 들어 데이터를 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (이전의 SQL DW)로 복사 하는 경우 POLYBASE 또는 COPY 문을 사용 합니다. 

  - ADF에서 싱크에 대 한 제한 오류를 보고 하는지 또는 데이터 저장소의 사용률이 높은 지를 확인 합니다. 그렇다면 데이터 저장소에서 작업을 줄이거나 데이터 저장소 관리자에 게 문의 하 여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 원본 및 싱크 패턴을 확인 합니다. 

    - 복사 패턴이 4 개 보다 큰 DIUs (데이터 통합 단위)를 지 원하는 경우-일반적으로 자세한 내용은 [이 섹션](copy-activity-performance.md#data-integration-units) 을 참조 하 여 성능을 향상 시킬 수 있습니다. 

    - 그렇지 않으면 [병렬 복사](copy-activity-performance-features.md)를 점차적으로 조정 합니다. 너무 많은 병렬 복사는 성능이 저하 될 수 있습니다.

  - 싱크 데이터 저장소 영역과 같거나 가까운 위치에 Azure IR를 사용 합니다.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>자체 호스팅 IR에서 복사 작업 문제 해결

[성능 튜닝 단계](copy-activity-performance.md#performance-tuning-steps) 를 따라 시나리오에 대 한 성능 테스트를 계획 하 고 수행 합니다. 

복사 성능이 예상과 일치 하지 않는 경우 Azure Integration Runtime에서 실행 되는 단일 복사 작업의 문제를 해결 하려면 [모니터링] 보기에 표시 된 [성능 튜닝 팁](#performance-tuning-tips) 이 표시 되 면 제안을 적용 하 고 다시 시도 하세요. 그렇지 않으면 [복사 작업 실행 세부 정보를 이해](#understand-copy-activity-execution-details)하 고, 기간이 **가장 긴** 단계를 확인 하 고, 복사 성능을 향상 시키기 위해 아래 지침을 적용 합니다.

- **"큐"가 장기간 발생 했습니다.** 즉, 복사 작업은 자체 호스팅 IR에서 실행할 리소스를 가질 때까지 큐에서 대기 하는 동안 대기 합니다. IR 용량과 사용량을 확인 하 고 워크 로드에 따라 [확장 또는 축소](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 합니다.

- **"첫 번째 바이트로의 전송 시간"은 긴 작업 시간**입니다 .이는 원본 쿼리가 데이터를 반환 하는 데 시간이 오래 걸리는 것을 의미 합니다. 쿼리 또는 서버를 확인 하 고 최적화 합니다. 추가 도움이 필요 하면 데이터 저장소 팀에 문의 하세요.

- **"전송 목록 원본"의 장기 작동 기간**: 원본 파일을 열거 하거나 원본 데이터베이스 데이터 파티션을 더 느리게 열거 하는 것을 의미 합니다.

  - 자체 호스팅 IR 컴퓨터가 원본 데이터 저장소에 연결 하는 데 짧은 대기 시간이 있는지 확인 합니다. 소스가 Azure에 있는 경우 [이 도구](http://www.azurespeed.com/Azure/Latency) 를 사용 하 여 자체 호스팅 IR 컴퓨터에서 azure 지역으로의 대기 시간을 확인할 수 있습니다.

  - 파일 기반 원본에서 데이터를 복사할 때 폴더 경로 또는 파일 이름에 **와일드 카드 필터** 를 사용 하거나 (`wildcardFolderPath` 또는 `wildcardFileName`) **파일을 마지막으로 수정한 시간 필터** (`modifiedDatetimeStart` 또는`modifiedDatetimeEnd`)를 사용 하는 경우 해당 필터는 지정 된 폴더 아래의 모든 파일을 클라이언트 쪽으로 나열 하는 복사 작업을 생성 한 다음 필터를 적용 합니다. 이러한 파일 열거는 특히 작은 파일 집합만 필터 규칙을 충족 하는 경우 병목 상태가 될 수 있습니다.

    - [Datetime 분할 파일 경로 또는 이름에 따라 파일을 복사할](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)수 있는지 여부를 확인 합니다. 이러한 방식으로 원본 측을 나열 하는 것은 부담 하지 않습니다.

    - 대신 데이터 저장소의 기본 필터를 사용할 수 있는지 확인 합니다. 특히 Amazon S3 및 Azure Blob의 경우 "**prefix**"를 사용 합니다. 접두사 필터는 데이터 저장소 서버 쪽 필터 이며 성능이 훨씬 향상 됩니다.

    - 단일 대량 데이터 집합을 여러 개의 작은 데이터 집합으로 분할 하는 것이 좋습니다. 이러한 복사 작업은 있는지 데이터의 각 부분에서 동시에 실행 될 수 있습니다. Lookup/GetMetadata + ForEach + Copy를 사용 하 여이 작업을 수행할 수 있습니다. 일반적인 예제와 같이 [여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md) 또는 Amazon s 3 [에서 ADLS Gen2 솔루션 템플릿으로 데이터 마이그레이션](solution-template-migration-s3-azure.md) 을 참조 하세요.

  - ADF가 원본에서의 제한 오류를 보고 하는지 또는 데이터 저장소가 높은 사용률 상태에 있는지를 보고 하는지 확인 합니다. 그렇다면 데이터 저장소에서 작업을 줄이거나 데이터 저장소 관리자에 게 문의 하 여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

- **"원본에서 전송-읽기" 숙련 된 긴 작업 시간**: 

  - 자체 호스팅 IR 컴퓨터가 원본 데이터 저장소에 연결 하는 데 짧은 대기 시간이 있는지 확인 합니다. 소스가 Azure에 있는 경우 [이 도구](http://www.azurespeed.com/Azure/Latency) 를 사용 하 여 자체 호스팅 IR 컴퓨터에서 azure 지역으로의 대기 시간을 확인할 수 있습니다.

  - 자체 호스팅 IR 컴퓨터에 데이터를 효율적으로 읽고 전송 하기 위한 충분 한 인바운드 대역폭이 있는지 확인 합니다. 원본 데이터 저장소가 Azure에 있는 경우 [이 도구](https://www.azurespeed.com/Azure/Download) 를 사용 하 여 다운로드 속도를 확인할 수 있습니다.

  - Azure Portal > 데이터 팩터리-> 개요 페이지에서 자체 호스팅 IR의 CPU 및 메모리 사용량 추세를 확인 합니다. CPU 사용량이 크거나 사용 가능한 메모리가 부족 한 경우 [IR을 확장/축소](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 하는 것이 좋습니다.

  - 이 적용 되는 경우 커넥터 관련 데이터 로드 모범 사례를 채택 합니다. 다음은 그 예입니다.

    - [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [sap 테이블](connector-sap-table.md#sap-table-as-source)및 [sap Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)에서 데이터를 복사 하는 경우 데이터를 병렬로 복사 하도록 데이터 파티션 옵션을 사용 하도록 설정 합니다.

    - [HDFS](connector-hdfs.md)에서 데이터를 복사 하는 경우 distcp를 사용 하도록 구성 합니다.

    - [Amazon Redshift](connector-amazon-redshift.md)에서 데이터를 복사 하는 경우 Redshift UNLOAD를 사용 하도록를 구성 합니다.

  - ADF가 원본에서의 제한 오류를 보고 하는지 또는 데이터 저장소의 사용률이 높은 지를 확인 합니다. 그렇다면 데이터 저장소에서 작업을 줄이거나 데이터 저장소 관리자에 게 문의 하 여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 원본 및 싱크 패턴을 확인 합니다. 

    - 파티션 옵션 사용 데이터 저장소에서 데이터를 복사 하는 경우 [병렬 복사](copy-activity-performance-features.md)를 점차적으로 조정 하는 것이 좋습니다. 병렬 복사본이 너무 많으면 성능이 저하 될 수 있습니다.

    - 그렇지 않은 경우에는 단일 크기의 데이터 집합을 여러 개의 작은 데이터 집합으로 분할 하 고 이러한 복사 작업이 있는지 데이터의 각 부분에서 동시에 실행 되도록 하는 것이 좋습니다. Lookup/GetMetadata + ForEach + Copy를 사용 하 여이 작업을 수행할 수 있습니다. 일반적인 예로는 [여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md), Amazon s 3 [에서 ADLS Gen2 데이터 마이그레이션](solution-template-migration-s3-azure.md)또는 [컨트롤 테이블 솔루션 템플릿을 사용 하 여 대량 복사](solution-template-bulk-copy-with-control-table.md) 를 참조 하세요.

- **"싱크에 대 한 전송-진행 중" 경험이 긴 작업 시간**:

  - 이 적용 되는 경우 커넥터 관련 데이터 로드 모범 사례를 채택 합니다. 예를 들어 데이터를 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (이전의 SQL DW)로 복사 하는 경우 POLYBASE 또는 COPY 문을 사용 합니다. 

  - 자체 호스팅 IR 컴퓨터에서 싱크 데이터 저장소에 연결 하는 데 대기 시간이 짧은 지 확인 합니다. 싱크가 Azure에 있는 경우 [이 도구](http://www.azurespeed.com/Azure/Latency) 를 사용 하 여 자체 호스팅 IR 컴퓨터에서 azure 지역으로의 대기 시간을 확인할 수 있습니다.

  - 자체 호스팅 IR 컴퓨터에 데이터를 효율적으로 전송 하 고 쓸 수 있는 아웃 바운드 대역폭이 충분 한지 확인 합니다. 싱크 데이터 저장소가 Azure에 있는 경우 [이 도구](https://www.azurespeed.com/Azure/UploadLargeFile) 를 사용 하 여 업로드 속도를 확인할 수 있습니다.

  - Azure Portal에서 자체 호스팅 IR의 CPU 및 메모리 사용량 추세를 확인 하 여 데이터 팩터리 > 개요 페이지 > 합니다. CPU 사용량이 크거나 사용 가능한 메모리가 부족 한 경우 [IR을 확장/축소](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 하는 것이 좋습니다.

  - ADF에서 싱크에 대 한 제한 오류를 보고 하는지 또는 데이터 저장소의 사용률이 높은 지를 확인 합니다. 그렇다면 데이터 저장소에서 작업을 줄이거나 데이터 저장소 관리자에 게 문의 하 여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - [병렬 복사](copy-activity-performance-features.md)를 점차적으로 조정 하는 것이 좋습니다. 병렬 복사본이 너무 많으면 성능이 저하 될 수 있습니다.

## <a name="other-references"></a>기타 참조

다음은 지원되는 데이터 저장소에 대한 몇 가지 성능 모니터링 및 튜닝 참조입니다.

* Azure Blob storage: blob 저장소에 [대 한 확장성 및 성능 목표](../storage/blobs/scalability-targets.md) 와 [blob 저장소에 대 한 성능 및 확장성 검사 목록](../storage/blobs/storage-performance-checklist.md)입니다.
* Azure 테이블 저장소: 테이블 저장소에 [대 한 확장성 및 성능 목표](../storage/tables/scalability-targets.md) 와 [테이블 저장소에 대 한 성능 및 확장성 검사 목록](../storage/tables/storage-performance-checklist.md)입니다.
* Azure SQL Database: [성능을 모니터링](../sql-database/sql-database-single-database-monitor.md) 하 고 DTU (데이터베이스 트랜잭션 단위) 비율을 확인할 수 있습니다.
* Azure SQL Data Warehouse: 해당 기능은 DWUs (데이터 웨어하우스 단위)로 측정 됩니다. [Azure SQL Data Warehouse에서 계산 능력 관리 (개요)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)를 참조 하세요.
* Azure Cosmos DB: [Azure Cosmos DB의 성능 수준](../cosmos-db/performance-levels.md)입니다.
* 온-프레미스 SQL Server: [성능을 모니터링 하 고 조정](https://msdn.microsoft.com/library/ms189081.aspx)합니다.
* 온-프레미스 파일 서버: [파일 서버에 대 한 성능 조정](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조 하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능 및 확장성 가이드](copy-activity-performance.md)
- [복사 작업 성능 최적화 기능](copy-activity-performance-features.md)
- [Azure Data Factory를 사용 하 여 data lake 또는 데이터 웨어하우스의 데이터를 Azure로 마이그레이션](data-migration-guidance-overview.md)
- [Amazon s 3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
