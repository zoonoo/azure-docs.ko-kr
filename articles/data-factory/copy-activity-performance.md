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
ms.date: 09/15/2020
ms.openlocfilehash: a6e89883ec0e1e493bad9572876af86f4a0d3853
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324446"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>복사 작업 성능 및 확장성 가이드

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory 버전을 선택하세요."]
> * [버전 1](v1/data-factory-copy-activity-performance.md)
> * [현재 버전](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data lake 또는 EDW (엔터프라이즈 데이터 웨어하우스)에서 Azure로 대규모 데이터 마이그레이션을 수행 하려는 경우가 있습니다. 빅 데이터 분석을 위해 다양 한 원본에서 Azure로 많은 양의 데이터를 수집 하려는 경우도 있습니다. 각 경우에서 최적의 성능 및 확장성을 구현 하는 것이 중요 합니다.

ADF (Azure Data Factory)는 데이터를 수집 하는 메커니즘을 제공 합니다. ADF의 이점은 다음과 같습니다.

* 많은 양의 데이터를 처리 합니다.
* 고성능
* 비용 효율적

이러한 이점은 뛰어난 확장성이 뛰어난 데이터 수집 파이프라인을 구축 하려는 데이터 엔지니어에 게 적합 합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

* 데이터 마이그레이션 및 데이터 수집 시나리오에서 ADF 복사 작업을 사용 하 여 어떤 수준의 성능 및 확장성을 달성할 수 있나요?
* ADF 복사 작업의 성능을 조정 하기 위해 수행 해야 하는 단계는 무엇 인가요?
* 단일 복사 작업 실행의 성능을 최적화 하기 위해 활용할 수 있는 ADF 성능 최적화 노브 무엇 인가요?
* ADF 외부에서 복사 성능을 최적화할 때 고려할 다른 요소는 무엇 인가요?

> [!NOTE]
> 일반적으로 복사 작업에 익숙하지 않은 경우이 문서를 읽기 전에 [복사 작업 개요](copy-activity-overview.md) 를 참조 하세요.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF를 사용 하 여 달성 가능한 복사 성능 및 확장성

ADF는 서로 다른 수준에서 병렬 처리를 허용 하는 서버 리스 아키텍처를 제공 합니다.

이 아키텍처를 통해 사용자 환경에 대 한 데이터 이동 처리량을 최대화 하는 파이프라인을 개발할 수 있습니다. 이러한 파이프라인은 다음 리소스를 완벽 하 게 활용 합니다.

* 네트워크 대역폭
* 초당 저장소 입/출력 작업 (IOPS) 및 대역폭

이 전체 사용률을 통해 다음 리소스와 함께 사용할 수 있는 최소 처리량을 측정 하 여 전체 처리량을 예측할 수 있습니다.

* 원본 데이터 저장소
* 대상 데이터 저장소
* 원본 및 대상 데이터 저장소 간의 네트워크 대역폭

아래 표에서는 복사 기간을 계산 합니다. 기간은 사용자 환경의 데이터 크기와 대역폭 제한을 기반으로 합니다.

&nbsp;

| 데이터 크기/ <br/> bandwidth | 50Mbps    | 100Mbps  | 500Mbps  | 1Gbps   | 5Gbps   | 10Gbps  | 50Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 분    | 1.4 분   | 0.3 분   | 0.1 분  | 0.03 분 | 0.01 분 | 0.0 분   |
| **10 GB**                   | 27.3 분   | 13.7 분  | 2.7 분   | 1.3 분  | 0.3 분  | 0.1 분  | 0.03 분  |
| **100GB**                  | 4.6 시간    | 2.3 시간   | 0.5 시간   | 0.2 시간  | 0.05 시간 | 0.02 시간 | 0.0 시간   |
| **1TB**                    | 46.6 시간   | 23.3 시간  | 4.7 시간   | 2.3 시간  | 0.5 시간  | 0.2 시간  | 0.05 시간  |
| **10TB**                   | 19.4 일  | 9.7 일  | 1.9 일  | 0.9 일 | 0.2 일 | 0.1 일 | 0.02 일 |
| **100TB**                  | 194.2 일 | 97.1 일 | 19.4 일 | 9.7 일 | 1.9 일 | 1일    | 0.2 일  |
| **1PB**                    | 64.7 mo    | 32.4 mo   | 6.5 mo    | 3.2 mo   | 0.6 mo   | 0.3 mo   | 0.06 mo   |
| **10gb**                   | 647.3 mo   | 323.6 mo  | 64.7 mo   | 31.6 mo  | 6.5 mo   | 3.2 mo   | 0.6 mo    |
| | |  | | |  | | |

ADF 복사본은 다양 한 수준에서 확장 가능 합니다.

![ADF 복사본 크기 조정 방법](media/copy-activity-performance/adf-copy-scalability.png)

* ADF 제어 흐름은 여러 복사 작업을 병렬로 시작할 수 있습니다(예: [For Each 루프](control-flow-for-each-activity.md) 사용).

* 단일 복사 작업은 확장 가능한 계산 리소스를 활용할 수 있습니다.
  * IR (Azure integration runtime)을 사용 하는 경우 각 복사 작업에 대해 [최대 256 개의 DIUs (데이터 통합 단위)](#data-integration-units) 를 서버를 사용 하지 않는 방식으로 지정할 수 있습니다.
  * 자체 호스팅 IR을 사용 하는 경우 다음 방법 중 하나를 사용할 수 있습니다.
    * 수동으로 컴퓨터를 확장 합니다.
    * 여러 컴퓨터 ([최대 4 개 노드](create-self-hosted-integration-runtime.md#high-availability-and-scalability))로 규모를 확장 하 고, 단일 복사 작업은 모든 노드에 걸쳐 해당 파일 집합을 분할 합니다.

* 단일 복사 작업은 여러 스레드를 [병렬로](#parallel-copy)사용 하 여 데이터 저장소에서 읽고 씁니다.

## <a name="performance-tuning-steps"></a>성능 튜닝 단계

복사 작업을 사용 하 여 Azure Data Factory 서비스의 성능을 조정 하려면 다음 단계를 수행 합니다.

1. **테스트 데이터 집합을 선택 하 고 기준선을 설정 합니다.**

    개발 하는 동안 대표 데이터 샘플에 대 한 복사 작업을 사용 하 여 파이프라인을 테스트 합니다. 선택한 데이터 집합은 다음과 같은 특성을 따르는 일반적인 데이터 패턴을 나타내야 합니다.

    * 폴더 구조
    * 파일 패턴
    * 데이터 스키마

    그리고 데이터 집합은 복사 성능을 평가할 만큼 충분히 커야 합니다. 복사 작업이 완료 되기까지 좋은 크기는 10 분 이상 소요 됩니다. [복사 작업 모니터링](copy-activity-monitoring.md)을 수행 하 여 실행 세부 정보 및 성능 특징을 수집 합니다.

2. **단일 복사 작업의 성능을 최대화 하는 방법**:

    먼저 단일 복사 작업을 사용 하 여 성능을 최대화 하는 것이 좋습니다.

    * **_Azure_ integration runtime에서 복사 작업을 실행 하는 경우:**

        [DIU (데이터 통합 단위](#data-integration-units) ) 및 [병렬 복사](#parallel-copy) 설정에 대 한 기본값을 사용 하 여 시작 합니다.

    * **복사 작업이 _자체 호스팅_ 통합 런타임에서 실행 되는 경우:**

        IR을 호스트 하는 전용 컴퓨터를 사용 하는 것이 좋습니다. 컴퓨터는 데이터 저장소를 호스트 하는 서버와 분리 해야 합니다. [병렬 복사](#parallel-copy) 설정의 기본값을 사용 하 여 시작 하 고 자체 호스팅 IR에 단일 노드를 사용 합니다.

    성능 테스트 실행을 수행 합니다. 달성 한 성능을 적어 둡니다. 사용 되는 실제 값 (예: DIUs 및 병렬 복사본)을 포함 합니다. 사용 되는 실행 결과 및 성능 설정을 수집 하는 방법에 대 한 [복사 작업 모니터링](copy-activity-monitoring.md) 을 참조 하세요. 병목 상태를 식별 하 고 해결 하기 위해 [복사 작업 성능 문제를 해결](copy-activity-performance-troubleshooting.md) 하는 방법에 대해 알아봅니다.

    문제 해결 및 튜닝 지침에 따라 추가 성능 테스트 실행을 수행 하려면 반복 합니다. 단일 복사 작업 실행이 더 나은 처리량을 달성할 수 없는 경우 여러 복사본을 동시에 실행 하 여 집계 처리량을 최대화할 지 여부를 고려 합니다. 이 옵션은 번호가 매겨진 다음 글머리 기호에 설명 되어 있습니다.

3. **여러 복사본을 동시에 실행 하 여 집계 처리량을 최대화 하는 방법:**

    이제 단일 복사 작업의 성능을 최대화 했습니다. 환경에 대 한 처리량 상한을 아직 달성 하지 않은 경우 여러 개의 복사 작업을 병렬로 실행할 수 있습니다. ADF 제어 흐름 구문을 사용 하 여 병렬로 실행할 수 있습니다. 이러한 구문 중 하나는 [For each 루프](control-flow-for-each-activity.md)입니다. 자세한 내용은 솔루션 템플릿에 대 한 다음 문서를 참조 하세요.

    * [여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)
    * [Amazon s 3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md)
    * [컨트롤 테이블을 사용 하 여 대량 복사](solution-template-bulk-copy-with-control-table.md)

4. **전체 데이터 집합에 대 한 구성을 확장 합니다.**

    실행 결과 및 성능에 만족 하는 경우 정의 및 파이프라인을 확장 하 여 전체 데이터 집합을 포함할 수 있습니다.

## <a name="troubleshoot-copy-activity-performance"></a>복사 작업 성능 문제 해결

[성능 튜닝 단계](#performance-tuning-steps) 를 따라 시나리오에 대 한 성능 테스트를 계획 하 고 수행 합니다. 그리고 [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)의 Azure Data Factory에서 각 복사 작업 실행의 성능 문제를 해결 하는 방법에 대해 알아봅니다.

## <a name="copy-performance-optimization-features"></a>성능 최적화 기능 복사

Azure Data Factory는 다음과 같은 성능 최적화 기능을 제공 합니다.

* [데이터 통합 단위](#data-integration-units)
* [자체 호스팅 통합 런타임 확장성](#self-hosted-integration-runtime-scalability)
* [병렬 복사](#parallel-copy)
* [준비된 복사](#staged-copy)

### <a name="data-integration-units"></a>데이터 통합 단위

DIU (데이터 통합 단위)는 Azure Data Factory에서 단일 단위의 기능을 나타내는 측정값입니다. 전원은 CPU, 메모리 및 네트워크 리소스 할당의 조합입니다. DIU는 [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime)에만 적용 됩니다. DIU는 [자체 호스팅 통합 런타임에](concepts-integration-runtime.md#self-hosted-integration-runtime)적용 되지 않습니다. [자세한 내용은 여기를 참조](copy-activity-performance-features.md#data-integration-units)하세요.

### <a name="self-hosted-integration-runtime-scalability"></a>자체 호스팅 통합 런타임 확장성

늘어난 동시 작업을 호스트할 수 있습니다. 또는 현재 워크 로드 수준에서 더 높은 성능을 달성할 수 있습니다. 다음 방법으로 처리 규모를 향상 시킬 수 있습니다.

* 노드에서 실행할 수 있는 [동시 작업](create-self-hosted-integration-runtime.md#scale-up) 수를 늘려서 자체 호스팅 IR _을 확장할 수_ 있습니다.  
수직 확장은 노드의 프로세서와 메모리가 완전히 활용 된 것 보다 작은 경우에만 작동 합니다.
* 노드 (컴퓨터 _)를 더_ 추가 하 여 자체 호스팅 IR을 확장할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [복사 작업 성능 최적화 기능: 자체 호스팅 통합 런타임 확장성](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [자체 호스팅 통합 런타임 만들기 및 구성: 크기 조정 고려 사항](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>병렬 복사

속성을 설정 `parallelCopies` 하 여 복사 작업에 사용할 병렬 처리를 나타낼 수 있습니다. 이 속성은 복사 작업 내의 최대 스레드 수로 간주 합니다. 스레드가 병렬로 작동 합니다. 스레드는 원본에서 읽거나 싱크 데이터 저장소에 기록 합니다. [자세히 알아봅니다](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>준비된 복사

데이터 복사 작업은 데이터를 싱크 데이터 저장소로 _직접_ 보낼 수 있습니다. 또는 Blob storage를 _임시 준비_ 저장소로 사용 하도록 선택할 수 있습니다. [자세히 알아보기](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>다음 단계

다른 복사 작업 문서를 참조하세요.

* [복사 작업 개요](copy-activity-overview.md)
* [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)
* [복사 작업 성능 최적화 기능](copy-activity-performance-features.md)
* [Azure Data Factory를 사용 하 여 data lake 또는 데이터 웨어하우스의 데이터를 Azure로 마이그레이션](data-migration-guidance-overview.md)
* [Amazon S3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
