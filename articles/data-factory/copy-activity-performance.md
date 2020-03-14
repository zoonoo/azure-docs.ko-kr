---
title: 복사 작업 성능 및 확장성 가이드
description: 복사 작업을 사용할 때 Azure Data Factory에서 데이터 이동의 성능에 영향을 주는 주요 요소에 대해 알아봅니다.
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
ms.openlocfilehash: 231b0d77dc441e70dc0ec8de313291bb6b4f9292
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261399"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>복사 작업 성능 및 확장성 가이드

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-copy-activity-performance.md)
> * [현재 버전](copy-activity-performance.md)

빅 데이터 분석을 위해 data lake 또는 EDW (엔터프라이즈 데이터 웨어하우스)에서 대규모 데이터 마이그레이션을 수행할지 또는 여러 원본에서 Azure로 대규모로 데이터를 수집 하려는 경우에는 최적의 성능을 구현 하는 것이 중요 합니다. 향상.  Azure Data Factory은 데이터를 대규모로 수집할 수 있는 성능, 복원 력 및 비용 효율적인 메커니즘을 제공 하 여 데이터 엔지니어가 뛰어난 성능 및 확장성이 뛰어난 데이터 수집 파이프라인을 구축 하는 데 적합 합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- 데이터 마이그레이션 및 데이터 수집 시나리오에서 ADF 복사 작업을 사용 하 여 어떤 수준의 성능 및 확장성을 달성할 수 있나요?

- ADF 복사 작업의 성능을 조정 하기 위해 수행 해야 하는 단계는 무엇 인가요?
- 단일 복사 작업 실행의 성능을 최적화 하기 위해 활용할 수 있는 ADF 성능 최적화 노브 무엇 인가요?
- ADF 외부에서 복사 성능을 최적화할 때 고려할 다른 요소는 무엇 인가요?

> [!NOTE]
> 일반적으로 복사 작업에 익숙하지 않은 경우이 문서를 읽기 전에 [복사 작업 개요](copy-activity-overview.md) 를 참조 하세요.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF를 사용 하 여 달성 가능한 복사 성능 및 확장성

ADF는 서로 다른 수준에서 병렬 처리를 허용 하는 서버 리스 아키텍처를 제공 합니다 .이 아키텍처를 통해 개발자는 네트워크 대역폭과 저장소 IOPS 및 대역폭을 완벽 하 게 활용 하 여 환경에 대 한 데이터 이동 처리량을 최대화할 수 있습니다.  즉, 원본 및 대상 사이에서 원본 데이터 저장소, 대상 데이터 저장소 및 네트워크 대역폭에서 제공 되는 최소 처리량을 측정 하 여 달성할 수 있는 처리량을 예상할 수 있습니다.  아래 표에서는 사용자 환경에 대 한 데이터 크기 및 대역폭 제한에 따라 복사 기간을 계산 합니다. 

| 데이터 크기/ <br/> bandwidth | 50Mbps    | 100Mbps  | 500Mbps  | 1Gbps   | 5Gbps   | 10Gbps  | 50Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1GB**                    | 2.7 분    | 1.4 분   | 0.3 분   | 0.1 분  | 0.03 분 | 0.01 분 | 0.0 분   |
| **10gb**                   | 27.3 분   | 13.7 분  | 2.7 분   | 1.3 분  | 0.3 분  | 0.1 분  | 0.03 분  |
| **100 GB**                  | 4.6 시간    | 2.3 시간   | 0.5 시간   | 0.2 시간  | 0.05 시간 | 0.02 시간 | 0.0 시간   |
| **1 TB**                    | 46.6 시간   | 23.3 시간  | 4.7 시간   | 2.3 시간  | 0.5 시간  | 0.2 시간  | 0.05 시간  |
| **10TB**                   | 19.4 일  | 9.7 일  | 1.9 일  | 0.9 일 | 0.2 일 | 0.1 일 | 0.02 일 |
| **100 TB**                  | 194.2 일 | 97.1 일 | 19.4 일 | 9.7 일 | 1.9 일 | 1일    | 0.2 일  |
| **1 PB**                    | 64.7 mo    | 32.4 mo   | 6.5 mo    | 3.2 mo   | 0.6 mo   | 0.3 mo   | 0.06 mo   |
| **10gb**                   | 647.3 mo   | 323.6 mo  | 64.7 mo   | 31.6 mo  | 6.5 mo   | 3.2 mo   | 0.6 mo    |

