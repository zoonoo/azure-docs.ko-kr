---
title: 복사 작업 성능 문제 해결
description: Azure 데이터 팩터리에서 복사 작업 성능 문제를 해결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 6df1903e828c0c4cafa6589d4a85f4016bed893e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414135"
---
# <a name="troubleshoot-copy-activity-performance"></a>복사 작업 성능 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 복사 작업 성능 문제를 해결하는 방법을 설명합니다. 

복사 활동을 실행한 후 [복사 활동 모니터링](copy-activity-monitoring.md) 보기에서 실행 결과 및 성능 통계를 수집할 수 있습니다. 다음은 이에 대한 예입니다.

![복사 활동 실행 세부 정보 모니터링](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>성능 튜닝 팁

일부 시나리오에서는 데이터 팩터리에서 복사 작업을 실행할 때 위의 예제와 같이 상단에 **"성능 튜닝 팁"이** 표시됩니다. 팁은 이 특정 복사본 실행에 대해 ADF에서 식별한 병목 현상과 복사 처리량을 높이는 방법에 대한 제안을 알려줍니다. 다시 명령된 변경을 시도한 다음 복사본을 다시 실행합니다.

참고로 현재 성능 튜닝 팁은 다음과 같은 경우에 대한 제안을 제공합니다.

| 범주              | 성능 튜닝 팁                                      |
| --------------------- | ------------------------------------------------------------ |
| 데이터 저장소 별   | **Azure Synpase 분석(이전 SQL DW)에**데이터를 로드: PolyBase 또는 COPY 문을 사용하지 않는 경우 사용하는 것이 좋습니다. |
| &nbsp;                | **Azure SQL 데이터베이스에서/로**데이터를 복사 : DTU의 사용률이 높은 경우 상위 계층으로 업그레이드하는 것이 좋습니다. |
| &nbsp;                | **Azure Cosmos**DB에서/로 데이터를 복사 : RU가 높은 사용률을 받고 있는 경우 더 큰 RU로 업그레이드하는 것이 좋습니다. |
| &nbsp;                | **Amazon Redshift에서**데이터 수집 : 사용하지 않는 경우 언로드를 사용하는 것이 좋습니다. |
| 데이터 저장소 제한 | 복사 하는 동안 데이터 저장소에 의해 여러 읽기/쓰기 작업이 제한 되는 경우 확인 하 고 데이터 저장소에 대 한 허용 된 요청 비율을 증가 제안 하거나 동시 워크로드를 줄일 수 있습니다. |
| 통합 런타임  | 자체 호스팅 **통합 런타임(IR)을** 사용하고 IR이 실행할 수 있는 리소스가 있을 때까지 복사 활동이 큐에서 오래 대기하는 경우 IR을 확장/확장하는 것이 좋습니다. |
| &nbsp;                | 최적이 아닌 지역에 있는 **Azure 통합 런타임을** 사용하면 읽기/쓰기속도가 느려지는 경우 다른 리전에서 IR을 사용하도록 구성하는 것이 좋습니다. |
| 내결함성       | 내결함성을 구성하고 호환되지 않는 행을 건너뛰면 성능이 저하되는 경우 원본 및 싱크 데이터가 호환되도록 하는 것이 좋습니다. |
| 준비된 복사           | 준비된 복사본이 구성되어 있지만 소스 싱크 쌍에 유용하지 않은 경우 제거하는 것이 좋습니다. |
| 다시 시작                | 복사 활동이 마지막 실패 지점에서 다시 시작되었지만 원래 실행 후 DIU 설정을 변경하는 경우 새 DIU 설정이 적용되지 않습니다. |

## <a name="understand-copy-activity-execution-details"></a>복사 활동 실행 세부 정보 이해

복사 활동 모니터링 보기 의 맨 아래에 있는 실행 세부 정보 및 기간은 복사 작업이 진행되는 주요 단계(이 문서의 시작 부분의 예제 참조)를 설명하며, 복사 성능 문제 해결에 특히 유용합니다. 복사 실행의 병목 현상은 가장 긴 기간의 병목 현상입니다. 각 단계의 정의에 대한 다음 표를 참조하고 [Azure IR에서 복사 활동 문제를 해결하고](#troubleshoot-copy-activity-on-azure-ir) 이러한 정보를 사용하여 자체 호스팅 [IR에서 복사 활동을 해결하는](#troubleshoot-copy-activity-on-self-hosted-ir) 방법을 알아봅니다.

| 단계           | Description                                                  |
| --------------- | ------------------------------------------------------------ |
| 큐           | 복사 활동이 실제로 통합 런타임에서 시작될 때까지의 경과 시간입니다. |
| 사전 복사 스크립트 | IR에서 시작하여 복사 활동 사이의 경과 시간 및 복사 활동 사이의 경과 시간은 싱크 데이터 저장소에서 복사 전 스크립트를 실행합니다. 데이터베이스 싱크에 대한 사전 복사 스크립트를 구성할 때 적용(예: Azure SQL Database에 데이터를 쓰는 경우 새 데이터를 복사하기 전에 정리). |
| 전송        | 이전 단계의 끝과 원본에서 싱크로 모든 데이터를 전송하는 IR 사이의 경과 시간입니다. "전송"의 하위 단계는 병렬로 실행됩니다.<br><br>- **첫 번째 바이트시간:** 이전 단계의 끝과 IR이 원본 데이터 저장소에서 첫 번째 바이트를 수신하는 시간 사이에 경과된 시간입니다. 비 파일 기반 원본에 적용됩니다.<br>- **목록 소스:** 원본 파일 또는 데이터 파티션 열거에 소요되는 시간입니다. 후자는 오라클/SAP HANA/Teradata/Netezza/등과 같은 데이터베이스에서 데이터를 복사할 때와 같이 데이터베이스 원본에 대한 파티션 옵션을 구성할 때 적용됩니다.<br/>-**출처에서 읽기:** 원본 데이터 저장소에서 데이터를 검색하는 데 소요된 시간입니다.<br/>- **싱크할 글쓰기:** 데이터 저장소를 싱크하기 위해 데이터를 작성하는 데 소요된 시간입니다. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Azure IR에서 복사 작업 문제 해결

성능 [조정 단계를](copy-activity-performance.md#performance-tuning-steps) 수행하여 시나리오에 대한 성능 테스트를 계획하고 수행합니다. 

복사 활동 성능이 기대에 부합하지 않는 경우 Azure Integration 런타임에서 실행되는 단일 복사 활동 문제를 해결하는 경우 복사 모니터링 보기에 [성능 조정 팁이](#performance-tuning-tips) 표시되면 제안을 적용하고 다시 시도하십시오. 그렇지 않으면 [복사 활동 실행 세부 정보를 이해하고](#understand-copy-activity-execution-details)기간이 가장 **긴** 스테이지를 확인하고 아래 지침을 적용하여 복사 성능을 향상시킵니다.

- **"복사 전 스크립트"는 긴 기간을 경험했습니다:** 싱크 데이터베이스에서 실행되는 사전 복사 스크립트가 완료하는 데 시간이 오래 걸린다는 것을 의미합니다. 지정된 사전 복사 스크립트 논리를 조정하여 성능을 향상시킵니다. 스크립트 개선에 대한 추가 도움이 필요한 경우 데이터베이스 팀에 문의하십시오.

- **"전송 - 첫 번째 바이트까지의 시간"은 긴 작업 기간을 경험했습니다:** 원본 쿼리가 데이터를 반환하는 데 시간이 오래 걸린다는 것을 의미합니다. 쿼리 또는 서버를 확인하고 최적화합니다. 추가 도움이 필요한 경우 데이터 저장소 팀에 문의하십시오.

- **"전송 - 목록 소스"는 긴 작업 기간을 경험**: 그것은 소스 파일 또는 소스 데이터베이스 데이터 파티션을 열거 속도가 느리다는 것을 의미합니다.

  - 파일 기반 원본에서 데이터를 복사할 때 폴더 경로 또는 파일 이름(or)에서`wildcardFolderPath` `wildcardFileName` **와일드카드 필터를** 사용하거나 파일 마지막 **수정시간** 필터(or)를`modifiedDatetimeStart` `modifiedDatetimeEnd`사용하는 경우 이러한 필터를 사용하면 해당 필터가 지정된 폴더 아래에 있는 모든 파일을 클라이언트 측에 나열한 다음 필터를 적용합니다. 이러한 파일 열거는 특히 작은 파일 집합이 필터 규칙을 충족하는 경우 병목 현상이 될 수 있습니다.

    - 날짜 시간 [분할 된 파일 경로 또는 이름을 기반으로 파일을 복사할](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)수 있는지 확인합니다. 이러한 방법은 목록 소스 측에 부담을 주지 않습니다.

    - Amazon S3 및 Azure Blob에 대해**prefix**데이터 저장소의 기본 필터를 대신 사용할 수 있는지 확인합니다. 접두사 필터는 데이터 저장소 서버 측 필터이며 성능이 훨씬 향상됩니다.

    - 단일 대용량 데이터 집합을 여러 개의 작은 데이터 집합으로 분할하고 해당 복사 작업이 각 데이터 부분을 동시에 처리하도록 하는 것이 좋습니다. 조회/GetMetadata + ForEach + 복사를 통해 이 작업을 수행할 수 있습니다. 여러 [컨테이너에서 파일 복사를](solution-template-copy-files-multiple-containers.md) 참조하거나 일반적인 예로 [Amazon S3에서 ADLS Gen2](solution-template-migration-s3-azure.md) 솔루션 템플릿으로 데이터를 마이그레이션합니다.

  - ADF가 원본에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은 상태인지 확인합니다. 이 경우 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 원본 데이터 저장소 리전과 동일하거나 가까운 Azure IR을 사용합니다.

- **"전송 - 소스에서 읽기"경험이 긴 작업 기간**: 

  - 적용되는 경우 커넥터별 데이터 로딩 모범 사례를 채택합니다. 예를 들어 [Amazon Redshift에서](connector-amazon-redshift.md)데이터를 복사할 때 Redshift 언로드를 사용하도록 구성합니다.

  - ADF가 원본에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은지 확인합니다. 이 경우 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 소스 및 싱크 패턴을 확인합니다. 

    - 복사 패턴이 4개 이상의 데이터 통합 단위(DIUs)를 지원하는 경우 자세한 내용은 [이 섹션을](copy-activity-performance-features.md#data-integration-units) 참조하여 일반적으로 DI를 늘려 성능을 향상시켜 보십시오. 

    - 그렇지 않으면 단일 대용량 데이터 집합을 여러 개의 작은 데이터 집합으로 분할하고 해당 복사 작업이 각 데이터 부분을 동시에 실행하도록 하는 것이 좋습니다. 조회/GetMetadata + ForEach + 복사를 통해 이 작업을 수행할 수 있습니다. 여러 [컨테이너에서 파일 복사,](solution-template-copy-files-multiple-containers.md) [Amazon S3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md)또는 일반적인 예로 제어 테이블 솔루션 [템플릿을 사용하여 대량 복사를](solution-template-bulk-copy-with-control-table.md) 참조합니다.

  - 원본 데이터 저장소 리전과 동일하거나 가까운 Azure IR을 사용합니다.

- **"전송 - 싱크에 쓰기"경험 긴 작업 기간**:

  - 적용되는 경우 커넥터별 데이터 로딩 모범 사례를 채택합니다. 예를 들어 [Azure 시냅스 분석(이전의](connector-azure-sql-data-warehouse.md) SQL DW)에 데이터를 복사할 때 PolyBase 또는 COPY 문을 사용합니다. 

  - ADF가 싱크에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은지 확인합니다. 이 경우 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 소스 및 싱크 패턴을 확인합니다. 

    - 복사 패턴이 4개 이상의 데이터 통합 단위(DIUs)를 지원하는 경우 자세한 내용은 [이 섹션을](copy-activity-performance-features.md#data-integration-units) 참조하여 일반적으로 DI를 늘려 성능을 향상시켜 보십시오. 

    - 그렇지 않으면 [병렬 복사본을](copy-activity-performance-features.md)점진적으로 조정하면 병렬 복사본이 너무 많면 성능이 저하될 수 있습니다.

  - 싱크 데이터 저장소 리전과 동일하거나 가까운 Azure IR을 사용합니다.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>자체 호스팅 IR에서 복사 활동 문제 해결

성능 [조정 단계를](copy-activity-performance.md#performance-tuning-steps) 수행하여 시나리오에 대한 성능 테스트를 계획하고 수행합니다. 

복사 성능이 기대에 부합하지 않는 경우 Azure 통합 런타임에서 실행되는 단일 복사 작업 문제를 해결하는 경우 복사 모니터링 보기에 [성능 조정 팁이](#performance-tuning-tips) 표시되면 제안을 적용하고 다시 시도하십시오. 그렇지 않으면 [복사 활동 실행 세부 정보를 이해하고](#understand-copy-activity-execution-details)기간이 가장 **긴** 스테이지를 확인하고 아래 지침을 적용하여 복사 성능을 향상시킵니다.

- **"큐"는 긴 기간을 경험했습니다:** 자체 호스팅 IR이 실행할 리소스가 될 때까지 복사 활동이 큐에서 오래 대기됨을 의미합니다. IR 용량 및 사용량을 확인하고 워크로드에 따라 [확장 또는 축소합니다.](create-self-hosted-integration-runtime.md#high-availability-and-scalability)

- **"전송 - 첫 번째 바이트까지의 시간"은 긴 작업 기간을 경험했습니다:** 원본 쿼리가 데이터를 반환하는 데 시간이 오래 걸린다는 것을 의미합니다. 쿼리 또는 서버를 확인하고 최적화합니다. 추가 도움이 필요한 경우 데이터 저장소 팀에 문의하십시오.

- **"전송 - 목록 소스"는 긴 작업 기간을 경험**: 그것은 소스 파일 또는 소스 데이터베이스 데이터 파티션을 열거 속도가 느리다는 것을 의미합니다.

  - 자체 호스팅 IR 컴퓨터가 원본 데이터 저장소에 연결하는 대기 시간이 짧은지 확인합니다. 원본이 Azure에 있는 경우 [이 도구를](http://www.azurespeed.com/Azure/Latency) 사용하여 자체 호스팅 IR 컴퓨터에서 Azure 지역으로의 대기 시간을 확인할 수 있습니다.

  - 파일 기반 원본에서 데이터를 복사할 때 폴더 경로 또는 파일 이름(or)에서`wildcardFolderPath` `wildcardFileName` **와일드카드 필터를** 사용하거나 파일 마지막 **수정시간** 필터(or)를`modifiedDatetimeStart` `modifiedDatetimeEnd`사용하는 경우 이러한 필터를 사용하면 해당 필터가 지정된 폴더 아래에 있는 모든 파일을 클라이언트 측에 나열한 다음 필터를 적용합니다. 이러한 파일 열거는 특히 작은 파일 집합이 필터 규칙을 충족하는 경우 병목 현상이 될 수 있습니다.

    - 날짜 시간 [분할 된 파일 경로 또는 이름을 기반으로 파일을 복사할](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)수 있는지 확인합니다. 이러한 방법은 목록 소스 측에 부담을 주지 않습니다.

    - Amazon S3 및 Azure Blob에 대해**prefix**데이터 저장소의 기본 필터를 대신 사용할 수 있는지 확인합니다. 접두사 필터는 데이터 저장소 서버 측 필터이며 성능이 훨씬 향상됩니다.

    - 단일 대용량 데이터 집합을 여러 개의 작은 데이터 집합으로 분할하고 해당 복사 작업이 각 데이터 부분을 동시에 처리하도록 하는 것이 좋습니다. 조회/GetMetadata + ForEach + 복사를 통해 이 작업을 수행할 수 있습니다. 여러 [컨테이너에서 파일 복사를](solution-template-copy-files-multiple-containers.md) 참조하거나 일반적인 예로 [Amazon S3에서 ADLS Gen2](solution-template-migration-s3-azure.md) 솔루션 템플릿으로 데이터를 마이그레이션합니다.

  - ADF가 원본에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은 상태인지 확인합니다. 이 경우 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

- **"전송 - 소스에서 읽기"경험이 긴 작업 기간**: 

  - 자체 호스팅 IR 컴퓨터가 원본 데이터 저장소에 연결하는 대기 시간이 짧은지 확인합니다. 원본이 Azure에 있는 경우 [이 도구를](http://www.azurespeed.com/Azure/Latency) 사용하여 자체 호스팅 IR 컴퓨터에서 Azure 지역으로의 대기 시간을 확인할 수 있습니다.

  - 자체 호스팅 IR 컴퓨터에 데이터를 효율적으로 읽고 전송하기에 충분한 인바운드 대역폭이 있는지 확인합니다. 원본 데이터 저장소가 Azure에 있는 경우 [이 도구를](https://www.azurespeed.com/Azure/Download) 사용하여 다운로드 속도를 확인할 수 있습니다.

  - Azure 포털에서 자체 호스팅 IR의 CPU 및 메모리 사용 추세를 확인하십시오-> 데이터 팩터리 -> 개요 페이지입니다. CPU 사용량이 높거나 사용 가능한 메모리가 부족한 경우 [IR을 확장/축소하는](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 것이 좋습니다.

  - 적용되는 경우 커넥터별 데이터 로딩 모범 사례를 채택합니다. 다음은 그 예입니다.

    - [오라클,](connector-oracle.md#oracle-as-source) [네테자,](connector-netezza.md#netezza-as-source) [테라데이타,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP 테이블](connector-sap-table.md#sap-table-as-source)및 [SAP 오픈 허브에서](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)데이터를 복사할 때 데이터 파티션 옵션을 사용하여 데이터를 병렬로 복사할 수 있습니다.

    - [HDFS에서](connector-hdfs.md)데이터를 복사할 때 DistCp를 사용하도록 구성합니다.

    - [Amazon Redshift에서](connector-amazon-redshift.md)데이터를 복사할 때 Redshift 언로드를 사용하도록 구성합니다.

  - ADF가 원본에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은지 확인합니다. 이 경우 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 소스 및 싱크 패턴을 확인합니다. 

    - 파티션 옵션 지원 데이터 저장소에서 데이터를 복사하는 경우 [병렬 복사본을](copy-activity-performance-features.md)점진적으로 조정하는 것이 좋습니다.

    - 그렇지 않으면 단일 대용량 데이터 집합을 여러 개의 작은 데이터 집합으로 분할하고 해당 복사 작업이 각 데이터 부분을 동시에 실행하도록 하는 것이 좋습니다. 조회/GetMetadata + ForEach + 복사를 통해 이 작업을 수행할 수 있습니다. 여러 [컨테이너에서 파일 복사,](solution-template-copy-files-multiple-containers.md) [Amazon S3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md)또는 일반적인 예로 제어 테이블 솔루션 [템플릿을 사용하여 대량 복사를](solution-template-bulk-copy-with-control-table.md) 참조합니다.

- **"전송 - 싱크에 쓰기"경험 긴 작업 기간**:

  - 적용되는 경우 커넥터별 데이터 로딩 모범 사례를 채택합니다. 예를 들어 [Azure 시냅스 분석(이전의](connector-azure-sql-data-warehouse.md) SQL DW)에 데이터를 복사할 때 PolyBase 또는 COPY 문을 사용합니다. 

  - 자체 호스팅 IR 컴퓨터가 싱크 데이터 저장소에 연결하는 대기 시간이 짧은지 확인합니다. 싱크가 Azure에 있는 경우 [이 도구를](http://www.azurespeed.com/Azure/Latency) 사용하여 자체 호스팅 IR 컴퓨터에서 Azure 지역으로의 대기 시간을 확인할 수 있습니다.

  - 자체 호스팅 IR 컴퓨터에 데이터를 효율적으로 전송하고 쓸 수 있는 아웃바운드 대역폭이 충분한지 확인합니다. 싱크 데이터 저장소가 Azure에 있는 경우 [이 도구를](https://www.azurespeed.com/Azure/UploadLargeFile) 사용하여 업로드 속도를 확인할 수 있습니다.

  - Azure 포털에서 자체 호스팅 IR의 CPU 및 메모리 사용 추세가 데이터 팩터리 -> 개요 페이지를 > 있는지 확인합니다. CPU 사용량이 높거나 사용 가능한 메모리가 부족한 경우 [IR을 확장/축소하는](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 것이 좋습니다.

  - ADF가 싱크에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은지 확인합니다. 이 경우 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - [병렬 복사본을](copy-activity-performance-features.md)점진적으로 조정하는 것이 좋습니다.

## <a name="other-references"></a>기타 참조

다음은 지원되는 데이터 저장소에 대한 몇 가지 성능 모니터링 및 튜닝 참조입니다.

* Azure Blob 저장소: [Blob 저장소에 대한 확장성 및 성능 목표](../storage/blobs/scalability-targets.md) 및 [Blob 저장소에 대한 성능 및 확장성 검사 목록입니다.](../storage/blobs/storage-performance-checklist.md)
* Azure 테이블 저장소: [테이블 저장소에 대한 확장성 및 성능 목표](../storage/tables/scalability-targets.md) 및 테이블 [저장소에 대한 성능 및 확장성 검사 목록입니다.](../storage/tables/storage-performance-checklist.md)
* Azure SQL 데이터베이스: [성능을 모니터링하고](../sql-database/sql-database-single-database-monitor.md) DTU(데이터베이스 트랜잭션 단위) 백분율을 확인할 수 있습니다.
* Azure SQL 데이터 웨어하우스: 해당 기능은 DW(데이터 웨어하우스 단위)로 측정됩니다. [Azure SQL 데이터 웨어하우스(개요)에서 계산 능력 관리를](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)참조하십시오.
* Azure 코스모스 DB: [Azure 코스모스 DB의 성능 수준.](../cosmos-db/performance-levels.md)
* 온-프레미스 SQL Server: [성능을 모니터링하고 조정합니다.](https://msdn.microsoft.com/library/ms189081.aspx)
* 온-프레미스 파일 서버: [파일 서버에 대한 성능 튜닝.](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>다음 단계
다른 복사 활동 문서를 참조하십시오.

- [활동 개요 복사](copy-activity-overview.md)
- [복사 활동 성능 및 확장성 가이드](copy-activity-performance.md)
- [활동 성능 최적화 기능 복사](copy-activity-performance-features.md)
- [Azure 데이터 팩터리를 사용하여 데이터 레이크 또는 데이터 웨어하우스에서 Azure로 데이터를 마이그레이션합니다.](data-migration-guidance-overview.md)
- [Amazon S3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
