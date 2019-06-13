---
title: 포함 파일
description: 포함 파일
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427587"
---
Azure Data Factory는 다른 사용자의 워크 로드에서 고객 구독이 보호 되도록 되어에서 다음과 같은 기본 제한이 있는 다중 테 넌 트 서비스입니다. 구독에 대 한 최대 한도 발생 시키려면 지원에 문의 합니다.

### <a name="version-2"></a>버전 2

| Resource | 기본 제한 | 최대 제한 |
| -------- | ------------- | ------------- |
| Azure 구독의 데이터 팩터리 | 50 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 총 파이프라인, 데이터 집합, 트리거, 연결 된 서비스 및 데이터 팩터리 내의 통합 런타임 등의 엔터티 수 | 5,000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 하나의 구독으로 Azure SSIS Integration Runtime에 대 한 총 CPU 코어 | 256 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 공장에서 모든 파이프라인 간에 공유 되는 데이터 팩터리 당 동시 파이프라인 실행 | 10000  | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 구독 당 동시 외부 작업 실행 [Azure 통합 런타임 영역](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>외부 활동 integration runtime에서 관리 되는 하지만 Databricks, 저장된 프로시저, HDInsights, 등 연결 된 서비스를 실행 합니다.</small> | 3000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 구독 당 동시 파이프라인 작업 실행 [Azure 통합 런타임 영역](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>파이프라인 작업 조회, GetMetadata를 포함 하 여 integration runtime에서 실행 하 고 삭제 합니다. </small>| 1000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 구독 당 작업을 작성 하는 동시 [Azure 통합 런타임 영역](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>연결 테스트, 브라우저 폴더 목록 및 테이블 목록에 포함 하 여 데이터를 미리 봅니다. | 200 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 동시 데이터 통합 단위<sup>1</sup> 구독 당 소비 [Azure 통합 런타임 영역](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| 지역 그룹 1<sup>2</sup>: 6000<br>지역 그룹 2<sup>2</sup>: 3000<br>지역 그룹 3<sup>2</sup>: 1500 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 컨테이너에 대 한 내부 활동을 포함 하는 파이프라인 당 최대 작업 | 40 | 40 |
| 최대 단일 자체 호스팅된 통합 런타임에 대해 만들 수 있는 연결 된 통합 런타임 | 100 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 파이프라인 당 최대 매개 변수 | 50 | 50 |
| ForEach 항목 | 100,000 | 100,000 |
| ForEach 병렬 처리 | 20 | 50 |
| 식당 문자 | 8,192 | 8,192 |
| 최소 연속 창 트리거 간격 | 15분 | 15분 |
| 파이프라인 작업에 대 한 최대 제한 시간에 실행 | 7 일 | 7 일 |
| 파이프라인 개체에 대 한 개체당 바이트<sup>3</sup> | 200KB | 200KB |
| 데이터 집합에 대 한 개체와 연결 된 서비스 개체 별로 바이트<sup>3</sup> | 100KB | 2,000 KB |
| 데이터 통합 단위<sup>1</sup> 복사 작업 실행 당 | 256 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API 호출 쓰기 | 2,500/h<br/><br/> 이 제한은 Azure Data Factory가 아닌 Azure Resource Manager가 부과합니다. | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API 호출 읽기 | 12,500/h<br/><br/> 이 제한은 Azure Data Factory가 아닌 Azure Resource Manager가 부과합니다. | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 분당 모니터링 쿼리 | 1,000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 분당 엔터티 CRUD 작업 | 50 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> 에서 자세한 정보를 데이터 통합 단위 (DIU) 클라우드-클라우드 복사 작업에서 사용할 [(버전 2) 데이터 통합 단위](../articles/data-factory/copy-activity-performance.md#data-integration-units)합니다. 청구에 대 한 내용은 참조 하세요 [Azure Data Factory 가격](https://azure.microsoft.com/pricing/details/data-factory/)합니다.

<sup>2</sup> [azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) 됩니다 [전역적으로 사용 가능한](https://azure.microsoft.com/global-infrastructure/services/) 효율성 및 네트워크 송신 비용 데이터 규정 준수를 보장 하려면. 

| 영역 그룹 | 영역 | 
| -------- | ------ |
| 지역 그룹 1 | 미국 중부, 미국 동부, 미국 동부 2, 북유럽, 유럽 서 부, 미국 서 부, 미국 서 부 2 |
| 지역 그룹 2 | 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 인도 중부, 일본 동부, 미국 서 부, 동남 아시아, 미국 중남부, 미국 Northcentral |
| 지역 그룹 3 | 캐나다 중부, 동남 아시아, 프랑스 중부, 한국 중부, 영국 남부 |

<sup>3</sup> 파이프라인, 데이터 집합 및 연결 된 서비스 개체에서 작업의 논리적 그룹화를 나타냅니다. 이러한 개체에 대 한 제한 없는 Azure Data Factory를 사용 하 여 데이터 이동 및 처리할 수의 크기와 관련이 있습니다. 데이터 팩터리는 페타바이트의 데이터를 처리 하도록 확장 하도록 설계 되었습니다.

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

<sup>1</sup> 파이프라인, 데이터 집합 및 연결 된 서비스 개체에서 작업의 논리적 그룹화를 나타냅니다. 이러한 개체에 대 한 제한 없는 Azure Data Factory를 사용 하 여 데이터 이동 및 처리할 수의 크기와 관련이 있습니다. 데이터 팩터리는 페타바이트의 데이터를 처리 하도록 확장 하도록 설계 되었습니다.

<sup>2</sup> 주문형 HDInsight 코어는 데이터 팩터리를 포함하는 구독 외부에서 할당됩니다. 결과적으로, 이전 제한은 주문형 HDInsight 코어에 대 한 Data Factory 적용 코어 제한입니다. 연결 된 Azure 구독의 코어 제한을 다릅니다.

<sup>3</sup> 클라우드 데이터 이동 단위 (DMU)는 클라우드-클라우드 복사 작업에서 사용 하는 버전 1에서 자세히 알아봅니다 [클라우드 데이터 이동 단위 (버전 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)합니다. 청구에 대 한 내용은 참조 하세요 [Azure Data Factory 가격](https://azure.microsoft.com/pricing/details/data-factory/)합니다.

| **리소스** | **기본 하한값** | **최소 제한** |
| --- | --- | --- |
| 일정 간격 |15분 |15분 |
| 재시도 간의 간격 |1 초 |1 초 |
| 재시도 시간 제한 값 |1 초 |1 초 |

#### <a name="web-service-call-limits"></a>웹 서비스 호출 제한
Azure Resource Manager에는 API 호출 제한이 있습니다. [Azure 리소스 관리자 API 제한](../articles/azure-subscription-service-limits.md#resource-group-limits)범위 안의 속도로 API 호출을 수행할 수 있습니다.
