---
title: 복사 활동 성능 및 확장성 가이드
description: 복사 작업을 사용할 때 Azure Data Factory에서 데이터 이동 성능에 영향을 주는 주요 요인에 대해 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261399"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>복사 활동 성능 및 확장성 가이드

> [!div class="op_single_selector" title1="사용 하는 Azure 데이터 팩터리의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-copy-activity-performance.md)
> * [현재 버전](copy-activity-performance.md)

데이터 레이크 또는 엔터프라이즈 데이터 웨어하우스(EDW)에서 Azure로 대규모 데이터 마이그레이션을 수행하거나 다른 소스에서 대규모 데이터를 Azure로 수집하여 빅 데이터 분석을 수행하려는 경우 최적의 성능을 달성하는 것이 중요합니다. 확장성.  Azure Data Factory는 대규모로 데이터를 수집할 수 있는 성능, 복원력 및 비용 효율적인 메커니즘을 제공하므로 성능이 뛰어나고 확장 성이 뛰어난 데이터 수집 파이프라인을 구축하려는 데이터 엔지니어에게 적합합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- 데이터 마이그레이션 및 데이터 수집 시나리오에 ADF 복사 활동을 사용하여 달성할 수 있는 성능 및 확장성 수준은 어느 수준입니까?

- ADF 복사 활동의 성능을 조정하기 위해 어떤 단계를 수행해야 합니까?
- 단일 복사 활동 실행을 위한 성능을 최적화하기 위해 어떤 ADF 퍼프 최적화 노브를 활용할 수 있습니까?
- 복사 성능을 최적화할 때 ADF 외부에서 고려해야 할 다른 요소는 무엇입니까?

> [!NOTE]
> 일반적으로 복사 활동에 익숙하지 않은 경우 이 문서를 읽기 전에 [복사 활동 개요를](copy-activity-overview.md) 참조하세요.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF를 사용하여 성능 및 확장성 복사 가능

ADF는 다양한 수준에서 병렬 처리가 가능한 서버리스 아키텍처를 제공하므로 개발자는 파이프라인을 빌드하여 네트워크 대역폭뿐만 아니라 스토리지 IOPS 및 대역폭을 최대한 활용하여 환경에 대한 데이터 이동 처리량을 최대화할 수 있습니다.  즉, 원본 데이터 저장소, 대상 데이터 저장소 및 소스와 대상 사이의 네트워크 대역폭에서 제공하는 최소 처리량을 측정하여 달성할 수 있는 처리량을 추정할 수 있습니다.  아래 표는 데이터 크기와 환경의 대역폭 제한을 기준으로 복사 기간을 계산합니다. 

| 데이터 크기 / <br/> bandwidth | 50Mbps    | 100Mbps  | 500Mbps  | 1Gbps   | 5Gbps   | 10Gbps  | 50Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 약 2.7분    | 약 1.4분   | 0.3분   | 0.1분  | 0.03 분 | 0.01 분 | 0.0분   |
| **10 GB**                   | 27.3분   | 약 13.7분  | 약 2.7분   | 약 1.3분  | 0.3분  | 0.1분  | 0.03 분  |
| **100GB**                  | 4.6시간    | 2.3시간   | 0.5시간   | 0.2시간  | 0.05시간 | 0.02시간 | 0.0시간   |
| **1 TB**                    | 46.6시간   | 23.3시간  | 4.7시간   | 2.3시간  | 0.5시간  | 0.2시간  | 0.05시간  |
| **10TB**                   | 19.4 일  | 9.7 일  | 1.9 일  | 0.9 일 | 0.2 일 | 0.1 일 | 0.02 일 |
| **100TB**                  | 194.2 일 | 97.1 일 | 19.4 일 | 9.7 일 | 1.9 일 | 1일    | 0.2 일  |
| **1PB**                    | 64.7 월    | 32.4 모   | 6.5 모    | 3.2 모   | 0.6 모   | 0.3 모   | 0.06 모   |
| **10 PB**                   | 647.3 월   | 323.6 월  | 64.7 월   | 31.6 모  | 6.5 모   | 3.2 모   | 0.6 모    |

ADF 복사본은 다양한 수준에서 확장 가능합니다.

![ADF 복사 스케일 방법](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 컨트롤 흐름은 예를 들어 [For Each 루프를](control-flow-for-each-activity.md)사용하여 여러 복사 활동을 병렬로 시작할 수 있습니다.
- 단일 복사 활동은 확장 가능한 계산 리소스를 활용할 수 있습니다. [up to 256 DIUs](#data-integration-units) 자체 호스팅 통합 런타임을 사용하는 경우 수동으로 컴퓨터를 확장하거나 여러[컴퓨터(최대 4개 노드)로](create-self-hosted-integration-runtime.md#high-availability-and-scalability)확장할 수 있으며 단일 복사 활동은 모든 노드에서 파일 집합을 분할합니다.
- 단일 복사 활동은 여러 스레드를 [병렬로](#parallel-copy)사용하여 데이터 저장소에서 읽고 씁니다.

## <a name="performance-tuning-steps"></a>성능 튜닝 단계

다음 단계를 수행하여 복사 활동과 Azure Data Factory 서비스의 성능을 조정합니다.

1. **테스트 데이터 집합을 선택하고 기준을 설정합니다.** 개발 단계에서 는 대표 데이터 샘플에 대해 복사 작업을 사용하여 파이프라인을 테스트합니다. 선택한 데이터 집합은 일반적인 데이터 패턴(폴더 구조, 파일 패턴, 데이터 스키마 등)을 나타내야 하며 복사 작업을 완료하는 데 10분 또는 그 이상이 걸리는 등 복사 성능을 평가할 수 있을 만큼 큽해야 합니다. 복사 활동 모니터링 다음 실행 세부 정보 및 성능 [특성을](copy-activity-monitoring.md)수집합니다.

2. **단일 복사 활동의 성능을 최대화하는 방법**:

   먼저 단일 복사 활동을 사용하여 성능을 최대화하는 것이 좋습니다.

   - **Azure 통합 런타임에서 복사 작업이 실행되는 경우:** [DIU(데이터 통합 단위)](#data-integration-units) 및 [병렬 복사](#parallel-copy) 설정에 대한 기본값으로 시작합니다. 

   - **자체 호스팅 통합 런타임에서 복사 작업이 실행되는 경우 통합 런타임을** 호스트하기 위해 데이터 저장소를 호스팅하는 서버와 별도로 전용 컴퓨터를 사용하는 것이 좋습니다. [병렬 복사](#parallel-copy) 설정에 대한 기본값으로 시작하고 자체 호스팅 IR에 대한 단일 노드를 사용합니다.  

   성능 테스트 실행을 수행하고 DIUs 및 병렬 복사본과 같은 실제 값뿐만 아니라 달성된 성능에 대해 기록합니다. 사용된 실행 결과 및 성능 설정을 수집하는 방법에 대한 [복사 활동 모니터링을](copy-activity-monitoring.md) 참조하고 복사 활동 성능 문제를 [해결하여](copy-activity-performance-troubleshooting.md) 병목 현상을 식별하고 해결하는 방법을 알아봅니다. 

   문제 해결 및 튜닝 지침에 따라 추가 성능 테스트 실행을 반복합니다. 단일 복사 작업 실행이 더 나은 처리량을 달성할 수 없는 경우 3단계를 참조하는 여러 복사본을 동시에 실행하여 집계 처리량을 최대화하는 것이 좋습니다.


3. **여러 복사본을 동시에 실행하여 집계 처리량을 최대화하는 방법:**

   이제 단일 복사 작업의 성능을 최대화되었으므로 네트워크, 원본 데이터 저장소 및 대상 데이터 저장소와 같은 환경의 처리량 상한을 아직 달성하지 못한 경우 [For Each 루프와](control-flow-for-each-activity.md)같은 ADF 제어 흐름 구문구조를 사용하여 여러 복사 작업을 병렬로 실행할 수 있습니다. 여러 [컨테이너에서 파일 복사,](solution-template-copy-files-multiple-containers.md) [Amazon S3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md)또는 일반적인 예로 제어 테이블 솔루션 [템플릿을 사용하여 대량 복사를](solution-template-bulk-copy-with-control-table.md) 참조합니다.

5. **구성을 전체 데이터 집합으로 확장합니다.** 실행 결과 및 성능이 만족스니면 정의 및 파이프라인을 확장하여 전체 데이터 집합을 포괄할 수 있습니다.

## <a name="troubleshoot-copy-activity-performance"></a>복사 작업 성능 문제 해결

성능 [조정 단계를](#performance-tuning-steps) 수행하여 시나리오에 대한 성능 테스트를 계획하고 수행합니다. 또한 문제 [해결 복사 활동 성능에서](copy-activity-performance-troubleshooting.md)Azure Data Factory에서 각 복사 활동 실행의 성능 문제를 해결하는 방법을 알아봅니다.

## <a name="copy-performance-optimization-features"></a>성능 최적화 기능 복사

Azure 데이터 팩터리는 다음과 같은 성능 최적화 기능을 제공합니다.

- [데이터 통합 단위](#data-integration-units)
- [자체 호스팅 통합 런타임 확장성](#self-hosted-integration-runtime-scalability)
- [병렬 복사](#parallel-copy)
- [준비된 복사](#staged-copy)

### <a name="data-integration-units"></a>데이터 통합 단위

데이터 통합 단위는 Azure Data Factory에서 단일 단위의 전원(CPU, 메모리 및 네트워크 리소스 할당의 조합)을 나타내는 측정값입니다. 데이터 통합 단위는 [Azure 통합 런타임에만](concepts-integration-runtime.md#azure-integration-runtime)적용되지만 [자체 호스팅 통합 런타임에는](concepts-integration-runtime.md#self-hosted-integration-runtime)적용되지 않습니다. [자세히 알아봅니다](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>자체 호스팅 통합 런타임 확장성

증가하는 동시 워크로드를 호스팅하거나 더 높은 성능을 얻으려면 자체 호스팅 통합 런타임을 확장하거나 확장할 수 있습니다. [자세히 알아봅니다](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>병렬 복사

병렬 복사본을 설정하여 복사 활동을 사용할 병렬성을 나타낼 수 있습니다. 이 속성은 원본에서 읽거나 싱크 데이터 저장소에 병렬로 쓰는 복사 작업 내의 최대 스레드 수로 생각할 수 있습니다. [자세히 알아봅니다](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>준비된 복사

원본 데이터 스토리지에서 싱크 데이터 스토리지에 데이터를 복사할 경우 중간 준비 스토리지로 Blob Storage를 사용하도록 선택할 수 있습니다. [자세히 알아봅니다](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>다음 단계
다른 복사 활동 문서를 참조하십시오.

- [활동 개요 복사](copy-activity-overview.md)
- [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)
- [활동 성능 최적화 기능 복사](copy-activity-performance-features.md)
- [Azure 데이터 팩터리를 사용하여 데이터 레이크 또는 데이터 웨어하우스에서 Azure로 데이터를 마이그레이션합니다.](data-migration-guidance-overview.md)
- [Amazon S3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
