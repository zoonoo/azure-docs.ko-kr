---
title: Azure 사이트 복구 모니터링에 대한 일반적인 질문
description: 붙게 된 모니터링 및 Azure 모니터(로그 분석)를 사용하여 Azure 사이트 복구 모니터링에 대한 일반적인 질문에 대한 답변을 얻을 수 있습니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718263"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>사이트 복구 모니터링에 대한 일반적인 질문

이 문서에서는 붙들은 사이트 복구 모니터링 및 Azure 모니터(Log Analytics)를 사용하여 Azure [사이트 복구](site-recovery-overview.md)모니터링에 대한 일반적인 질문에 대해 답변합니다.

## <a name="general"></a>일반

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>RPO 값은 사용 가능한 최신 복구 지점과 어떻게 다른가요?

사이트 복구는 다중 단계의 비동기 프로세스를 사용하여 컴퓨터를 Azure에 복제합니다.

- 복제의 마지막 두 번째 단계에서 컴퓨터의 최근 변경 내용이 메타데이터와 함께 로그/캐시 스토리지 계정으로 복사됩니다.
- 이러한 변경 사항은 복구 가능한 지점을 식별하는 태그와 함께 대상 지역의 저장소 계정/관리 디스크에 기록됩니다.
- 이제 사이트 복구를 통해 컴퓨터에 대해 복구 가능한 지점을 생성할 수 있습니다.
- 이 시점에서 RPO는 지금까지 저장소 계정에 업로드된 변경 사항에 대해 충족되었습니다. 즉, 이 시점에서 컴퓨터 RPO는 복구 가능한 지점에 해당하는 타임스탬프에서 경과되는 시간과 같습니다.
- 이제 Site Recovery는 스토리지 계정에서 업로드된 데이터를 선택하고 해당 컴퓨터용으로 만든 복제본 디스크에 적용합니다.
- 그런 다음 Site Recovery는 복구 지점을 생성하고, 이 지점을 장애 조치(Failover) 시의 복구에 사용할 수 있게 합니다.
- 따라서 사용 가능한 최신 복구 지점은 이미 처리되어 복제본 디스크에 적용된 최신 복구 지점에 해당하는 타임스탬프를 나타냅니다.


복제 원본 컴퓨터 또는 온-프레미스 인프라 서버에서 잘못된 시스템 시간이 계산된 RPO 값을 왜곡합니다. 정확한 RPO 보고를 위해 시스템 클록이 모든 서버와 컴퓨터에서 정확한지 확인합니다.



## <a name="inbuilt-site-recovery-logging"></a>붙장된 사이트 복구 로깅


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>볼트 인프라 뷰의 VM 수가 복제된 항목에 표시된 총 수와 다른 이유는 무엇입니까?

자격 증명 모음 인프라 보기는 복제 시나리오에 따라 범위가 지정됩니다. 현재 선택된 복제 시나리오의 컴퓨터만 뷰의 개수에 포함됩니다. 또한 Azure에 복제되도록 구성된 VM만 계산됩니다. 컴퓨터 위에 장애가 있거나 온-프레미스 사이트로 다시 복제되는 컴퓨터는 뷰에 계산되지 않습니다.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Essentials에서 복제된 항목의 수가 대시보드에서 복제된 항목의 총 개수와 다른 이유는 무엇입니까?

초기 복제가 완료된 컴퓨터만 Essentials에 표시된 개수에 포함됩니다. 복제된 항목 합계에는 초기 복제가 현재 진행 중인 컴퓨터를 포함하여 볼트의 모든 컴퓨터가 포함됩니다.

## <a name="azure-monitor-logging"></a>Azure Monitor 로깅


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>사이트 복구가 Azure 모니터 로그에 진단 로그를 보내는 빈도는 얼마나 됩니까? 

- AzureSiteRecoverytionStats 및 AzureSiteRecoveryRecovery포인트는 15분마다 전송됩니다.  
- AzureSiteRecovery복제데이터업로드속도 및 AzureSiteRecoveryProtectedDiskDataChurn은 5분마다 전송됩니다. 
- AzureSiteRecoveryJobs는 작업의 트리거 및 완료시 전송됩니다.
- AzureSiteRecoveryEvents 이벤트가 생성될 때마다 전송됩니다. 
- AzureSiteRecovery복제항목은 환경이 변경될 때마다 전송됩니다. 일반적으로 데이터 새로 고침 시간은 변경 후 15분입니다. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Azure Monitor 로그에 데이터는 얼마나 오래 보관되어 있습니까? 

기본적으로 보존기간은 31일입니다. 로그 분석 작업 영역의 **사용 및 예상 비용** 섹션에서 기간을 늘릴 수 있습니다. 데이터 **보존을**클릭하고 범위를 선택합니다.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>진단 로그의 크기는 무엇입니까? 

일반적으로 로그의 크기는 15-20KB입니다. 


## <a name="next-steps"></a>다음 단계

[사이트 복구 붙그드 모니터링](site-recovery-monitor-and-troubleshoot.md)또는 [Azure 모니터를](monitor-log-analytics.md)사용하여 모니터링하는 방법에 대해 알아봅니다.