ADF 복사본은 다양 한 수준에서 확장 가능 합니다.

![ADF 복사본 크기 조정 방법](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 제어 흐름은 [For each 루프](control-flow-for-each-activity.md)를 사용 하는 등의 여러 복사 작업을 병렬로 시작할 수 있습니다.
- 단일 복사 작업은 확장 가능한 계산 리소스를 활용할 수 있습니다. Azure Integration Runtime를 사용 하는 경우 서버를 사용 하지 않는 방식으로 각 복사 작업에 대해 [최대 256 DIUs를](#data-integration-units) 지정할 수 있습니다. 자체 호스트 된 Integration Runtime를 사용 하는 경우 수동으로 컴퓨터를 확장 하거나 여러 컴퓨터로 확장할 수 있습니다 ([최대 4 개 노드](create-self-hosted-integration-runtime.md#high-availability-and-scalability)). 단일 복사 작업은 모든 노드에 걸쳐 해당 파일 집합을 분할 합니다.
- 단일 복사 작업은 여러 스레드를 [병렬로](#parallel-copy)사용 하 여 데이터 저장소에서 읽고 씁니다.

## <a name="performance-tuning-steps"></a>성능 튜닝 단계

이러한 단계를 수행 하 여 복사 작업으로 Azure Data Factory 서비스의 성능을 조정 합니다.

1. **테스트 데이터 집합을 선택 하 고 기준선을 설정 합니다.** 개발 단계에서 대표 데이터 샘플에 대 한 복사 작업을 사용 하 여 파이프라인을 테스트 합니다. 선택한 데이터 집합은 일반적인 데이터 패턴 (폴더 구조, 파일 패턴, 데이터 스키마 등)을 표시 하 고 복사 성능을 평가할 만큼 충분히 큽니다. 예를 들어 복사 작업을 완료 하는 데 10 분 이상 소요 됩니다. [복사 작업 모니터링](copy-activity-monitoring.md)을 수행 하 여 실행 세부 정보 및 성능 특징을 수집 합니다.

2. **단일 복사 작업의 성능을 최대화 하는 방법**:

   먼저 단일 복사 작업을 사용 하 여 성능을 최대화 하는 것이 좋습니다.

   - **복사 작업이 Azure Integration Runtime에서 실행 되는 경우:** [Diu (데이터 통합 단위](#data-integration-units) ) 및 [병렬 복사](#parallel-copy) 설정의 기본값을 사용 하 여 시작 합니다. 

   - **복사 작업이 자체 호스트 된 Integration Runtime에서 실행 되는 경우:** 데이터 저장소를 호스트 하는 서버와 별개의 전용 컴퓨터를 사용 하 여 Integration Runtime을 호스트 하는 것이 좋습니다. [병렬 복사](#parallel-copy) 설정의 기본값을 사용 하 여 시작 하 고 자체 호스팅 IR에 단일 노드를 사용 합니다.  

   성능 테스트 실행을 수행 하 고, DIUs 및 병렬 복사와 같이 사용 되는 실제 값 뿐만 아니라 성능을 확인 합니다. 실행 결과 및 사용 되는 성능 설정을 수집 하는 방법에 대 한 [복사 작업 모니터링](copy-activity-monitoring.md) 및 병목 상태를 식별 하 고 해결 하기 위해 [복사 작업 성능 문제를 해결](copy-activity-performance-troubleshooting.md) 하는 방법을 알아봅니다. 

   문제 해결 및 튜닝 지침에 따라 추가 성능 테스트 실행을 수행 하려면 반복 합니다. 단일 복사 작업 실행이 더 나은 처리량을 달성할 수 없는 경우 3 단계를 참조 하는 여러 복사본을 동시에 실행 하 여 집계 처리량을 최대화 합니다.


3. **여러 복사본을 동시에 실행 하 여 집계 처리량을 최대화 하는 방법:**

   이제 단일 복사 작업의 성능을 최대화 했으므로 (네트워크, 원본 데이터 저장소 및 대상 데이터 저장소) 환경의 처리량 상한을 아직 획득 하지 않은 경우 [For each 루프](control-flow-for-each-activity.md)와 같은 ADF 제어 흐름 구문을 사용 하 여 여러 개의 복사 작업을 병렬로 실행할 수 있습니다. 일반적인 예로는 [여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md), Amazon s 3 [에서 ADLS Gen2 데이터 마이그레이션](solution-template-migration-s3-azure.md)또는 [컨트롤 테이블 솔루션 템플릿을 사용 하 여 대량 복사](solution-template-bulk-copy-with-control-table.md) 를 참조 하세요.

5. **전체 데이터 집합에 대 한 구성을 확장 합니다.** 실행 결과 및 성능에 만족 하는 경우 정의 및 파이프라인을 확장 하 여 전체 데이터 집합을 포함할 수 있습니다.

## <a name="troubleshoot-copy-activity-performance"></a>복사 작업 성능 문제 해결

[성능 튜닝 단계](#performance-tuning-steps) 를 따라 시나리오에 대 한 성능 테스트를 계획 하 고 수행 합니다. 그리고 [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)의 Azure Data Factory에서 각 복사 작업 실행의 성능 문제를 해결 하는 방법에 대해 알아봅니다.

## <a name="copy-performance-optimization-features"></a>성능 최적화 기능 복사

Azure Data Factory는 다음과 같은 성능 최적화 기능을 제공 합니다.

- [데이터 통합 단위](#data-integration-units)
- [자체 호스팅 통합 런타임 확장성](#self-hosted-integration-runtime-scalability)
- [병렬 복사](#parallel-copy)
- [준비된 복사](#staged-copy)

### <a name="data-integration-units"></a>데이터 통합 단위

데이터 통합 단위는 Azure Data Factory의 단일 단위에 대 한 전원 (CPU, 메모리 및 네트워크 리소스 할당의 조합)을 나타내는 척도입니다. 데이터 통합 단위는 [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime)에만 적용 되 고 [자체 호스팅 통합 런타임에](concepts-integration-runtime.md#self-hosted-integration-runtime)는 적용 되지 않습니다. [자세히 알아봅니다](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>자체 호스팅 통합 런타임 확장성

증가 하는 동시 작업을 호스팅하거나, 성능을 높이기 위해 자체 호스팅 Integration Runtime 확장 하거나 규모를 확장할 수 있습니다. [자세히 알아봅니다](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>병렬 복사

병렬 복사를 설정 하 여 복사 작업에 사용할 병렬 처리를 나타낼 수 있습니다. 이 속성은 소스에서 읽거나 싱크 데이터 저장소에 병렬로 쓰는 복사 작업 내의 최대 스레드 수로 간주할 수 있습니다. [자세히 알아봅니다](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>준비된 복사

원본 데이터 스토리지에서 싱크 데이터 스토리지에 데이터를 복사할 경우 중간 준비 스토리지로 Blob Storage를 사용하도록 선택할 수 있습니다. [자세히 알아봅니다](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조 하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)
- [복사 작업 성능 최적화 기능](copy-activity-performance-features.md)
- [Azure Data Factory를 사용 하 여 data lake 또는 데이터 웨어하우스의 데이터를 Azure로 마이그레이션](data-migration-guidance-overview.md)
- [Amazon s 3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
