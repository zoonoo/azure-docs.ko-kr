---
title: 포함 파일
description: 포함 파일
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086181"
---
Azure Data Factory는 고객 구독이 서로의 워크로드로부터 보호되도록 하기 위해 다음과 같은 기본 제한이 있는 다중 테넌트 서비스입니다. 구독한도를 최대로 높이려면 지원팀에 문의하세요.

### <a name="version-2"></a>버전 2

| 리소스 | 기본 제한 | 최대 제한 |
| -------- | ------------- | ------------- |
| Azure 구독의 데이터 팩터리 | 800 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 데이터 팩터리 내에서 파이프라인, 데이터 세트, 트리거, 연결된 서비스 및 통합 런타임과 같은 총 엔터티 수 | 5,000 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 하나의 구독에서 Azure-SSIS 통합 런타임에 대한 총 CPU 코어 | 256 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 팩터리의 모든 파이프라인 간에 공유되는 데이터 팩터리당 동시 파이프라인 실행 | 10000  | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| [Azure 통합 런타임 리전 리전 리전당](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) 구독당 동시 외부 활동 실행<br><small>외부 활동은 통합 런타임에 관리되지만 Databricks, 저장 프로시저, HDInsights, 웹 등을 비롯한 연결된 서비스에서 실행됩니다.</small> | 3000 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| [Azure 통합 런타임 리전 리전 리전당](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) 구독당 동시 파이프라인 활동 실행 <br><small>파이프라인 활동은 조회, GetMetadata 및 삭제를 포함하여 통합 런타임에서 실행됩니다.</small>| 1000 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| [Azure 통합 런타임 리전 리전 지역별](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) 구독당 동시 작성 작업<br><small>테스트 연결, 찾아보기 폴더 목록 및 테이블 목록, 미리 보기 데이터를 포함합니다. | 200 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| [Azure 통합 런타임 리전 리전당](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) 구독당 동시 데이터 통합 단위<sup>1</sup> 소비| 지역 그룹 1<sup>2</sup>: 6000<br>지역 그룹 2<sup>2</sup>: 3000<br>지역 그룹 3<sup>2</sup>: 1500 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 컨테이너에 대한 내부 활동을 포함하는 파이프라인당 최대 활동 | 40 | 40 |
| 단일 자체 호스팅 통합 런타임에 대해 생성할 수 있는 연결된 통합 런타임 최대 수 | 100 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 파이프라인당 최대 파라미터 | 50 | 50 |
| ForEach 항목 | 100,000 | 100,000 |
| ForEach 병렬 처리 | 20 | 50 |
| 파이프라인당 최대 큐에 대기된 실행 | 100 | 100 |
| 식당 문자 | 8,192 | 8,192 |
| 최소 텀블링 윈도우 트리거 간격 | 15분 | 15분 |
| 파이프라인 활동 실행에 대한 최대 시간 초과 | 7 일 | 7 일 |
| 파이프라인 개체에 대한 개체당 바이트<sup>3</sup> | 200KB | 200KB |
| 데이터 집합 및 연결된 서비스 개체에 대한 개체당 바이트<sup>3</sup> | 100KB | 2,000KB |
| 복사 활동 실행당 데이터 통합 단위<sup>1</sup> | 256 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| API 호출 쓰기 | 1,200/h<br/><br/> 이 제한은 Azure Data Factory가 아닌 Azure Resource Manager가 부과합니다. | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| API 호출 읽기 | 12,500/h<br/><br/> 이 제한은 Azure Data Factory가 아닌 Azure Resource Manager가 부과합니다. | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 분당 모니터링 쿼리 | 1,000 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 분당 엔터티 CRUD 작업 | 50 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 데이터 흐름 디버그 세션의 최대 시간 | 8시간 | 8시간 |
| 공장별 동시 데이터 흐름 수 | 50 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 공장당 사용자당 데이터 흐름 디버그 세션의 동시 수 | 3 | 3 |
| 데이터 흐름 Azure IR TTL 제한 | 4시간 | [지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |

<sup>1</sup> DIU(데이터 통합 장치)는 클라우드-클라우드 복사 작업에 사용되며 데이터 [통합 장치(버전 2)에서](../articles/data-factory/copy-activity-performance.md#data-integration-units)자세히 알아봅니다. 청구에 대한 자세한 내용은 [Azure 데이터 팩터리 가격 책정을](https://azure.microsoft.com/pricing/details/data-factory/)참조하십시오.

<sup>2</sup> [Azure 통합 런타임은](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) 데이터 규정 준수, 효율성 및 네트워크 송신 비용 절감을 보장하기 위해 전 세계적으로 사용할 [수](https://azure.microsoft.com/global-infrastructure/services/) 있습니다. 

| 지역 그룹 | 영역 | 
| -------- | ------ |
| 지역 그룹 1 | 미국 중부, 미국 동부, US2, 북유럽, 서유럽, 미국 서부, 미국 서부 2 |
| 지역 그룹 2 | 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 인도 중부, 일본 동부, 미국 북중부, 미국 중남부, 동남아시아, 미국 중서부 |
| 지역 그룹 3 | 캐나다 중부, 동아시아, 프랑스 중부, 한국 중부, 영국 남부 |

<sup>3</sup> 파이프라인, 데이터 집합 및 연결된 서비스 개체는 워크로드의 논리적 그룹화입니다. 이러한 개체에 대한 제한은 Azure Data Factory를 사용하여 이동하고 처리할 수 있는 데이터의 양과 관련이 없습니다. 데이터 팩터리는 페타바이트 규모의 데이터를 처리하도록 설계되었습니다.

### <a name="version-1"></a>버전 1

| **리소스** | **기본 제한** | **최대 제한** |
| --- | --- | --- |
| 데이터 팩터리 내의 파이프라인sd |2,500 |[지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 데이터 팩터리 내의 데이터 집합 |5,000 |[지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 데이터 집합당 동시 슬라이스 |10 |10 |
| 파이프라인 개체에 대한 개체당 바이트<sup>1</sup> |200KB |200KB |
| 데이터 집합 및 연결된 서비스 개체에 대한 개체당 바이트<sup>1</sup> |100KB |2,000KB |
| 구독<sup>2</sup> 내에서 Azure HDInsight 온디맨드 클러스터 코어 |60 |[지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 복사 활동당 클라우드 데이터 이동 단위 실행<sup>3</sup> |32 |[지원 팀에 문의하십시오.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 파이프라인 활동 실행에 대한 재시도 횟수 |1,000 |MaxInt(32비트) |

<sup>1</sup> 파이프라인, 데이터 집합 및 연결된 서비스 개체는 워크로드의 논리적 그룹화입니다. 이러한 개체에 대한 제한은 Azure Data Factory를 사용하여 이동하고 처리할 수 있는 데이터의 양과 관련이 없습니다. 데이터 팩터리는 페타바이트 규모의 데이터를 처리하도록 설계되었습니다.

<sup>2</sup> 주문형 HDInsight 코어는 데이터 팩터리를 포함하는 구독 외부에서 할당됩니다. 따라서 이전 제한은 온디맨드 HDInsight 코어에 대한 데이터 팩토리 적용 코어 제한입니다. Azure 구독과 연결된 핵심 제한과 다릅니다.

<sup>3</sup> 버전 1의 클라우드 데이터 이동 장치(DMU)는 클라우드-클라우드 복사 작업에서 사용되며 클라우드 [데이터 이동 장치(버전 1)에서](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)자세히 알아봅니다. 청구에 대한 자세한 내용은 [Azure 데이터 팩터리 가격 책정을](https://azure.microsoft.com/pricing/details/data-factory/)참조하십시오.

| **리소스** | **기본 하한값** | **최소 제한** |
| --- | --- | --- |
| 일정 간격 |15분 |15분 |
| 재시도 간의 간격 |1초 |1초 |
| 재시도 시간 제한 값 |1초 |1초 |

#### <a name="web-service-call-limits"></a>웹 서비스 호출 제한
Azure Resource Manager에는 API 호출 제한이 있습니다. [Azure 리소스 관리자 API 제한](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)범위 안의 속도로 API 호출을 수행할 수 있습니다.
