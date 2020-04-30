---
title: Azure Site Recovery 모니터링에 대 한 일반적인 질문
description: 기본 제공 모니터링 및 Azure Monitor (Log Analytics)를 사용 하 여 Azure Site Recovery 모니터링에 대 한 일반적인 질문에 대 한 답변을 받으세요.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131155"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Site Recovery 모니터링에 대 한 일반적인 질문

이 문서에서는 기본 제공 Site Recovery 모니터링 및 Azure Monitor (Log Analytics)를 사용 하 여 Azure [Site Recovery](site-recovery-overview.md)모니터링에 대 한 일반적인 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>RPO 값은 사용 가능한 최신 복구 지점과 다른 방식으로 기록 되나요?

Site Recovery는 여러 단계의 비동기 프로세스를 사용 하 여 Azure에 컴퓨터를 복제 합니다.

- 복제의 마지막 두 번째 단계에서 컴퓨터의 최근 변경 내용이 메타데이터와 함께 로그/캐시 스토리지 계정으로 복사됩니다.
- 복구 가능한 지점을 식별 하는 태그와 함께 이러한 변경 내용은 대상 지역의 저장소 계정/관리 디스크에 기록 됩니다.
- 이제 컴퓨터에 대 한 복구 가능한 지점을 생성할 수 Site Recovery.
- 지금까지 저장소 계정에 업로드 된 변경 내용에 대해 RPO를 충족 했습니다. 즉,이 시점에서 컴퓨터 RPO는 복구 가능한 지점에 해당 하는 타임 스탬프에서 경과 된 시간에 해당 합니다.
- 이제 Site Recovery는 스토리지 계정에서 업로드된 데이터를 선택하고 해당 컴퓨터용으로 만든 복제본 디스크에 적용합니다.
- 그런 다음 Site Recovery는 복구 지점을 생성하고, 이 지점을 장애 조치(Failover) 시의 복구에 사용할 수 있게 합니다.
- 따라서 사용 가능한 최신 복구 지점은 이미 처리 되었으며 복제본 디스크에 적용 된 최신 복구 지점에 해당 하는 타임 스탬프를 나타냅니다.


복제 원본 컴퓨터 또는 온-프레미스 인프라 서버의 시스템 시간이 잘못 되 면 계산 된 RPO 값이 왜곡 됩니다. 정확한 RPO 보고를 위해 시스템 클록이 모든 서버와 컴퓨터에서 정확한지 확인합니다.



## <a name="inbuilt-site-recovery-logging"></a>내장 Site Recovery 로깅


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>자격 증명 모음 인프라 보기의 VM 수가 복제 된 항목에 표시 된 총 개수와 다른 이유는 무엇 인가요?

자격 증명 모음 인프라 보기는 복제 시나리오에 따라 범위가 지정됩니다. 현재 선택한 복제 시나리오의 컴퓨터만 보기의 수에 포함 됩니다. 또한 Azure에 복제되도록 구성된 VM만 계산됩니다. 장애 조치 (failover) 된 컴퓨터 또는 온-프레미스 사이트로 다시 복제 되는 컴퓨터는 뷰에서 계산 되지 않습니다.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Essentials의 복제 된 항목 수가 대시보드의 복제 된 항목의 총 수와 다른 이유는 무엇 인가요?

초기 복제가 완료 된 컴퓨터만 Essentials에 표시 되는 개수에 포함 됩니다. 복제 된 항목 합계에는 초기 복제가 현재 진행 중인 항목을 포함 하 여 자격 증명 모음에 있는 모든 컴퓨터가 포함 됩니다.

## <a name="azure-monitor-logging"></a>Azure Monitor 로깅


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Site Recovery에서 Azure Monitor 로그에 리소스 로그를 전송 하는 빈도는 얼마나 됩니까? 

- AzureSiteRecoveryReplicationStats 및 AzureSiteRecoveryRecoveryPoints는 15 분 마다 전송 됩니다.  
- AzureSiteRecoveryReplicationDataUploadRate 및 AzureSiteRecoveryProtectedDiskDataChurn는 5 분 마다 전송 됩니다. 
- AzureSiteRecoveryJobs는 작업을 시작 하 고 완료할 때 전송 됩니다.
- AzureSiteRecoveryEvents는 이벤트가 생성 될 때마다 전송 됩니다. 
- AzureSiteRecoveryReplicatedItems는 환경이 변경 될 때마다 전송 됩니다. 일반적으로 데이터 새로 고침 시간은 변경 후 15 분입니다. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Azure Monitor 로그에 데이터는 얼마나 오래 유지 되나요? 

기본적으로 보존은 31 일입니다. Log Analytics 작업 영역의 **사용량 및 예상 비용** 섹션에서 기간을 늘릴 수 있습니다. **데이터 보존**을 클릭 하 고 범위를 선택 합니다.

### <a name="whats-the-size-of-the-resource-logs"></a>리소스 로그의 크기는 어떻게 되나요? 

일반적으로 로그 크기는 15-20 KB입니다. 


## <a name="next-steps"></a>다음 단계

Site Recovery 기본 제공 [모니터링](site-recovery-monitor-and-troubleshoot.md)또는 [Azure Monitor](monitor-log-analytics.md)를 사용 하 여 모니터링 하는 방법에 대해 알아봅니다.


