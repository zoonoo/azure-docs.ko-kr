---
title: Azure SQL Database FAQ | Microsoft Docs
description: "클라우드 데이터베이스 및 Azure SQL 데이터베이스, Microsoft 관계형 데이터베이스 관리 시스템(RDBMS) 및 클라우드에서 서비스로 데이터베이스에 대해 고객이 궁금해하는 일반적인 질문에 대한 답변입니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sashan;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 0b53eb34293078ad2ce334583fee88a8c5ac5ccf
ms.lasthandoff: 04/20/2017


---
# <a name="sql-database-faq"></a>SQL 데이터베이스 FAQ

## <a name="what-is-the-current-version-of-sql-database"></a>SQL Database의 최신 버전은 무엇인가요?
SQL Database의 현재 버전은 V12입니다. 버전 V11은 만료되었습니다.

## <a name="what-is-the-sla-for-sql-database"></a>SQL Database에 대한 SLA란 무엇인가요?
고객의 단일 또는 탄력적인 기본, 표준 또는 프리미엄 Microsoft Azure SQL Database와 인터넷 게이트웨이 간의 연결이 99.99% 이상 작동하도록 보장합니다. 자세한 내용은 [SLA](http://azure.microsoft.com/support/legal/sla/)를 참조하세요.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>서버 관리자에 대한 암호를 재설정하려면 어떻게 하나요?
[Azure Portal](https://portal.azure.com)에서 **SQL Server**를 클릭하고, 목록에서 서버를 선택한 다음 **암호 재설정**을 클릭합니다.

## <a name="how-do-i-manage-databases-and-logins"></a>데이터베이스 및 로그인을 관리하려면 어떻게 해야 하나요?
[데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>권한이 부여된 IP 주소만 서버에 액세스할 수 있도록 허용하려면 어떻게 해야 하나요?
[방법: SQL 데이터베이스에서 방화벽 설정 구성](sql-database-configure-firewall-settings.md)을 참조하세요.

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>SQL 데이터베이스 사용량은 청구서에 어떻게 표시되나요?
SQL Database는 서비스 계층 + 단일 데이터베이스에 대한 성능 수준 또는 탄력적 풀당 eDTU에 따라 예측 가능한 시간당 요금이 청구됩니다. 실제 사용량은 시간별로 계산 및 비례 배분되므로 청구서에 시간의 일부가 표시될 수 있습니다. 예를 들어 데이터베이스가 한 달에 12시간 동안 있었다면 청구서에는 0.5일의 사용량이 표시됩니다. 또한 서비스 계층 + 성능 수준 및 풀당 eDTU가 청구서에 세부적으로 표시되어 각 해당 월에 사용한 데이터베이스 일수를 쉽게 확인할 수 있습니다.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>단일 데이터베이스가 1시간 미만 동안 활성화되거나 더 높은 서비스 계층을 1시간 미만 사용하는 경우 어떻게 되나요?
사용량이나 데이터베이스가 한 시간 미만 동안 활성화되었는지 여부에 관계없이, 그 시간에 적용된 최고 서비스 계층 + 성능 수준을 사용하여 데이터베이스가 있었던 각 시간에 대해 요금이 청구됩니다. 예를 들어 단일 데이터베이스를 만들고 5분 후 삭제하더라도 청구서에는 데이터베이스 1시간 사용에 대한 요금이 반영됩니다. 

예

* Basic 데이터베이스를 만든 후 즉시 Standard S1로 업그레이드한 경우에는 처음 1시간 사용에 대해 Standard S1 요금이 청구됩니다.
* 오후 10시에 Basic에서 Premium으로 데이터베이스를 업그레이드하고 다음 날 오전 1시 35분에 업그레이드가 완료되면 오전 1시 시작 시간부터 Premium 요금이 부과됩니다. 
* 오전 11시에 Premium에서 Basic으로 데이터베이스를 다운그레이드하여 오후 2시 15분에 완료되면 데이터베이스에는 Basic 요금이 부과된 후 오후 3시까지 Premium 요금이 부과됩니다.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>탄력적 풀 사용량은 청구서에 어떻게 표시되며 풀당 eDTU를 변경하면 어떻게 되나요?
탄력적 풀 요금은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)의 풀당 eDTU 아래 표시된 증분 단위로 탄력적 DTU(eDTU)로 청구서에 표시됩니다. 탄력적 풀에 대한 데이터베이스별 요금은 없습니다. 사용량이나 풀이 한 시간 미만 동안 활성화되었는지 여부에 관계없이, 최고 eDTU를 사용하여 풀이 있었던 각 시간에 대해 요금이 청구됩니다. 

예

* 오전 11시 18분에 5개의 데이터베이스를 풀에 추가하면서 200 eDTU로 Standard 탄력적 풀을 만든 경우 오전 11시부터 해당 일의 남은 시간 동안 200 eDTU에 대한 요금이 부과됩니다.
* 2일차의 오전 5시 5분에 데이터베이스 1은 50 eDTU 사용을 시작하고 그 날 동안 계속 유지합니다. 데이터베이스 2-5는 0과 80 eDTU 사이를 이동합니다. 이 날에는 하루 종일 다양한 eDTU를 사용하는 5개의 다른 데이터베이스를 추가합니다. 2일차는 200 eDTU에서 전일 요금이 청구됩니다. 
* 3일차 오전 5시에 다른 15개의 데이터베이스를 추가합니다. 데이터베이스 사용량이 종일 증가하여 오후 8시 5분에는 풀에 대한 eDTU를 200에서 400으로 증가하도록 결정하기에 이릅니다. 오후 8시까지 200 eDTU 수준으로 요금이 적용되고 남은 4시간에 대해서는 400 eDTU로 요금이 증가합니다. 

## <a name="elastic-pool-billing-and-pricing-information"></a>탄력적 풀 대금 청구 및 가격 정보
탄력적 풀은 다음 특성에 따라 대금이 청구됩니다.

* 탄력적 풀은 풀에 데이터베이스가 없더라도 생성 시부터 대금이 청구됩니다.
* 탄력적 풀은 시간당 대금이 청구됩니다. 이는 단일 데이터베이스의 성능 수준과 같은 계량 빈도입니다.
* 탄력적 풀이 새로운 크기의 eDTU로 조정된 경우, 크기 조정 작업이 완료될 때까지 새 eDTU 크기에 따른 대금이 청구되지 않습니다. 이는 단일 데이터베이스의 성능 수준을 변경하는 것과 동일한 패턴을 따릅니다.
* 탄력적 풀의 가격은 풀의 eDTU 수를 기준으로 책정됩니다. 탄력적 풀의 가격은 그 안의 탄력적 데이터베이스 사용률 및 수와 무관합니다.
* 가격은 (풀 eDTU 수)x(eDTU 당 단위 가격)으로 계산됩니다.

탄력적 풀의 단위 eDTU 가격은 동일한 서비스 계층에 있는 단일 데이터베이스의 단위 DTU 가격보다 높습니다. 자세한 내용은 [SQL 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 

eDTU 및 서비스 계층을 이해하려면 [SQL 데이터베이스 옵션 및 성능](sql-database-service-tiers.md)을 참조하세요.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>탄력적 풀에서 활성 지역 복제의 사용량은 청구서에 어떻게 표시되나요?
단일 데이터베이스와 달리, 탄력적 데이터베이스의 [활성 지역 복제](sql-database-geo-replication-overview.md) 를 사용해도 청구서에 직접적인 영향은 없습니다.  각 풀(기본 풀 및 보조 풀)에 프로비전된 eDTU에 대해서만 요금이 청구됩니다.

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>감사 기능 사용이 청구서 요금에 반영되는 방식
감사는 추가 비용 없이 SQL Database 서비스에 기본으로 제공되며 Basic, Standard, Premium 및 Premium RS 데이터베이스에서 사용할 수 있습니다. 하지만 감사 로그를 저장할 때 감사 기능은 Azure 저장소 계정을 사용하며 감사 로그의 크기에 따라 Azure 저장소의 테이블 및 큐에 대한 요금이 부과됩니다.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀에 적합한 서비스 계층과 성능 수준을 찾으려면 어떻게 해야 하나요?
몇 가지 도구를 사용할 수 있습니다. 

* 온-프레미스 데이터베이스의 경우 필요한 DTU 및 데이터베이스를 추천하는 [DTU 크기 조정 관리자](http://dtucalculator.azurewebsites.net/)를 사용하고 탄력적 풀에 대해 여러 데이터베이스를 평가합니다.
* 풀에 포함될 경우 유리한 단일 데이터베이스의 경우 Azure의 지능형 엔진이 사용 패턴 기록이 타당하다고 판단되는 경우 탄력적 풀을 권장합니다. [Azure Portal을 사용하여 탄력적 풀 모니터링 및 관리](sql-database-elastic-pool-manage-portal.md)를 참조하세요. 직접 계산을 수행하는 방법에 대한 자세한 내용은 [탄력적 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool.md)을 참조하세요.
* 단일 데이터베이스를 확장 또는 축소해야 할지 여부를 확인하려면 [단일 데이터베이스의 성능 지침](sql-database-performance-guidance.md)을 참조하세요.

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>단일 데이터베이스의 서비스 계층 또는 성능 수준은 얼마나 자주 변경할 수 있나요?
서비스 계층(Basic, Standard, Premium 및 Premium RS) 또는 서비스 계층 내 성능 수준(예: S1~S2)을 원하는 횟수만큼 변경할 수 있습니다. 이전 버전의 데이터베이스는 서비스 계층 또는 성능 수준을 24시간 동안 총 4번 변경할 수 있습니다.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>풀당 eDTU는 얼마나 자주 조정할 수 있나요?
원하는 횟수만큼 조정할 수 있습니다.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>단일 데이터베이스의 서비스 계층 또는 성능 수준을 변경하거나 탄력적 풀 내부 및 외부로 데이터베이스를 이동하는 데는 얼마나 시간이 소요되나요?
데이터베이스의 서비스 계층을 변경하고 풀 내부 및 외부로 이동하려면 플랫폼에서 백그라운드 작업으로 데이터베이스를 복사해야 합니다. 서비스 계층을 변경하는 데는 데이터베이스 크기에 따라 몇 분에서 몇 시간까지 소요될 수 있습니다. 두 경우 모두 데이터베이스는 온라인 상태이고 이동 중에도 사용할 수 있습니다. 단일 데이터베이스 변경에 대한 자세한 내용은 [데이터베이스의 서비스 계층 변경](sql-database-service-tiers.md)을 참조하세요. 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>탄력적 데이터베이스와 단일 데이터베이스는 언제 사용해야 하나요?
일반적으로 탄력적 풀은 고객 또는 테넌트당 하나의 데이터베이스가 있는 전형적인 [SaaS(Software-as-a-Service) 응용 프로그램 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 위해 설계되었습니다. 개별 데이터베이스를 만들고 각 데이터베이스의 가변적인 최대 수요를 충족하기 위해 과도하게 프로비저닝하는 것은 대체로 비용 효율적이지 않습니다. 풀을 사용하여 사용자는 풀의 집단 성능을 관리하고 데이터베이스는 자동으로 확장 및 축소됩니다. Azure의 지능형 엔진이 사용 패턴이 타당하다고 판단되는 경우 데이터베이스에 대한 풀을 추천합니다. 자세한 내용은 [탄력적 풀 지침](sql-database-elastic-pool.md)을 참조하세요.

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>최대 프로비전된 데이터베이스 저장소의 최대 200%가 백업 저장소로 제공된다는 것은 어떤 의미인가요?
백업 저장소는 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore) 및 [지역 복원](sql-database-recovery-using-backups.md#geo-restore)에 사용되는 자동 데이터베이스 백업과 관련된 저장소입니다. Microsoft Azure SQL 데이터베이스에서는 추가 비용 없이 최대 프로비전된 데이터베이스 저장소의 최대 200%까지 백업 저장소가 제공됩니다. 예를 들어, 프로비전된 DB의 크기가 250GB인 Standard DB 인스턴스가 있으면 추가 비용 없이 500GB의 백업 저장소가 제공됩니다. 데이터베이스가 제공된 백업 저장소를 초과하는 경우 Azure 지원에 문의하여 보존 기간을 줄이도록 선택하거나 표준 RA-GRS(읽기 액세스 지리 중복 저장소) 요금으로 청구되는 추가 백업 저장소에 대해 비용을 지불할 수 있습니다. RA-GRS 청구에 대한 자세한 내용은 저장소 가격 세부 정보를 참조하세요.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Web/Business에서 새 서비스 계층으로 옮기려고 합니다. 알아야 할 사항은 무엇인가요?
Azure SQL Web 및 Business 데이터베이스는 이제 사용되지 않습니다. Basic, Standard, Premium, Premium RS 및 Elastic 계층이 사용되지 않는 Web 및 Business 데이터베이스를 대체합니다. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>동일한 Azure 지리 내의 두 지역 간에 데이터베이스를 지역 복제하는 경우 예상되는 복제 지연은 무엇인가요?
현재 5초의 RPO를 지원하고 있으며 복제 지연은 지역 보조 데이터베이스가 Azure에서 권장하는 쌍을 이루는 지역에 호스트되고 동일한 서비스 계층인 경우보다 짧았습니다.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>지역 보조 데이터베이스가 주 데이터베이스와 동일한 지역에 만들어지는 경우 예상되는 복제 지연은 무엇인가요?
경험적 데이터를 보면 Azure에서 권장하는 쌍을 이루는 지역을 사용하는 경우 지역 내 및 지역 간 복제 지연 간에 그리 많은 차이는 없습니다. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>두 지역 간에 네트워크 오류가 있는 경우 지역에서 복제가 설정되었을 때 재시도 논리가 어떻게 작동하나요?
연결이 끊어지면 연결을 다시 설정하도록 10초마다 재시도합니다.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>주 데이터베이스의 중요한 변경 내용이 복제되도록 보장하려면 어떻게 해야 하나요?
지역 보조 데이터베이스는 비동기 복제본이며 주 데이터베이스와 전체 동기화를 유지하려고 하지 않습니다. 하지만 중요한 변경 내용(예: 암호 업데이트)이 복제되도록 하기 위해 강제 동기화 방법을 제공하고 있습니다. 강제 동기화는 모든 커밋된 트랜잭션이 복제될 때까지 호출 스레드를 차단하므로 성능에 영향을 주게 됩니다. 자세한 내용은 [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx)를 참조하세요. 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>주 데이터베이스와 지역 보조 데이터베이스 간의 복제 지연을 모니터링할 수 있는 도구는 무엇인가요?
DMV를 통해 주 데이터베이스와 지역 보조 데이터베이스 간의 실시간 복제 지연을 표시합니다. 자세한 내용은 [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx)를 참조하세요.

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>동일한 구독의 다른 서버에 데이터베이스를 이동하려면
* [Azure Portal](https://portal.azure.com)에서 **SQL Database**를 클릭하고 목록에서 데이터베이스를 선택한 후 **복사**를 클릭합니다. 자세한 내용은 [Azure SQL Database 복사](sql-database-copy.md) 를 참조하세요.

## <a name="to-move-a-database-between-subscriptions"></a>구독 간에 데이터베이스를 이동하려면
* [Azure Portal](https://portal.azure.com)에서 **SQL Server** 를 클릭하고, 목록에서 데이터베이스를 호스팅하는 서버를 선택합니다. **이동**을 클릭한 후 이동할 리소스와 이동 대상인 구독을 선택합니다.

