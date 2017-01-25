---
title: Azure Guidance | Microsoft Docs
description: "Azure에 대한 모범 사례 및 지침"
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Azure의 참고 자료
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Microsoft 패턴 및 작업 방식 팀은 Azure 고객 자문 팀에 속해 있습니다. Microsoft는 Microsoft Azure Platform에서 개발자, 설계자 및 IT 전문가가 성공적으로 작업하도록 돕는 것을 목표로 합니다. Azure에서 클라우드 솔루션을 구축하기 위한 모범 사례를 보여 주는 참고 자료를 작성하였습니다.

## <a name="checklists"></a>검사 목록
다음 목록은 가용성 및 확장성의 기본적인 측면을 검토하기 위한 빠른 참조입니다. 

* [가용성 검사 목록][AvailabilityChecklist] 
  
    가용성을 보장하기 위해 권장되는 작업 방식에 대한 요약입니다.
* [확장성 검사 목록][ScalabilityChecklist]
  
    확장성 있는 서비스를 설계 및 구현하고 데이터 관리를 처리하기 위해 권장되는 작업 방식에 대한 요약입니다.

## <a name="best-practices-articles"></a>모범 사례 문서
이러한 문서는 클라우드 컴퓨팅과 일반적으로 관련된 중요한 개념에 대해 자세히 설명합니다. 

* [API 디자인][APIDesign] 
  
    웹 API를 설계할 때 고려할 디자인 문제에 대한 설명입니다.
* [API 구현][APIImplementation] 
  
    웹 API를 구현 및 게시하기 위해 권장되는 작업 방식 집합입니다.
