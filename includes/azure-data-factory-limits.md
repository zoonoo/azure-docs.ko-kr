---
title: 포함 파일
description: 포함 파일
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 6d06ac6efd08c14f77fd963ddf2c67de54260959
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733773"
---
Azure Data Factory는 다른 사용자의 워크 로드에서 고객 구독이 보호 되도록 되어에서 다음과 같은 기본 제한이 있는 다중 테 넌 트 서비스입니다. 구독에 대 한 최대 한도 발생 시키려면 지원에 문의 합니다.

### <a name="version-2"></a>버전 2

| 리소스 | 기본 제한 | 최대 제한 |
| -------- | ------------- | ------------- |
| Azure 구독의 데이터 팩터리 | 50 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 총 파이프라인, 데이터 집합, 트리거, 연결 된 서비스 및 데이터 팩터리 내의 통합 런타임 등의 엔터티 수 | 5,000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 하나의 구독으로 Azure SSIS Integration Runtime에 대 한 총 CPU 코어 | 256 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 공장에서 모든 파이프라인 간에 공유 되는 데이터 팩터리 당 동시 파이프라인 실행 | 10000  | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 컨테이너에 대 한 내부 활동을 포함 하는 파이프라인 당 최대 작업 | 40 | 40 |
| 최대 단일 자체 호스팅된 통합 런타임에 대해 만들 수 있는 연결 된 통합 런타임 | 100 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 파이프라인 당 최대 매개 변수 | 50 | 50 |
| ForEach 항목 | 100,000 | 100,000 |
| ForEach 병렬 처리 | 20 | 50 |
| 식당 문자 | 8,192 | 8,192 |
| 최소 연속 창 트리거 간격 | 15분 | 15분 |
| 파이프라인 작업에 대 한 최대 제한 시간에 실행 | 7 일 | 7 일 |
| 파이프라인 개체에 대 한 개체당 바이트<sup>1</sup> | 200KB | 200KB |
| 데이터 집합에 대 한 개체와 연결 된 서비스 개체 별로 바이트<sup>1</sup> | 100KB | 2,000 KB |
| 복사 작업 실행 당 데이터 통합 단위<sup>3</sup> | 256 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API 호출 쓰기 | 2,500/h<br/><br/> 이 제한은 Azure Data Factory가 아닌 Azure Resource Manager가 부과합니다. | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API 호출 읽기 | 12,500/h<br/><br/> 이 제한은 Azure Data Factory가 아닌 Azure Resource Manager가 부과합니다. | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 분당 모니터링 쿼리 | 1,000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 분당 엔터티 CRUD 작업 | 50 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |


### <a name="version-1"></a>버전 1

| **리소스** | **기본 제한** | **최대 한도** |
| --- | --- | --- |
| Azure 구독의 데이터 팩터리 |50 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 데이터 팩터리 내의 파이프라인sd |2,500 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 데이터 팩터리 내의 데이터 집합 |5,000 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 데이터 집합당 동시 조각 |10 |10 |
| 파이프라인 개체에 대 한 개체당 바이트<sup>1</sup> |200KB |200KB |
| 데이터에 대 한 개체당 바이트 설정 및 연결 된 서비스 개체<sup>1</sup> |100KB |2,000 KB |
| 구독 내에서 azure HDInsight 주문형 클러스터 코어<sup>2</sup> |60 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 클라우드 데이터 이동 단위 복사 작업 실행 당<sup>3</sup> |32 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 파이프라인 활동 실행에 대한 재시도 횟수 |1,000 |MaxInt(32비트) |

<sup>1</sup>파이프라인, 데이터 집합 및 연결 된 서비스 개체에서 작업의 논리적 그룹화를 나타냅니다. 이러한 개체에 대 한 제한 없는 Azure Data Factory를 사용 하 여 데이터 이동 및 처리할 수의 크기와 관련이 있습니다. 데이터 팩터리는 페타바이트의 데이터를 처리 하도록 확장 하도록 설계 되었습니다.

<sup>2</sup>주문형 HDInsight 코어는 데이터 팩터리를 포함하는 구독 외부에서 할당됩니다. 결과적으로, 이전 제한은 주문형 HDInsight 코어에 대 한 Data Factory 적용 코어 제한입니다. 연결 된 Azure 구독의 코어 제한을 다릅니다.

<sup>3</sup>데이터 통합 단위 (DIU) 버전 2 용 또는 클라우드 데이터 이동 단위 (DMU) 버전 1에 대 한 클라우드-클라우드 복사 작업에서 사용 됩니다. 이 Data Factory에서 단일 단위의 힘을 나타내는 측정값입니다. CPU, 메모리 및 네트워크 리소스 할당을 결합합니다. 일부 시나리오의 경우 더 많은 DMU를 활용하여 더 높은 복사 처리량을 달성할 수 있습니다. 자세한 내용은 [(버전 2) 데이터 통합 단위](../articles/data-factory/copy-activity-performance.md#data-integration-units) 하 고 [클라우드 데이터 이동 단위 (버전 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)합니다. 청구에 대 한 내용은 참조 하세요 [Azure Data Factory 가격](https://azure.microsoft.com/pricing/details/data-factory/)합니다.

<sup>4</sup>는 IR (통합 런타임)은 데이터 이동, 계산 서비스, 작업 디스 패 츠 같은 다른 네트워크 환경에서 데이터 통합 기능을 제공 하려면 Azure Data Factory에서 사용 하는 계산 인프라 및 SSIS 패키지의 실행 합니다. 자세한 내용은 [통합 런타임 개요](../articles/data-factory/concepts-integration-runtime.md)합니다.

| **리소스** | **기본 하한값** | **최소 제한** |
| --- | --- | --- |
| 일정 간격 |15분 |15분 |
| 재시도 간의 간격 |1초 |1초 |
| 재시도 시간 제한 값 |1초 |1초 |

#### <a name="web-service-call-limits"></a>웹 서비스 호출 제한
Azure Resource Manager에는 API 호출 제한이 있습니다. [Azure 리소스 관리자 API 제한](../articles/azure-subscription-service-limits.md#resource-group-limits)범위 안의 속도로 API 호출을 수행할 수 있습니다.