* [API 보안 지침](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    인증 및 권한 부여 개념(예: 토큰 형식, 권한 부여 프로토콜, 권한 부여 흐름 및 위협 완화)에 대해 설명합니다.
* [자동 크기 조정 지침][AutoscalingGuidance] 
  
    수동 개입이 필요 없는 자동 크기 조정 솔루션을 위한 고려 사항을 요약합니다.
* [백그라운드 작업 지침][BackgroundJobsGuidance] 
  
    포그라운드 또는 대화형 작업과 별개로, 백그라운드에서 수행해야 하는 작업을 구현하는 데 사용할 수 있는 옵션 및 권장되는 작업 방식에 대한 설명입니다.
* [콘텐츠 배달 네트워크(CDN) 지침][CDNGuidance] 
  
    CDN을 사용하여 응용 프로그램에 대한 부하를 최소화하고 가용성과 성능을 극대화하기 위한 일반적인 지침 및 권장되는 사례입니다.
* [캐싱 지침][CachingGuidance] 
  
    시스템의 성능 및 확장성을 개선하기 위해 캐싱을 사용하는 방법에 대한 요약입니다.
* [데이터 분할 지침][DataPartitioningGuidance]
  
    데이터 분할로 확장성을 향상시키고 경합을 줄여 성능을 최적화하는 방법에 대한 전략입니다.
* [모니터링 및 진단 지침][MonitoringandDiagnosticsGuidance] 
  
    사용자가 시스템을 활용하고 리소스 사용률을 추적하며, 시스템의 상태 및 성능을 전반적으로 모니터링하는 방법을 추적하는 지침입니다.
* [권장 명명 규칙][naming-conventions] 
  
    Azure 리소스에 대한 권장되는 명명 규칙.
* [재시도 일반 지침][RetryGeneralGuidance] 
  
    일시적인 오류 처리에 대한 일반적인 개념 설명입니다.
* [서비스 관련 재시도 지침][RetryServiceSpecificGuidance]
  
    수많은 Azure 서비스에 대한 재시도 기능을 요약하고 해당 서비스에 대한 재시도 메커니즘을 사용, 적용 또는 확장하는 데 도움이 되는 정보를 제공합니다.

## <a name="scenario-guides"></a>시나리오 가이드
* [Azure에서 Elasticsearch 실행][elasticsearch] 
  
    Elasticsearch는 확장성이 뛰어난 오픈 소스 검색 엔진 및 데이터베이스로서, 큰 데이터 집합에 저장된 정보를 신속하게 분석 및 검색해야 할 때 유용합니다. 이 참고 자료에서는 Elasticsearch 클러스터를 디자인할 때 고려해야 할 몇 가지 주요 측면을 살펴봅니다.
* [다중 테넌트 응용 프로그램에 대한 ID 관리][identity-multitenant] 
  
    다중 테넌트 지원은 여러 테넌트가 응용 프로그램을 공유하지만 서로 격리되어 있는 아키텍처입니다. 이 지침에서는 [Azure Active Directory][AzureAD]를 사용하여 로그인 및 인증을 처리하는 방식으로 다중 테넌트 응용 프로그램에서 사용자 ID를 관리하는 방법을 보여 줍니다.
* [빅 데이터 솔루션 개발](https://msdn.microsoft.com/library/dn749874.aspx)
  
    이 가이드에서는 데이터 웨어하우스로 반복적인 탐색 등의 시나리오에 HDInsight를 사용하고 ETL 프로세스의 경우 기존 BI 시스템으로 통합하는 내용을 살펴봅니다. 또한 빅 데이터 개념, 빅 데이터 솔루션 계획 및 설계, 이러한 솔루션 구현에 대한 개념을 이해하는 지침도 제공합니다.

## <a name="patterns"></a>패턴
* [클라우드 디자인 패턴: 클라우드 응용 프로그램에 대한 규범적 아키텍처 지침](https://msdn.microsoft.com/library/dn568099.aspx)
  
    클라우드 디자인 패턴은 디자인 패턴과 관련 지침 항목의 라이브러리입니다. 각 부분이 클라우드 응용 프로그램 아키텍처에 얼마나 적합한지를 보여 주어 패턴을 적용할 때의 이점을 설명합니다.
* [클라우드 응용 프로그램 성능 최적화](https://github.com/mspnp/performance-optimization)
  
    이 설명서는 부하 상태에서 앱이 크기 조정되지 않도록 하는 일반적인 안티패턴에 대해 살펴봅니다. 여기서는 8가지 안티패턴 및 [성능 분석 입문](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md)을 보여주는 샘플과 [주요 메트릭에 대해 성능을 평가](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md)하는 지침을 제공합니다.

## <a name="reference-architectures"></a>참조 아키텍처
참조 아키텍처는 시나리오별로 정렬되어 있습니다.
각 개별 아키텍처는 권장되는 작업 방식, 조치 단계 및 권장 사항을 구현하는 실행 가능한 구성 요소를 제공합니다.

참조 아키텍처의 현재 라이브러리는 [http://aka.ms/architecture](http://aka.ms/architecture)에서 제공됩니다.

## <a name="resiliency-guidance"></a>복원력 지침
이 항목에서는 분산된 클라우드 환경에서 오류에 대해 복원력 있는 응용 프로그램을 설계하는 방법을 설명합니다.   

* [복원력 개요][ResiliencyOvervew]
  
     Azure 플랫폼에서 오류에 대해 복구 가능하고 계속해서 작동할 수 있는 응용 프로그램을 구축하는 방법입니다. 설계부터 구현, 배포 및 작업에 이르기까지 복원력을 달성하는 구조화된 방법을 설명합니다.
* [복원력 검사 목록][resiliency-checklist]
  
    발생할 수 있는 다양한 오류 모드에 대해 설계할 수 있는 권장 사항에 대한 검사 목록입니다.
* [실패 모드 분석][resiliency-fma] 
  
    오류 모드 분석(FMA)은 가능한 오류 지점을 식별하여 시스템에 대한 복원력을 구축하는 프로세스입니다. 이 문서는 FMA 프로세스에 대한 시작점으로 활용할 수 있으며 잠재적인 오류 모드의 카탈로그 및 완화 방법을 포함합니다. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Jan17_HO3-->


